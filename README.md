## Because JSON is not expressive enough

SION is a data serialization format:

* Named after "Swift Interchangeable Object Notation". As JSON is originated from a {ECMA,Java}Script literal, SION is originated from a [Swift[ literal.
  * but like [JSON], it should not be considered acronym.  It is a textual data format of its own and independent of programming languages.
* It can serialize anything JSON can. Plus
  * support `Data` - binary blobs
  * support `Date`
* non-`String` keys in `Dictionary`
  * `Int` and `Double` distinctively, not `Number`.  
    * Therefore you can exchange 64-bit integers losslessly.
* `Double` can be expressed both in decimal and [C99 Hexadecimal floating-point] notation.
* `NaN` and ±`Infinity` are valid.
  * This is necessary for really interchangeable format.  Suppose you want to send calculation results with errors.
* // comment is allowed!.  `//` up to newline.
  * The lack of comment support of JSON definitely make it suck as a configuration file format.
* Roughly equvalent to [MsgPack] in terms of capability.
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

## Implementations

* Swift
  * [swift-sion]
* Go
  * [go-sion]
* ECMAScript (aka JavaScript)
  * [js-sion]

[swift-sion]: https://github.com/dankogai/swift-sion
[go-sion]: https://github.com/mattn/go-sion
[js-sion]: https://github.com/dankogai/swift-sion

## Examples

Below is an exmaple of data encoded in SION.

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
    "data" : .Data("R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7"),
    "date" : .Date(0x0p+0),
    "dictionary" : [
        "array" : [],
        "bool" : false,
        "double" : 0x0p+0,
        "int" : 0,
        "nil" : nil,
        "object" : [:],
        "string" : ""
    ],
    "double" : 0x1.518f5c28f5c29p+5, // Double in hexadecimal
    "int" : -0x2a, // Int in hexadecimal
    "nil" : nil,
    "string" : "漢字、カタカナ、ひらがなの入ったstring😇",
    "url" : "https://github.com/dankogai/",
    nil   : "Unlike JSON and Property Lists,",
    true  : "Yes, SION",
    1     : "does accept",
    1.0   : "non-String keys.",
    []    : "like",
    [:]   : "Map of ECMAScript."
]
```

As you notice,

* comments are allowed - `//` up to newline.
* non-`String` keys are allowed.  Any data conforming to SION can be a {Dictionary,Map,Object} key.

And below is an example of JSON-compatible SION…

```swift
[
    "array" : [
        nil,
        true,
        1,
        1.0,
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

…which turns into a JSON blow.

```javascript
{
    "array" : [
        nil,
        true,
        1,    // Int in decimal
        1.0,  // Double in decimal
        "one",
        [1],
        {"one": 1.0}
    ],
    "bool" : true,
    "dictionary": [
        "array": [],
        "bool": false,
        "double": 0.0,
        "int": 0,
        "nil": null,
        "object": {},
        "string": ""
    ],
    "double": 42.195,
    "int": -42,
    "nil": nil,
    "string": "漢字、カタカナ、ひらがなの入ったstring😇",
    "url": "https://github.com/dankogai/"
}
```
