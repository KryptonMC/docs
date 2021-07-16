# Blocks

### Overview

The block API, like most of the rest of the API, is designed around simplicity, and ease of use, as well as ease of implementation, and thread-safety.

The `Block` interface is fully immutable, which means it cannot be modified. Any attempt to modify any of its data will result in a new `Block` created with the requested changes. This immutability also makes blocks very reusable and completely thread-safe.

In addition to this, a `Block` does not store any contextual data that could compromise its ability to be effectively reused, such as its location, or the 

```
Overview

The block API, like most of the rest of the API, is designed around
simplicity and ease of use, as well as ease of implementation, and
thread-safety.
```

```text

`Block`s are completely immutable, which means there is no hidden state,
or any whacky logic going on behind the scenes. This immutability also
helps to make the blocks very easily reusable, and thread-safe.

This means that any attempts to modify a block object will give you
a new object with the applied changes. Currently, however, you are
only able to modify the properties of a block.

A `Block` does not store any information that could restrict its
ability to be reused easily, such as the location of it.

## Usage

You can get built-in blocks from the constants available in the `Blocks`
class. In addition, there is also a registry for blocks, so you can also
retrieve them using the [Registry API]()
```

