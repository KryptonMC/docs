# Commands

Commands are one of the core fundamentals for plugins. They are mostly what allow you to get your plugin to do things.

In Krypton, commands are structured very similarly to BungeeCord's commands, though they are different in that they run on their own asynchronous coroutine scope internally, called the command scope.

You can use them something like this:

```kotlin
class MyCommand : Command(name = "test", permission = "test.permission", aliases = setOf("hello", "world")) {

    override suspend fun execute(sender: Sender, args: List<String>) {
        sender.sendMessage(Component.text("Hello there! This is a test command!"))
    }
}
```

There really isn't all that much else to it than that, other than that you may optionally also override `suggest` to provide command suggestions.

That works something like this:

```kotlin
override fun suggest(sender: Sender, args: List<String>): List<String> {
    return listOf("hello", "world")
}
```

