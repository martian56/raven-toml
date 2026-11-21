# raven-toml

[![CI](https://github.com/martian56/raven-toml/actions/workflows/ci.yml/badge.svg)](https://github.com/martian56/raven-toml/actions/workflows/ci.yml)

Reads a practical subset of TOML: `[tables]` (including dotted `[a.b]`),
`key = value` entries, `#` comments, and the scalar types (string, int, float,
bool) plus arrays of them. Values come back typed and are addressed by their
dotted path. No dependencies.

## Install

```toml
[dependencies]
"github.com/martian56/raven-toml" = "v0.2.0"
```

## Usage

```rust
from "github.com/martian56/raven-toml" import parse

let text = "[server]\nhost = \"0.0.0.0\"\nport = 8080\n"
match parse(text) {
    case Ok(t) {
        let host = t.get_string_or("server.host", "localhost")
        let port = t.get_int_or("server.port", 80)
    }
    case Err(e) { print(e.to_string()) }   // "toml:3: unterminated string"
}
```

Everything is addressed by a dotted path. A top-level key is just `"title"`, and
a key under `[server]` is `"server.host"`.

## Reading values

The getters come in two shapes. `get_string(key)` returns an `Option[str]`,
and `get_string_or(key, fallback)` gives you the value or a default. It's the
same for `int` and `bool` (and `get_float`, which has no `_or` form). A getter
returns nothing when the key is missing or holds a different type, so asking for
an int on a string key just hands back the default.

`get_array(key)` returns `Option[list[Value]]`, where `Value` is the enum the
parser produces (`Str`, `int`, `float`, `bool`, `Array`). If you'd rather match
on the raw value yourself, `get(key)` gives you the `Value` directly.

## What's supported

Tables and dotted tables, `key = value`, `#` comments (including after a value,
outside quotes), strings with `\n` `\t` `\r` escapes, and single-line arrays.
Strings have to be quoted, as in real TOML, so a bare word is an error.
Datetimes, inline tables, and arrays of tables are not handled.

## Requirements

Needs Raven 3.0.0 or newer.

## License

MIT
