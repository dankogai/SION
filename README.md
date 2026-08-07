# SION, a serialization format a little more expressive than JSON

This page introduces and describes SION, a data serialization format that addresses the shortcomings of [JSON], arguably the most popular data serialization format today. 

## Because JSON is not expressive enough

SION is a data serialization format:

* Named after "Swift Interchangeable Object Notation". As JSON is originated from a {ECMA,Java}Script literal, SION is originated from a [Swift] literal.
  * but like [JSON], it should not be considered acronym.  It is a textual data format of its own and independent of programming languages.
* It can serialize anything JSON can. Plus
  * support `Data` - binary blobs
  * support `Date`
* non-`String` keys in `Dictionary`
  * `Int` and `Double` distinctively, not `Number`.  
    * Therefore you can exchange 64-bit integers losslessly.
* No `{}` needed for `Dictionary` (aka Map, Hash, Object…).
  * `[` and `]` delimit both `Array` and `Dictionary`.  What makes a `Dictionary` a `Dictionary` is `:` between keys and values, not a different pair of brackets.
* `Double` can be expressed both in decimal and [C99 Hexadecimal floating-point] notation.
* `NaN` and ±`Infinity` are valid.
  * This is necessary for really interchangeable format.  Suppose you want to send calculation results with errors.
* // comment is allowed!.  `//` up to newline.
  * The lack of comment support of JSON definitely makes it suck as a configuration file format.
* Roughly equivalent to [MsgPack] in terms of capability.
  * [MsgPack] is a binary serialization while `SION` is a text serialization.

[JSON]: https://json.org
[Swift]: https://swift.org
[MsgPack]: https://msgpack.org
[C99 Hexadecimal floating-point]: https://en.wikipedia.org/wiki/C99#IEEE_754_floating_point_support

Below is a table of a few notable serialization formats and capabilities.

| Type | SION | MsgPack | JSON | Property List | Comment |
|--------|---------------|-------|---|---|---|
| `Nil`           | ✔︎ | ✔︎ | ✔︎ | ❌ | plist: .binary only |
| `Bool`          | ✔︎ | ✔︎ | ✔︎ | ✔︎ |
| `Int`           | ✔︎ | ✔︎ | ❌ | ✔︎ | 64bit |
| `Double`        | ✔︎ | ✔︎ | ✔︎ | ✔︎ | JSON's Number |
| `String`        | ✔︎ | ✔︎ | ✔︎ | ✔︎ | utf-8 encoded |
| `Data`          | ✔︎ | ✔︎ | ❌ | ✔︎ | binary blob |
| `Date`          | ✔︎ | ✔︎ | ❌ | ✔︎ | .timeIntervalSince1970 in `Double` |
| `[Self]`        | ✔︎ | ✔︎ | ✔︎ | ✔︎ | aka Array |
| `[String:Self]` | ✔︎ | ✔︎ | ✔︎ | ✔︎ | aka Object, Map…|
| `[Self:Self]`   | ✔︎ | ✔︎ | ❌ | ❌ |non-`String` keys|
| `Ext`           | ✔︎ | ✔︎ | ❌ | ❌ |msgpack extension|

## One bracket to rule them all

Unlike JSON, SION needs no `{}` for `Dictionary` (aka Map, Hash, Object…).  Both `Array` and `Dictionary` are delimited by `[` and `]`.  They are distinguished not by brackets but by what is inside — `key : value` pairs make it a `Dictionary`:

```swift
[ "zero", "one", "two" ]        // an Array
[ 0:"zero", 1:"one", 2:"two" ]  // a Dictionary
[]                              // an empty Array
[:]                             // an empty Dictionary
```

This is not just aesthetics:

