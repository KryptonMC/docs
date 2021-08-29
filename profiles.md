# Profiles

Profiles are holders of authenticated information for users. They provide useful information, such as a user's name and
unique ID (UUID).

In Krypton, all players have an authenticated `GameProfile` object containing the information received from Mojang
when the player was authenticated. Or, if they were not authenticated, this will contain information about the
offline user.

All cached `GameProfile`s are held by the `ProfileCache`, and can be retrieved using the appropriate `get` functions,
providing either the username or the UUID of the player whose information you would like to receive. You can also
retrieve all the currently cached profiles from the `profiles` property (`getProfiles` method in Java).

## Retrieving profiles

As mentioned before, you may retrieve profiles using the appropriate `get` function, either providing the username or
the `UUID` of the player whose information you wish to receive. Invoking this function will, as the documentation
states, perform a blocking request to retrieve the profile if it is not already stored in the cache. It is **your
responsibility** to ensure that this request does not block the server whilst it is executing.

Executing these functions will provide you with a `GameProfile` if the username or UUID has an associated profile,
or null if not. The `GameProfile` will hold the `username` and `uuid` of the user, and a list of profile properties,
which could contain things like skin data. See [here](https://wiki.vg/Mojang_API#UUID_to_Profile_and_Skin.2FCape) for
more details on how this actually works, and how to read the data.

The following are examples of contexts where the server could be blocked from executing:
* When listening for a blocking event, such as `JoinEvent` or `LoginEvent`.
* When running a command.

The following are examples of contexts where the server should not be blocked from executing:
* When listening for an asynchronous event, such as `AuthenticationEvent` or `ChatEvent`.
* When in a task that has been scheduled by the `Scheduler`.

An example of requesting a profile is as follows:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
package my.plugin

import com.google.inject.Inject
import org.apache.logging.log4j.Logger
import org.kryptonmc.api.auth.GameProfile
import org.kryptonmc.api.auth.ProfileCache
import org.kryptonmc.api.plugin.annotation.Plugin

@Plugin("my-plugin")
class MyPlugin @Inject constructor(
    val profileCache: ProfileCache, // Inject the profile cache
    val logger: Logger
) {

    @Listener
    fun onLogin(event: LoginEvent) {
        val profile = profileCache[event.username] // Lookup the profile for the user
        if (profile == null) {
            logger.warn("No profile found for user with name ${event.username}!")
            return
        }
        // Find the skin profile property, may be less expensive in the future
        val skin = profile.properties.firstOrNull { it.name == "textures" }
        logger.info("The skin base 64 of user with name ${event.username} is ${skin?.value}")
    }
}
```
{% endtab %}

{% tab title="Java" %}
```java
package my.plugin

import com.google.inject.Inject
import org.apache.logging.log4j.Logger
import org.kryptonmc.api.auth.GameProfile
import org.kryptonmc.api.auth.ProfileCache
import org.kryptonmc.api.plugin.annotation.Plugin

@Plugin(id = "my-plugin")
public final class MyPlugin {

    private final ProfileCache profileCache;
    private final Logger logger;

    @Inject
    public MyPlugin(final ProfileCache profileCache, final Logger logger) {
        this.profileCache = profileCache;
        this.logger = logger;
    }

    @Listener
    public void onLogin(final LoginEvent event) {
        final GameProfile profile = profileCache.get(event.username); // Lookup the profile for the user
        if (profile == null) {
            logger.warn("No profile found for user with name ${event.username}!");
            return;
        }
        // Find the skin profile property, may be less expensive in the future
        ProfileProperty skin = null;
        for (final ProfileProperty property : profile.properties) {
            if (property.name == "textures") {
                skin = property;
                break;
            }
        }
        logger.info("The skin base 64 of user with name " + event.username + " is " + skin != null ? skin.value : "null");
    }
}
```
{% endtab %}
{% endtabs %}

## Modifying player profiles

You can modify player profiles using the `AuthenticationEvent`. This event will be called before the server has made
the request to authenticate the `name` with Mojang, and setting its result can change the profile that will be used
for the user. Passing `null` to the result will use the authenticated profile.

To construct your own `GameProfile` instances, you can use the `of` factory functions. For example, to construct a new
`GameProfile` with name "Bob" and UUID "17c2482e-1e21-46a6-ba2a-daf55a136fec" (randomly generated), you could do
`GameProfile.of("Bob", UUID.fromString("17c2482e-1e21-46a6-ba2a-daf55a136fec"))`.

An example of how to use this to modify profiles in the authentication event is as follows:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
package my.plugin

import com.google.inject.Inject
import java.util.UUID
import org.apache.logging.log4j.Logger
import org.kryptonmc.api.auth.GameProfile
import org.kryptonmc.api.event.auth.AuthenticationEvent
import org.kryptonmc.api.plugin.annotation.Plugin

@Plugin("my-plugin")
class MyPlugin {

    @Listener
    fun onAuthentication(event: AuthenticationEvent) {
        // Create a profile for Bob
        val profile = GameProfile.of("Bob", UUID.fromString("17c2482e-1e21-46a6-ba2a-daf55a136fec"))
        event.result = AuthenticationResult.allowed(profile) // Set the result of the event
    }
}
```
{% endtab %}

{% tab title="Java" %}
```java
package my.plugin;

import com.google.inject.Inject;
import java.util.UUID;
import org.apache.logging.log4j.Logger;
import org.kryptonmc.api.auth.GameProfile;
import org.kryptonmc.api.event.auth.AuthenticationEvent;
import org.kryptonmc.api.event.auth.AuthenticationResult;
import org.kryptonmc.api.plugin.annotation.Plugin;

@Plugin(id = "my-plugin")
public final class MyPlugin {

    @Listener
    public void onAuthentication(final AuthenticationEvent event) {
        // Create a profile for Bob
        final GameProfile profile = GameProfile.of("Bob", UUID.fromString("17c2482e-1e21-46a6-ba2a-daf55a136fec"));
        event.setResult(AuthenticationResult.allowed(profile)); // Set the result of the event
    }
}
```
{% endtab %}
{% endtabs %}
