[![CI via GitHub Actions](https://github.com/dankogai/p5-sion/actions/workflows/ci.yml/badge.svg)](https://github.com/dankogai/p5-sion/actions/workflows/ci.yml)

# p5-sion

[SION], a serialization format a little more expressive than JSON,
serializer/deserializer for Perl 5, with an interface modeled after
[JSON::PP].

[SION]: https://dankogai.github.io/SION/
[JSON::PP]: https://metacpan.org/pod/JSON::PP

## Synopsis

```perl
use SION;    # exports encode_sion() and decode_sion()

# functional interface: SION text is UTF-8 octets
my $data = decode_sion($sion_text);
my $sion = encode_sion($data);

# OO interface, a la JSON::PP
my $codec = SION->new->utf8->canonical->pretty;
$sion = $codec->encode($data);
$data = $codec->decode($sion);
```

## SION?

```swift
[
    "nil":      nil,                // nil is allowed
    "bool":     true,
    "int":      -42,               // Int is distinguished from Double
    "double":   0x1.518f5c28f5c29p+5,   // C99 hexadecimal notation
    "string":   "漢字、カタカナ、ひらがなの入ったstring😇",
    "array":    [nil, true, 1, 1.0, "one", [1], ["one":1.0]],
    "dictionary": ["nil":nil, "array":[], "object":[:]],
    "data":     .Data("R0lGODlh"), // binary data in Base64
    "date":     .Date(0x0p+0),     // date in seconds since epoch
    1:          "non-string keys are also allowed", // comments, too!
]
```

## Mapping

| SION       | Perl                                          |
|------------|-----------------------------------------------|
| nil        | `undef`                                       |
| Bool       | `JSON::PP::true` / `JSON::PP::false`          |
| Int        | integer (IV)                                  |
| Double     | floating point number (NV)                    |
| String     | character string (decoded UTF-8 string)       |
| Data       | byte string (raw octets); `SION::Data` to force |
| Date       | `SION::Date` object (`->epoch`)               |
| Ext        | `SION::Ext` object                            |
| Array      | ARRAY reference                               |
| Dictionary | HASH reference                                |

* Booleans are handled exactly like JSON::PP; native booleans (`!!1`)
  are also supported on perl 5.36+.
* Like JSON::PP, `1` encodes as Int `1` while `1.0` encodes as Double
  `0x1p+0` — Doubles are emitted in lossless C99 hexadecimal notation
  and the Int/Double distinction survives round trips.
* Strings are utf-8 strings, Data are byte strings: a scalar that is
  not valid UTF-8 encodes as `.Data("…")`; `.Data("…")` decodes to a
  plain byte string.
* Any object with an `epoch` method (Time::Piece, DateTime, …) encodes
  as `.Date(…)`.

See `perldoc SION` for the full documentation.

## Installation

```
perl Makefile.PL
make
make test
make install
```

Requires perl 5.22 or later (for hexadecimal floating point support).

## See also

* [SION specification](https://dankogai.github.io/SION/)
* [swift-sion](https://github.com/dankogai/swift-sion)
* [js-sion](https://github.com/dankogai/js-sion)

## License

Copyright (c) 2026 Dan Kogai. This software is licensed under
The Artistic License 2.0 (GPL Compatible).
