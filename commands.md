# Commands

The command system is how command senders (such as players) can give instructions to the server, such as summoning
entities, teleporting, or even making others server operators.

The API is mainly split into two parts: executors and metadata. Executors define how to process a command when it is
issued by a command sender, and metadata defines information that distinguishes when the executor should be called to
handle the execution of the command, such as the name and aliases of the command.

## Executors

As mentioned above, executors define how to process a command. Krypton's command API has three main types of command
executor available for use. These are:
* `BrigadierCommand` - a command that uses a Brigadier `CommandNode` to define its execution.
* `SimpleCommand` - a command that is modelled after the convention popularised by Bukkit and BungeeCord
* `RawCommand` - a command that is not processed to allow external frameworks to do their own processing

You can also create your own `CommandRegistrar` to register your own command, but more on that later.

Anyway, let's break down each one of these types with examples.

### Brigadier

The Brigadier command is named as such because it uses a `CommandNode` from Mojang's
[Brigadier](https://github.com/Mojang/brigadier) command library. This library defines commands in a tree style, using
nodes.

An example of a Brigadier command can be seen with this simple `hello` command:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val myCommand = BrigadierCommand(
    LiteralArgumentBuilder.literal<Sender>("hello")
        .executes {
            val sender = it.source
            sender.sendMessage(Component.text("Hello World!"))
            1
        }
)
```
{% endtab %}

{% tab title="Java" %}
```java
final BrigadierCommand myCommand = new BrigadierCommand(
    LiteralArgumentBuilder.<Sender>literal("hello")
        .executes(context -> {
            final Sender sender = context.getSource();
            sender.sendMessage(Component.text("Hello World!"));
            return 1;
        })
);
```
{% endtab %}
{% endtabs %}

This is the most modern command executor, and the one that offers the most freedom in terms of command execution. We
recommend this type of executor over the other two.

### Simple

The simple command is named as such because it is the simplest form of command executor to learn to use, and it is the
most commonly recognised one amongst the community, due to its usage in the Bukkit and BungeeCord APIs.

Let's take a look at that same hello command, but with a simple executor this time:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
class HelloCommand : SimpleCommand {

    override fun execute(sender: Sender, args: Array<String>) {
        sender.sendMessage(Component.text("Hello There!"))
    }
}
```
{% endtab %}

{% tab title="Java" %}
```java
public final class HelloCommand implements SimpleCommand {

    @Override
    public void execute(final Sender sender, final String[] args) {
        sender.sendMessage(Component.text("Hello There!"));
    }
}
```
{% endtab %}
{% endtabs %}

### Raw

The raw command is named as such because it provides the raw arguments, absent of processing. It is designed for use
with command frameworks, however it can be used the same as any other command.

Here's that same `hello` command, but with a raw executor:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
class HelloCommand : RawCommand {

    override fun execute(sender: Sender, args: String) {
        sender.sendMessage(Component.text("Hello There!"))
    }
}
```
{% endtab %}

{% tab title="Java" %}
```java
public final class HelloCommand implements RawCommand {

    @Override
    public void execute(final Sender sender, final String args) {
        sender.sendMessage(Component.text("Hello There!"));
    }
}
```
{% endtab %}
{% endtabs %}

## Metadata

The second part of the command API is metadata. Every command has metadata associated with it. This metadata tells the
command manager all the triggers for your command, such as its name and its aliases.
You can construct metadata using the builders available within each `CommandMeta` type.

For example, to construct metadata for our `SimpleCommand`, we can do something like this:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
// Builder
val meta = SimpleCommandMeta.builder("hello")
    .alias("world")
    .build()

// DSL
val meta = simpleCommandMeta("hello") {
    alias("world")
}
```
{% endtab %}

{% tab title="Java" %}
```java
final SimpleCommandMeta meta = SimpleCommandMeta.builder("hello")
    .alias("world")
    .build();
```
{% endtab %}
{% endtabs %}

## Registration

The command system is managed by the `CommandManager`. It is responsible for command registration and dispatching.
Let's see how we can use it to register our commands.

An example of how to register our simple command would be:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
server.commandManager.register(HelloCommand(), meta)
```
{% endtab %}

{% tab title="Java" %}
```java
server.commandManager().register(new HelloCommand(), meta);
```
{% endtab %}
{% endtabs %}

Every command has its own implementation of `CommandRegistrar` that's used to register it to a Brigadier 
`RootCommandNode`. For built-in commands, these are internal, and you don't need to worry about how they work.
If you want to register your own subtype of `Command` though, you'll need to implement `CommandRegistrar` in your own
registrar to define how we can turn your custom `Command` in to a command node.
