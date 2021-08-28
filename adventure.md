# Adventure

Adventure is, quoting from the [README](https://github.com/KyoriPowered/adventure),
"A serverside user interface library for Minecraft: Java Edition". Krypton is what is known by Adventure as a
["platform"](https://docs.adventure.kyori.net/platform/index.html), and supports it completely natively, meaning
all the features that Adventure provides are fully supported.

Find out more information about what Adventure actually provides on their [wiki](https://docs.adventure.kyori.net).

## Extensions

Krypton, being a mostly Kotlin API, provides convenience extensions to the Adventure API to help to work with
it effectively. These extensions include conversions to and from various serializable formats.

Note: These extensions are for **Kotlin users only**, they are hidden from Java. The mappings for methods to use from
Java for the format extensions have been provided, and the flattening extensions are not necessary in Java.

### Formats

There are three formats that Krypton provides extension functions for serialization to (and sometimes from):
* JSON (text and tree format)
* Legacy text
* Plain text

The first can be done either with `toJson`, which takes a `Component` as its receiver and, using the
`GsonComponentSerializer`, converts the receiver to a `JsonElement` representation of it, or it can be done with
`toJsonString`, which, again, uses the `GsonComponentSerializer`, instead this time directly converting to a string.
There is also a `toComponent` extension for `JsonElement` that allows it to be deserialized in to a `Component` using
the `GsonComponentSerializer`.

The second can be done either with `toLegacyText`, which takes a `char` argument, which is the legacy character that
should be used to translate the text, and converts the receiver `Component` to legacy text, using the 
`LegacyComponentSerializer`, or with either `toLegacySectionText` or `toLegacyAmpersandText`, to convert the receiver
`Component` to legacy text using the section sign and ampersand respectively, also using the `LegacyComponentSerializer`.

The final format can be converted to using the `toPlainText` function, which converts the receiver `Component` in to
plain text without any formatting, using the `PlainTextComponentSerializer`.

Mappings for methods to use in Java instead of these extensions:
* `Component.toJson()` -> `GsonComponentSerializer.gson().serializeToTree(Component)`
* `JsonElement.toComponent()` -> `GsonComponentSerializer.gson().deserializeFromTree(JsonElement)`
* `Component.toJsonString()` -> `GsonComponentSerializer.gson().serialize(Component)`
* `Component.toLegacyText(Char)` -> `LegacyComponentSerializer.legacy(char).serialize(Component)`
* `Component.toLegacySectionText()` -> `LegacyComponentSerializer.legacySection().serialize(Component)`
* `Component.toLegacyAmpersandText()` -> `LegacyComponentSerializer.legacyAmpersand().serialize(Component)`
* `Component.toPlainText()` -> `PlainTextComponentSerializer.plainText().serialize(Component)`

### Flattening

In addition, two extensions are provided to `ComponentFlattener.Builder`, which use inlining and reification to
avoid a `Class` argument on both `mapper` and `complexMapper`, instead allowing you to provide them with arguments
in the same way you would provide a standard generic argument.

If the words "inlining" and "reification" confuse you at all, check out the
[official Kotlin documentation's page](https://kotlinlang.org/docs/inline-functions.html#reified-type-parameters)
for an explanation on what they are and how they work.

## Brigadier

Brigadier is a library maintained by Mojang that handles command parsing and dispatching in Krypton. More on how
it works can be found on the [Commands](commands.md) chapter of this wiki.

Anyway, this library adds in an interface called `Message` that can be used to define messages to send as a result
of exceptions that you may throw as part of the library, more on that later.

Now, in vanilla Minecraft, the `Component` directly implements this interface, however Adventure does not, thus an
`AdventureMessage` wrapper class exists to service this purpose.

This class can either be instantiated directly (recommended for Java users) with the constructor that takes a
`Component`, or created from an extension function (recommended for Kotlin users) called `toMessage` that may be
called with `Component` as its receiver.
