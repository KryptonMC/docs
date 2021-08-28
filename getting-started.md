# Getting Started

So, you've checked out the project and want to crack on making your first plugin? Great! This guide should show you how.

## Dependency

Before we begin, you need to add the API as a dependency for your project. This can be done either using a build
system of choice (recommend and supported), or directly using your IDE (not recommended and not supported).

Here are some examples for different build systems of how you can depend on the API in your project:

### Gradle

#### Kotlin DSL

```kotlin
repositories {
    maven("https://repo.kryptonmc.org/releases")
}

dependencies {
    compileOnly("org.kryptonmc:api:LATEST")
}
```

#### Groovy DSL

```groovy
repositories {
    maven { url 'https://repo.kryptonmc.org/releases' }
}

dependencies {
    compileOnly 'org.kryptonmc:api:LATEST'
}
```

### Maven

```xml
<repositories>
    <repository>
        <id>krypton-repo</id>
        <url>https://repo.kryptonmc.org/releases</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>org.kryptonmc</groupId>
        <artifactId>api</artifactId>
        <version>LATEST</version>
    </dependency>
</dependencies>
```

## Creating your first plugin

After adding the API as a dependency, creating your first plugin is fairly simple. Let's try learning by example:

### Kotlin

```kotlin
package my.first.plugin

import com.google.inject.Inject
import org.apache.logging.log4j.Logger
import org.kryptonmc.api.Server
import org.kryptonmc.api.event.Listener
import org.kryptonmc.api.event.server.ServerStartEvent

@Plugin(
    id = "my-plugin",
    name = "MyPlugin",
    version = "1.0",
    description = "My first ever plugin created for Krypton"
)
class MyPlugin @Inject constructor(
    val server: Server,
    val logger: Logger
) {

    @Listener
    fun onStart(event: ServerStartEvent) {
        logger.info("Hello there! This is my first plugin!")
    }
}
```

### Java

```java
package my.first.plugin;

import com.google.inject.Inject;
import org.apache.logging.log4j.Logger;
import org.kryptonmc.api.Server;
import org.kryptonmc.api.event.Listener;
import org.kryptonmc.api.event.server.ServerStartEvent;

@Plugin(
    id = "my-plugin",
    name = "MyPlugin",
    version = "1.0",
    description = "My first ever plugin created for Krypton"
)
public final class MyPlugin {

    private final Server server;
    private final Logger logger;

    @Inject
    public MyPlugin(final Server server, final Logger logger) {
        this.server = server;
        this.logger = logger;
    }

    @Listener
    public void onStart(final ServerStartEvent event) {
        logger.info("Hello there! This is my first plugin!");
    }
}
```

Now, you might be thinking, "what the hell is that? what does this do?" well, let's break it down:

First, we declare a new class that has the `Plugin` annotation. This annotation marks this class as the main
class for our plugin, which will be loaded by the server on startup. This contains some important details, like
our plugin's `id`, which is a unique identifier for your plugin that will be used to depend on it, so this must
be unique. It also has to pass the regex `[A-Za-z0-9-_]+`. You can use a tool such as [regex101](https://regex101.com)
to test if your `id` passes this regex.

In addition, we declare a constructor that has the `Inject` annotation. This instructs Guice to inject some dependencies
in to our constructor. If you don't know what Guice is, or how to use it, feel free to familiarise yourself with our
[Guice](guice.md) (TODO) chapter before continuing.

In this case, you can see that we are injecting both the `Server`, and our plugin's `Logger`. The `Logger` interface
is a simple interface that can be used to log messages out to the console. For more information, see the
[Logging](logging.md) (TODO) chapter.

Second, you can see that we declare a function called `onStart` that takes a single parameter, `ServerStartEvent`, and
is annotated with the `Listener` annotation. If you're familiar with the event systems of similar APIs such as
Velocity or Sponge, you may recognise this. If you don't, this is an event listener. The `ServerStartEvent` is a marker
event used to signal that the server has mostly finished starting, and that you can use to register things for your
plugin. More on this later.

In this case, we are telling the server that we want to register a new event listener for the `ServerStartEvent`, and
we want to print the message "Hello there! This is my first plugin!" to the console at the `INFO` level when this
event is triggered.

If you copy and run this code, and your environment is set up correctly, you should see a message that looks something
like [this](https://i.imgur.com/KyUXMjV.png) appear in the console.

If you get stuck and need help with any of this, please feel free to join the Discord server
[here](https://discord.gg/4QuwYACDRX), and you can also check out all the source code for the various environments
in the [plugin-examples repository](https://github.com/KryptonMC/plugin-examples), in the `getting-started` folder.
