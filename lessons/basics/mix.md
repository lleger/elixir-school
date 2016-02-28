---
layout: page
title: Mix
category: basics
order: 9
lang: en
---

Before we can dive into the deeper waters of Elixir we first need to learn about Mix. If you're familiar with Ruby, Mix is Bundler, RubyGems, and Rake combined. It's a crucial part of any Elixir project and in this lesson we're going to explore just a few of its great features. To see all that Mix has to offer run `mix help`.

Until now we've been working exclusively within `iex`, which has limitations.  In order to build something substantial we need to divide our code up into many files to effectively manage it, and Mix lets us do that with projects.

## Table of Contents

- [New Projects](#new-project)
- [Compilation](#compilation)
- [Interactive](#interactive)
- [Manage Dependencies](#manage-dependencies)
- [Environments](#environments)

## New Projects

When we're ready to create a new Elixir project, Mix makes it easy with the `mix new` command.  This will generate our project's folder structure and necessary boilerplate.  This is pretty straight forward, so let's get started:

```bash
$ mix new example
```

From the output we can see that mix has created our directory and a number of boilerplate files:

```bash
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/example.ex
* creating test
* creating test/test_helper.exs
* creating test/example_test.exs
```

In this lesson we're going to focus our attention on `mix.exs`.  Here we configure our application, dependencies, environment, and version.  Open the file in your favorite editor. You should see something like this (comments removed for brevity):

```elixir
defmodule Example.Mixfile do
  use Mix.Project

  def project do
    [app: :example,
     version: "0.0.1",
     elixir: "~> 1.0",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end

  def application do
    [applications: [:logger]]
  end

  defp deps do
    []
  end
end
```

The first section we'll look at is `project`.  Here we define the name of our application (`app`), specify our version (`version`), Elixir version (`elixir`), and finally our dependencies (`deps`).

The `application` section is used during the generation of our application file which we'll cover next.

## Compilation

Mix is smart and will compile your changes when necessary, but it may still be necessary to explicitly compile your project.  In this section we'll cover how to compile our project and what compilation does.

To compile a Mix project we only need to run `mix compile` in our base directory:

```bash
$ mix compile
```

There isn't much to our project so the output isn't too exciting but it should complete successfully:

```bash
Compiled lib/example.ex
Generated example app
```

When we compile a project Mix creates a `_build` directory for our artifacts.  If we look inside `_build` we will see our compiled application: `example.app`.

## Interactive

It may be necessary to use `iex` within the context of our application.  Thankfully, Mix makes this easy.  With our application compiled we can start a new `iex` session:

```bash
$ iex -S mix
```

Starting `iex` this way will loads your application and dependencies into the current runtime.

## Manage Dependencies

Our project doesn't have any dependencies but will shortly, so we'll go ahead and cover defining dependencies and fetching them.

To add a new dependency we need to first add it to our `mix.exs` in the `deps` section.  Our dependency list is comprised of tuples with two required values and one optional: the package name as an atom, the version string, and other options.

For this example let's look at a project with dependencies, like [`phoenix_slim`](https://github.com/doomspork/phoenix_slim):

```elixir
def deps do
  [{:phoenix, "~> 0.16"},
   {:phoenix_html, "~> 2.1"},
   {:cowboy, "~> 1.0", only: [:dev, :test]},
   {:slim_fast, ">= 0.6.0"}]
end
```

As you probably discerned from the dependencies above, the `cowboy` dependency is only necessary during development and test.

Once we've defined our dependencies there is one final step, fetching them.  This is analogous to `bundle install`:

```bash
$ mix deps.get
```

That's it!  We've defined and fetched our project dependencies.  Now we're prepared to add dependencies when the time comes.

## Environments

Mix, much like Bundler, supports multiple environments.  Out of the box it works with three environments:

+ `:dev` — The default environment.
+ `:test` — Used by `mix test`. Covered further in our next lesson.
+ `:prod` — Used when we ship our application to production.

The current environment can be accessed using `Mix.env`.  As expected, the environment can be changed via the `MIX_ENV` environment variable:

```bash
$ MIX_ENV=prod mix compile
```