* **Simpler syntax**.  One kind of bracket for collections means fewer tokens, fewer delimiter-mismatch errors, and a smaller grammar.
* **Collections are one concept**.  A `Dictionary` is just a collection whose elements are keyed — `:` is what says so.  An `Array` is arguably a `Dictionary` implicitly keyed by 0, 1, 2… as a matter of fact `["zero", "one", "two"]` and `[0:"zero", 1:"one", 2:"two"]` are semantically equivalent.
* **A natural consequence of non-`String` keys**.  JSON's `{}` carries the implicit promise that keys are strings.  Once any value can be a key, that distinction loses its reason to exist.

The only price is `[:]`, the empty `Dictionary`, since `[]` is taken by the empty `Array`.  A fair trade for a whole class of brackets.

## Implementations

* Swift
  * [swift-sion] by [dankogai]
* Go
  * [go-sion] by [mattn]
* ECMAScript (aka JavaScript)
  * [js-sion] by [dankogai]
* Rust
  * [sion-rs] by [pandaman64]
* Python 3
  * [py-sion] by [kamimura]
* Perl 5
  * [p5-sion] by [dankogai]

[dankogai]: https://github.com/dankogai/
[mattn]: https://github.com/mattn/
[pandaman64]: https://github.com/pandaman64/
[kamimura]: https://github.com/kamimura/
[swift-sion]: https://github.com/dankogai/swift-sion
[go-sion]: https://github.com/mattn/go-sion
[js-sion]: https://github.com/dankogai/js-sion
[sion-rs]: https://github.com/pandaman64/sion-rs
[py-sion]: https://github.com/kamimura/py-sion
[p5-sion]: https://github.com/dankogai/p5-sion

## Syntax

* ANTLR4(EBNF)
  * [sion-antlr4] by [kamimura]

[sion-antlr4]: https://github.com/kamimura/sion-antlr4

## Examples

Below is an example of data encoded in SION.

```swift
[
    "array": [
        nil,
        true,
        1,      // Int in decimal
        1.0,    // Double in decimal
        "one",
        [1],
        ["one" : 1.0]
    ],
    "bool": true,
    "data": .Data("R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7"),
    "date": .Date(0x0p+0),
    "dictionary": [
        "array" : [],
        "bool" : false,
        "double" : 0.0,
        "int" : 0,
        "nil" : nil,
        "object" : [:],
        "string" : ""
    ],
    "double": 0x1.518f5c28f5c29p+5, // double in hex
    "ext": .Ext("1NTU"),            // 0xd4,0xd4,0xd4
    "int": -42,                     // Int in decimal
    "nil": nil,
    "string": "漢字、カタカナ、ひらがなの入ったstring😇",
    "url": "https://github.com/dankogai/"
]
```

As you notice,

* comments are allowed - `//` up to newline.
* non-`String` keys are allowed.  Any data conforming to SION can be a {Dictionary,Map,Object} key.
* no `{}` in sight.  The whole document is a `Dictionary`, delimited by `[` and `]` like everything else.

And below is an example of JSON-compatible SION…

```swift
[
    "array" : [
        nil,
        true,
        1,    // Int in decimal
        1.0,  // Double in decimal
        "one",
        [1],
        ["one" : 1.0]
    ],
    "bool" : true,
    "dictionary" : [
        "array" : [],
        "bool" : false,
        "double" : 0.0,
        "int" : 0,
        "nil" : nil,
        "object" : [:],
        "string" : ""
    ],
    "double" : 42.195,
    "int" : -42,
    "nil" : nil,
    "string" : "漢字、カタカナ、ひらがなの入ったstring😇",
    "url" : "https://github.com/dankogai/"
]
```

…which turns into a JSON below.

```javascript
{
    "array" : [
        null,
        true,
        1,
        1.0,
        "one",
        [1],
        {"one": 1.0}
    ],
    "bool" : true,
    "dictionary": {
        "array": [],
        "bool": false,
        "double": 0.0,
        "int": 0,
        "nil": null,
        "object": {},
        "string": ""
    },
    "double": 42.195,
    "int": -42,
    "nil": null,
    "string": "漢字、カタカナ、ひらがなの入ったstring😇",
    "url": "https://github.com/dankogai/"
}
```

Note the `{}`s are back, along with the comments gone.
