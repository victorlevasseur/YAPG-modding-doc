# Lua runtime

This section describe how to write lua script / function meant to be
executed during the level runtime.

## Callback functions

Some components have parameters of type "function". Thsese are lua
function that are called during runtime.

*For example, the PlatformerComponent has a `on_start_walking` attribute that
is called when the entity starts moving. In the players templates
provided in the vanilla game, it is often used to change the animation
of the player.*

Most callback function receive an argument called `entity`, it's a
reference on the entity concerned. *In the previous example, the
`on_start_walking` function receive an `entity` argument that is the
entity that starts walking.*

**Warning: when the functions are executed (during runtime), the lua
table representing the template is not accessible (it's just a template,
not the instanciated entity). You must act on the entity instance (given
by the `entity` argument).**

Here is an example of the callback function that changes the animation
of the player (template `kenney/player1.lua`) when he jumps:

```lua
-- The other components here
["Platformer"] = {
    --... (other attributes)
    on_start_jumping = function(entity) --The callback function
        entity:set_attribute("Render", "current_animation", string_value("jump"))
    end,
    --... (other attributes)
}
```

The `entity` object
-------------------

As previously said, functions often have an `entity` parameter. It
represents a particular entity instance.

### Methods

-   `destroy()` : destroys the entity

### Attributes

-   `collidable` : returns the CollidableComponent of the entity
-   `collider` : returns the ColliderComponent of the entity
-   `custom_behavior` : returns the CustomBehaviorComponent of the entity
-   `finish_line` : returns the FinishLineComponent of the entity
-   `hitbox` : returns the HitboxComponent of the entity
-   `platform` : returns the PlatformComponent of the entity
-   `platformer` : returns the PlatformerComponent of the entity
-   `player` : returns the PlayerComponent of the entity
-   `position` : returns the PositionComponent of the entity
-   `render` : returns the RenderComponent of the entity

See [Components](components.md) to learn about the attributes of each components.

### Example

This example demonstrates how to add 200 pixels to the X position of an
entity:

```lua
-- considering that "entity" is the variable holding the entity instance.
entity.position.x = entity.position.x + 200
```

## Per entity instance storage

It's sometimes useful to be able to store custom data in entities for a
later use in a callback. As callbacks are used by all entities
instantiated from a template, you can't use local variables to store
data related to a particular entity instance. But you're lucky, YAPG
provides a special component called `CustomDataComponent` that can
contains **any type of object** (supported by the engine).

This component is quite different from the others because it doesn't
have any preset attributes and it doesn't need to be declared in a
template to be created for each instanciated entities.

### Gettting and settings values from/to `CustomDataComponent`

`get_attribute` and `set_attribute` are not used to get or set a value
inside it (remember, it doesn't have real attributes). But the component
itself is accessible through the `get_custom_data` method of `entity`.
This method returns the custom data component. It has the following
methods:

 - `get_value(field)`: returns the value stored in a field
 - `set_value(field, value)`: set the value stored in a field
 - `has_value(field)`: returns `true` if the specified field exists, `false` otherwise.

 The `get/set_value` methods don't work directly with values. In
 fact, `get_value` return a small **proxy object** containing the
 value and for `set_value`, the `value` must be a **proxy object**
 too.

 However, the game provides functions to convert from/to these proxy
 objects. These functions are :

 -   `as_xxx()`: converts from the proxy object returned by
     `get_attribute` to the xxx type.
 -   `xxx_value()`: converts a value of type xxx to a proxy object usable
     by `set_attribute`.

 `xxx` must be the name of the type (float, int, unsigned_int, string...).

 **Warning: using `int_value()`/`as_int()` for a value of type float
 will not work. You must use the exactly correct type for converting
 proxy objects.**

 *Note:* Why proxy objects ? These is mainly because the game engine is
 written in C++ which is a statically typed language. They allow the
 engine to work with any types of values to receive or change values

### Define default values

To set default values to some fields, you need to declare the component
in the template's `components` table and just write key/values pairs.
**You also need to use the** `XXX_value(...)` **conversion function
according to which type of data you want to store.**

**Example of declaring a default string value to a field in `CustomDataComponent`:**

```lua
-- Some other stuff

components = {
    -- Other components here

    ["CustomDataComponent"] = {
        my_custom_state = string_value("idle state"),
    },

    -- Other components here too
},

--Some other stuff
```

### Use a `CustomDataComponent` field as a template parameter

It is **possible under some conditions** to have a `CustomDataComponent`
field as a template parameter:

 - the field **must have a default value** defined in the template (so that YAPG knows its type)
 - the type of the field **must be supported by the editor**

The syntax for these special parameters is a bit different from the
others: instead of `component` and `attribute` keys, it has a
`custom_data_field` that contains the name of the field linked to the
parameter.

**Example (use the code of the previous example):**

```lua
-- Some other stuff

parameters = {
    my_field_parameter = {
        name = "My special parameter!",
        custom_data_field = "my_custom_state",
    }
}

-- Some other stuff
```
