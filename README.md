# Taggart

[![Hex.pm](https://img.shields.io/hexpm/v/taggart.svg)](https://hex.pm/packages/taggart)
[![Build Docs](https://img.shields.io/badge/hexdocs-release-blue.svg)](https://hexdocs.pm/taggart/Taggart.html)
[![Build Status](https://travis-ci.org/ijcd/taggart.svg?branch=master)](https://travis-ci.org/ijcd/taggart)

Taggart is a generation library for tag-based markup (HTML, XML, SGML,
etc.). It is useful for times when you just want code and functions, not
templates. We already have great composition and abstraction tools in
Elixir. Why not use them? With this approach, template coposition through
smaller component functions should be easy.

## Installation

The package can be installed by adding `taggart` to your list of
dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:taggart, "~> 0.1.0"}
  ]
end
```

## Usage

Taggart produce Phoenix-compatible "safe" html through underlying usage of the
[`content_tag/2`](https://hexdocs.pm/phoenix_html/Phoenix.HTML.Tag.html#content_tag/2)
from Phoenix.HTML. Since it just produces IO Lists, it should remain
compatible with any other library that uses the same format.

Taggart supports a number of different syntaxes:

```
use Taggart

div("Name")

div("Name", class: "bold")

div(class: "bold", do: "Name")

div do
end

div(class: "bold") do
  "Name"
end
```

You can nest and combine in expected ways:

```
use Taggart

name = "Susan"
age = 27

html do
  body do
    div do
      h2 "Buyer"
      p name, class: "name"
      p age, class: "age"
    end
    div do
      "Welcome"
    end
  end
end
```

If you want an IO List without creating a top-level wrapping tag (useful for Phoenix forms):

```
use Taggart

taggart do
  div()
  span()
  div()
end
```

You can embed Taggart inside Phoenix helpers:

```
use Taggart

form = form_for(conn, "/users", [as: :user], fn f ->
  taggart do
    label do
      "Name:"
    end
    label do
      "Age:"
    end
    submit("Submit")
  end
end)
```

And you can embed Phoenix helpers inside Taggart:

```
use Taggart

html do
  body do
    div do
      h3 "Person"
      p name, class: "name"
      p 2 * 19, class: "age"
      form_for(build_conn(), "/users", [as: :user], fn f ->
        taggart do
          label do
            "Name:"
            text_input(f, :name)
          end
          label do
            "Age:"
            select(f, :age, 18..100)
          end
          submit("Submit")
        end
      end)
    end
  end
end
```


## Design

The design had two basic requirements:

1. Simple Elixir-based generation of tag-based markup.
2. Interoperate properly with Phoenix helpers.

I looked at and tried a few similar libraries (Eml, Marker), but
either wasn't able to get them to work with Phoenix helpers or had
problems with their approach (usage of @tag syntax in templates where
it didn't refer to a module attribute). My goal was to keep things
simple.

## License

Taggard is released under the Apache License, Version 2.0.