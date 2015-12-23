# Writing Documentation

Elixir treats documentation as a first-class citizen. This means documentation should be easy to write and easy to read. In this document you will learn how to write documentation in Elixir, covering constructs like module attributes, style practices and doctests.

## Markdown

Elixir documentation is written using Markdown. There are plenty of guides on Markdown online, we recommend the ones available at GitHub as a getting started point:

  * https://help.github.com/articles/markdown-basics/
  * https://help.github.com/articles/github-flavored-markdown/

## Module Attributes

Documentation in Elixir is usually attached to module attributes. Let's see an example:

    defmodule MyApp.Hello do
      @moduledoc """
      This is the Hello module.
      """

      @doc """
      Says hello to the given `name`.

      Returns `:ok`.

      ## Examples

          iex> MyApp.Hello.world(:john)
          :ok

      """
      def world(name) do
        IO.puts "hello #{name}"
      end
    end

The `@moduledoc` attribute is used to attach documentation to the module. `@doc` is used before a function to attach documentation to it. Besides the attributes above, `@typedoc` can also be used to attach documentation to types defined as part of typespecs.

## Recommendations

There are a couple tips we recommend developers to follow when writing documentation:

  * Keep the first paragraph of the documentation concise and simple. Tools like ExDoc uses the first line to generate a summary.

  * Markdown uses backticks (`` ` ``) to quote code. Elixir builds on top of that to automatically generate links when modules or function names are referenced. For this reason, always use full module names. If you have a module called `MyApp.Hello`, always reference it as `` `MyApp.Hello` `` and never only as `` `Hello` ``. Function names must be references by name and arity if they are local, as in `` `world/1` ``, or by module, name and arity if pointing to an external module: `` `MyApp.Hello.world/1` ``.

  * If using headings, always start from the second heading by using `##`. The first heading is reserved to the module or function name itself.

## Doctests

We recommend developers to include examples in their documentation, often under its own `## Examples` heading. To ensure examples do not get out of date, Elixir's test framework (ExUnit) provides a feature called doctests that allows developers to test the examples in their documentation. Doctests work by parsing out code samples starting with `iex>` from the documentation. You can read more about it at `ExUnit.DocTest`.

Notice doctests have limitations. When you cannot doctest a function, because it relies on state or side-effects, we recommend developers to include examples directly without the `iex>` prompt.

## Privacy

Elixir allows developers to hide both modules and functions from the documentation by setting the doc attribute to false:

    defmodule MyApp.Hidden do
      @moduledoc false

      def this_will_be_ignored_by_tools do
        # ...
      end
    end

Notice that, although developers can add `@doc false` to functions, it does not make the function private:

    defmodule MyApp.Sample do
      @doc false
      def add(a, b), do: a + b
    end

The function above can still be invoked as `MyApp.Sample.add(1, 2)`. Not only that, if the `MyApp.Sample` is imported, the `add/2` function will also be imported into the caller. For those reasons, be wary when adding `@doc false` to functions, instead prefer one of:

  * Move the private function to a module with `@moduledoc false`, like `MyApp.Hidden`, ensuring the function won't be accidentally exposed or imported. In fact, you can use `@moduledoc false` to hide a whole module and still document each function with `@doc`. Tools will still ignore the module.

  * Start the function name with underscores, for example, `__add__/2`, and add `@doc false`. The compiler does not import functions with underscore and the underscore will tell users to be wary of using it.

## Documentation != Comments

Elixir makes the difference between documentation and code comments. Documentation are for users of your API, be it your co-worker or your future self. Modules and functions must always be documented if they are part of your application public interface (API).

Code comments are for developers reading the code. They are useful to mark improvements, leave notes for developers reading the code (for example, you decided to not call a function due to a bug in a library) and so forth.

In other words, documentation is required, code comments are optional.

## Code.get_docs/2

Elixir stores documentation inside pre-defined chunks in the bytecode. It can be accessed from Elixir by using the `Code.get_docs/2` function. This also means documentation is only accessed when required and not when modules are loaded by the Virtual Machine. The only downside is that modules defined in-memory, like the ones defined via IEx, cannot have their documentation accessed.