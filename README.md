## Because JSON is not expressive enough

SION is a data serialization format:

* Named after "Swift Interchangeable Object Notation"
  * but like [JSON], it is not an acronym.  It is a textual data format of its own and not strictly tied to [Swift].
* As JSON is originated from a {ECMA,Java}Script literal, SION is originated from a Swift literal.
* It can serialize anything JSON can. Plus
  * support `Data` - binary blobs
  * support `Date`
* non-`String` keys in `Dictionary`
  * `Int` and `Double` distinctively, not `Number`.  
    * Therefore you can exchange 64-bit integers losslessly.
* // comment is allowed!.  `//` up to newline.
* `Double` includes `NaN` and ±`Infinity`.
* Roughly equvalent to [MsgPack] in terms of capability.
  * [MsgPack] is a binary serialization while `SION` is a text serialization.

[JSON]: https://json.org
[Swift]: https://swift.org
[MsgPack]: https://msgpack.org

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
* ECMAScript (aka JavaScript)
  * [js-sion]

[swift-sion]: https://github.com/dankogai/swift-jsion
[js-sion]: https://github.com/dankogai/swift-jsion
