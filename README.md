# SampleFuture

Parallel map sample code using the following future library.
  - https://github.com/eproxus/future

```Elixir
defmodule SampleFuture do
  require Future

  def fib(0), do: 0
  def fib(1), do: 1
  def fib(n), do: fib(n - 1) + fib(n - 2)

  defmacro benchmark([do: content]) do
    quote do
      s = :erlang.now
      unquote(content)
      e = :erlang.now
      IO.inspect :timer.now_diff(e, s) / (1000 * 1000)
    end
  end

  def parallel_map(collection) do
    collection
      |> Enum.map(Future.new(&fib/1))
      |> Enum.map(&Future.value(&1))
  end

  def normal_map(collection) do
    collection
      |> Enum.map(&fib/1)
  end

  def run do
    collection = [35, 36, 37, 38]

    benchmark do
      IO.inspect parallel_map(collection)
    end

    benchmark do
      IO.inspect normal_map(collection)
    end
  end
end
```

## Execution
### Prepare
```
$ git clone https://github.com/parroty/sample_future
$ cd sample_future
$ mix deps.get
```

### Run
```
mix run -e "SampleFuture.run"
[9227465, 14930352, 24157817, 39088169]
4.438063
[9227465, 14930352, 24157817, 39088169]
7.567721
```