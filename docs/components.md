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
-   [PlatformerComponent](#platformercomponent)
-   [PositionComponent](#positioncomponent)
-   [RenderComponent](#rendercomponent)

## CustomBehaviorComponent

Component that allow a lua callback function to be called at each frames.

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

Contains an polygonal hitbox (must be convex). Used to detect collisions between platformer
entities and platforms and collisions between collider entities and collidable entities.

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

## PlatformerComponent

Declare an entity as affected by the gravity and that it can walk and jump on platforms.

*Note: it doesn't mean the entity is controlled by a player, need to use PlayerComponent
in addition for that. So, items moving on the ground can use the PlatformerComponent too.*

**Warning: when trying to move an entity with this component in a lua callback function,
consider setting `wants_to_jump`, `wants_to_go_left` and `wants_to_go_right` attributes
of this component to make it move (instead of changing it position).**

Name | Type | Description | [U](#vocabulary) | [G/S](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
max_walking_speed | float | The maximum walking speed (pixels/s) | ✓ | ✓ | ✓
max_jumping_speed | float | The maximum jumping speed (pixels/s) | ✓ | ✓ | ✓
max_falling_speed | float | The maximum falling speed (pixels/s) | ✓ | ✓ | ✓
acceleration | float | The walking acceleration of the entity (jumping and falling are affected by `gravity`, not `acceleration`) (pixels/s^2). | ✓ | ✓ | ✓
deceleration | float | The walking deceleration of the entity (pixels/s^2) | ✓ | ✓ | ✓
gravity | float | The gravity. The jumping and falling acceleration (pixels/s^2) | ✓ | ✓ | ✓
wants_to_jump | bool | If `true`, the player will try to jump as soon as possible (the value is not reset by the engine each frame, so don't forget to turn it back to `false` after). | ✓ | ✓ | ✓
wants_to_go_left | bool | If `true`, the player will try to go left (the value is not reset by the engine each frame, so don't forget to turn it back to `false` after). | ✓ | ✓ | ✓
wants_to_go_right | bool | If `true`, the player will try to go right (the value is not reset by the engine each frame, so don't forget to turn it back to `false` after). | ✓ | ✓ | ✓
on_idle | function | Function with the current entity as its single argument. Called when the entity stops moving. | ✓ | ✓ | ✓
on_start_walking | function | Function with the current entity as its single argument. Called when the entity starts walking (on a platform). | ✓ | ✓ | ✓
on_start_jumping | function | Function with the current entity as its single argument. Called when the entity starts jumping. | ✓ | ✓ | ✓
on_start_falling | function | Function with the current entity as its single argument. Called when the entity starts falling (from a platform or when going down after a jump). | ✓ | ✓ | ✓
on_turn_right | function | Function with the current entity as its single argument. Called when the entity turns towards the right direction. | ✓ | ✓ | ✓
on_turn_left | function | Function with the current entity as its single argument. Called when the entity turns towards the left direction. | ✓ | ✓ | ✓

## PositionComponent

Component containing position and size data of an entity.

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

Stores the animations of an entity and tells the render engine which one to render.

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
