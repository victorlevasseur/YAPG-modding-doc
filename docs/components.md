# Components and classes

This section describes the attributes of each components.

**Be sure to read** [serialization-format](serialization-format.md) **before continuing your
reading.**

## Vocabulary

In the attribute's tables below:

-   the **"U"** column tells if the attributes can be unserialized from
    lua/XML
-   the **"G/S"** column tells if the attributes can be received or
    changed at runtime with `entity:get_attribute("aComp", "attrName")`
    and `entity:set_attribute("aComp", "attrName", val)` methods. See
    lua to see how to get/change attributes' values of components during
    runtime.
-   the **"P"** column tells if the attributes can be used as a template
    parameter.

"✓" means "yes", "…" means that it will be included in a future version
(planned) and nothing means "no".

Currently, the game has the following components:

-   [CustomBehaviorComponent](#custombehaviorcomponent)
-   [HitboxComponent](#hitboxcomponent)
-   [PositionComponent](#positioncomponent)
-   [RenderComponent](#rendercomponent)

## CustomBehaviorComponent

### Attributes

Name | Type | Description | [U](#vocabulary) | [G/S](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
on_update | function | A function called each frame with two arguments : entity and dt (the time since the previous frame). | ✓ | |

### Lua serialization

```lua
components = {
    --The CustomBehaviorComponent
    ["CustomBehavior"] = {
        on_update = function(entity, dt)
            --act on the entity
        end,
    },
}
```

## HitboxComponent

### Attributes

Name | Type | Description | [U](#vocabulary) | [G/S](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
polygon | polygon | The hitbox convex polygon | ✓ | ✓ |

### Lua serialization

```lua
components = {
    --The HitboxComponent
    ["Hitbox"] = {
        --this is a square hitbox
        polygon = {
            points = {
                {
                    x = 0,
                    y = 0
                },
                {
                    x = 64,
                    y = 0
                },
                {
                    x = 64,
                    y = 64
                },
                {
                    x = 0,
                    y = 64
                },
            }
        },
    },
}
```

## PositionComponent

### Attributes

Name | Type | Description | [U](#vocabulary) | [G/S](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
x | float | | ✓ | ✓ | ✓
y | float | | ✓ | ✓ | ✓
width | float | | ✓ | ✓ | ✓
height | float | | ✓ | ✓ | ✓

### Lua serialization

```lua
components = {
    --The PositionComponent
    ["Position"] = {
        x = 10,
        y = 20,
        width = 64,
        height = 128,
    },
}
```

### Lua script example

This example moves the entity by 100px towards the right direction.

```lua
--myEntity is an particular entity instance
local old_x_pos = as_float(myEntity:get_attribute("Position", "x"))
local new_x_pos = old_x_pos + 100
myEntity:set_attribute("Position", "x", float_value(new_x_pos))
```

## RenderComponent

### Attributes

Name | Type | Description | [U](#vocabulary) | [G/S](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
texture | string | The filepath to the texture picture | ✓ | ✓ | ✓
animations | map associating string to animation | Associates the animation name with the animation. | ✓ | … |
current_animation | string | The current animation's name | ✓ | ✓ | ✓

### Lua serialization

```lua
components = {
    --The RenderComponent
    ["Render"] = {
        texture = "myTexture.png",
        animations = {
            default_animation = {
                duration = 1, --See the doc of the Animation class to see how Animations are serialized.
            },
            alternative_animation = {
                duration = 2,
            },
        },
    },
}
```
