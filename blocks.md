# Blocks

Blocks in Krypton are immutable data holders, providing access to many properties. The actual `Block` interface
does the job of two interfaces in vanilla Minecraft and other APIs like Sponge, in that it represents both the block
type **and** the block state.

It is important to be aware that blocks are designed with reusability in mind, as they do not store any specific
information such as their placement or the world they may be placed in, if any, they only store their static data,
such as their type ID (`id`), state ID, hardness, friction, render shape, properties, and a lot more.

Built-in blocks can either be retrieved using the constants object `Blocks`, which follows the same pattern as the
rest of the registry system, or by requesting for one at a specific position in either a `World` or `Chunk` using
the `getBlock` functions, and you can set blocks in worlds by using the `setBlock` functions in `World` and `Chunk`.

## Usage

An example of how to get a grass block and set it in a world at some coordinates is as follows:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val grass = Blocks.GRASS
myWorld.setBlock(0, 0, 0, grass)
```
{% endtab %}

{% tab title="Java" %}
```java
final Block grass = Blocks.GRASS;
myWorld.setBlock(0, 0, 0, grass);
```
{% endtab %}
{% endtabs %}

(full example can be found in the examples repository [here](https://github.com/KryptonMC/plugin-examples))

## Handling and behaviour

Some blocks in Minecraft, such as redstone, aren't purely aesthetic, however, and not all data for blocks is static,
some needs to be calculated on the fly based on context. Well fret not, because Krypton has a solution for this.

You can define your own block handlers, which can handle those situations where data is dynamic and contextual, simply
implement the `BlockHandler` interface to define how the server should handle certain things about certain block types.
These handlers are also reusable, and can be applied to any type of block, using the block manager.

It is highly recommended that you make your `BlockHandler` implementations singletons, so that you can avoid
accidentally instantiating them multiple times in places and defeat their purpose.

This can be done like so:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
object MyDirtHandler : BlockHandler {

    override fun getDestroyProgress(player: Player, world: World, block: Block, position: Vector3i): Float {
        // Do some calculations
        return 0F
    }

    override fun onPlace(player: Player, block: Block, position: Vector3i, face: BlockFace) {
        // Do some placement handling
    }

    override fun preDestroy(player: Player, world: World, block: Block, position: Vector3i) {
        // Do some pre-destroy handling
    }

    override fun onDestroy(player: Player, block: Block, position: Vector3i, item: ItemStack) {
        // Do some destroy handling
    }

    override fun interact(player: Player, world: World, block: Block, position: Vector3i, hand: Hand): InteractionResult {
        // Do some processing for the interaction
        return InteractionResult.SUCCESS
    }

    override fun attack(player: Player, world: World, block: Block, position: Vector3i) {
        // Do some processing for the attack
    }
}
```
{% endtab %}

{% tab title="Java" %}
```java
public final class MyDirtHandler implements BlockHandler {

    public static final MyDirtHandler INSTANCE = new MyDirtHandler();

    private MyDirtHandler() {
    }

    @Override
    public float getDestroyProgress(final Player player, final World world, final Block block, final Vector3i position) {
        // Do some calculations
        return 0F;
    }

    @Override
    public void onPlace(final Player player, final Block block, final Vector3i position, final BlockFace face) {
        // Do some placement handling
    }

    @Override
    public void preDestroy(final Player player, final World world, final Block block, final Vector3i position) {
        // Do some pre-destroy handling
    }

    @Override
    public void onDestroy(final Player player, final Block block, final Vector3i position, final ItemStack item) {
        // Do some destroy handling
    }

    @Override
    public InteractionResult interact(final Player player, final World world, final Block block, final Vector3i position, final Hand hand) {
        // Do some processing for the interaction
        return InteractionResult.SUCCESS;
    }

    @Override
    public void attack(final Player player, final World world, final Block block, final Vector3i position) {
        // Do some processing for the attack
    }
}
```
{% endtab %}
{% endtabs %}

These handlers can then be registered to the `BlockManager` that can be retrieved from the `Server` with
`server.blockManager` (`getBlockManager` in Java), or be injected in to your plugin's constructor. If the word
"injection" confuses you, see [the chapter on Guice](guice.md) (TODO).

An example of how to register your new handler to the `BlockManager` is as follows:

{% tabs %}
{% tab title="Kotlin" %}
```kotlin
val blockManager = server.blockManager
blockManager.register(Blocks.DIRT, MyDirtHandler)
```
{% endtab %}

{% tab title="Java" %}
```java
final BlockManager blockManager = server.getBlockManager();
blockManager.register(Blocks.DIRT, MyDirtHandler.INSTANCE);
```
{% endtab %}
{% endtabs %}

Please be aware that if another handler is registered for the same block type after your handler, that handler
will **override** your existing handler and replace it.
