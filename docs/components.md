# Components and classes

This section describes the attributes of each components.

**Be sure to read** [YAPG template syntax](serialization-format.md) **before continuing your
reading.**

In the attribute's tables below:

-   the **"U"** column tells if the attributes can be unserialized from YAPG templates files
-   the **"R"** column tells if the attributes can be received or
    changed at runtime with `a_component(entity).attr_name`
    and `a_component(entity).attr_name = value` syntaxes. See
    [Lua runtime](lua.md) to see how to get/change attributes' values of components during
    runtime.
-   the **"P"** column tells if the attributes can be used as a template
    parameter.

"✓" means "yes", "…" means that it will be included in a future version
(planned) and nothing means "no".

Currently, the game has the following components:

-   [collidable_component](#collidable_component)
-   [collider_component](#collider_component)
-   [custom_behavior_component](#custom_behavior_component)
-   [custom_data_component](#custom_data_component)
-   [custom_data_component](#custom_data_component)
-   [health_component](#health_component)
-   [platform_component](#platform_component)
-   [platformer_component](#platformer_component)
-   [platformer_hitbox_component](#platformer_hitbox_component)
-   [player_component](#player_component)
-   [position_component](#position_component)
-   [render_component](#render_component)

## collidable_component

Component that allows an entity to react to collisions with multiple polygons.

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
polygons | Map associating string to [polygon_callback](#polygon_callback-class) | The string is the polygon's name. | ✓ |

### polygon_callback class

Represents a polygon with its associated callbacks (used by Collidable_component to allow scripting on collisions)

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
polygon | [polygon](#polygon-class) | The polygon | ✓ | ✓
exclusive | boolean | Whether **all** the following polygon_callback will be ignored if this one collides | ✓ |
on_collision_begin | A function, two args: the entity itself and the other colliding entity | A function called when the collision starts | ✓ |
on_collision_end | A function, two args: the entity itself and the other colliding entity | A function called when the collision ends | ✓ |
collides | A function, two args: the entity itself and the other colliding entity | A function called each frame while the collision still exists. | ✓ |

## collider_component

Component that allows the object to trigger collisions with entities with Collidable_component.

Alone, it will not work as it needs the entity to have a Collidable_component to store at least one hitbox

## custom_behavior_component

Component that allows a lua callback function to be called at each frames.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
on_update | function | A function called each frame with two arguments : entity and dt (the time since the previous frame). | ✓ | |

```lua
components = {
    --The custom_behavior_component
    ["custom_behavior"] = {
        on_update = function(entity, dt)
            --act on the entity
        end,
    },
}
```

## custom_data_component

This is a special component, see [Lua runtime > Per entity instance storage](lua.md#per-entity-instance-storage) to understand how it should be used.

## health_component

Component that allows an entity to have an health. The entity is killed when its health falls to 0.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
health | int | The current health | ✓ | ✓ (read-only)
max_health | int | The maximum health | ✓ | ✓

### Methods

Methods that can be called on the `health_component` in scripts (runtime).

 - `kill()` : put the entity's health to 0. Will be killed next frame.
 - `loose_pv(nbr_hp)` : remove `nbr_hp` to the entity (without going under 0).
 - `gain_pv(nbr_hp)` : add `nbr_hp` to the entity (without exceeding `max_health`).

 **Example: an enemy that instant kills the player**
 ```lua
 --This is the callback called when the enemy polygon is collided by an entity
 on_collision_begin = function(entity, other)
     if(player(other) ~= nil and health(other) ~= nil) then --Tests if it's a player and it has a health_component
         health(other):loose_pv(1) --Remove one hp to it
     end
 end,
 ```

## platform_component

Declares and entity as a platform on which the entities with the `Platformer_component` can walk and jump.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
activated | bool | activates or not the platform collision with entities | ✓ | ✓ | ✓
platform_type | string | `"Platform"` for solid platforms or `"Jumpthru"` for platforms that the platformer entities can pass through from underneath. | ✓ | |

## platformer_component

Declares an entity as affected by the gravity and that it can walk and jump on platforms.

*Note: it doesn't mean the entity is controlled by a player, need to use Player_component
in addition for that. So, items moving on the ground can use the Platformer_component too.*

**Warning: when trying to move an entity with this component in a lua callback function,
consider setting `wants_to_jump`, `wants_to_go_left` and `wants_to_go_right` attributes
of this component to make it move (instead of changing it position).**

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
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

## platformer_hitbox_component

Contains an polygonal hitbox (must be convex). Used to detect collisions between platformer
entities and platforms (not used by the CollisionSystem however).

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
polygon | polygon | The hitbox convex polygon | ✓ | ✓ |

```lua
components = {
    --The platformer_hitbox_component
    ["platformer_hitbox"] = {
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

## player_component

Declare an entity as a player. It will allow the player's controls to make the entity walk and jump using the `Platformer_component`.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
player_number | int | The player number (starting from 0) |  | ✓ (read-only) |

## position_component

Component containing position and size data of an entity.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
x | float | | ✓ | ✓ | ✓
y | float | | ✓ | ✓ | ✓
width | float | | ✓ | ✓ | ✓
height | float | | ✓ | ✓ | ✓

```lua
components = {
    --The position_component
    ["position"] = {
        x = 10,
        y = 20,
        width = 64,
        height = 128,
    },
}
```

## render_component

Stores the animations of an entity and tells the render engine which one to render.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary) | [P](#vocabulary)
-----|------|-------------|------------------|--------------------|------------------
texture | string | The filepath to the texture picture | ✓ | ✓ | ✓
animations | map associating string to animation | Associates the animation name with the animation. | ✓ | … |
current_animation | string | The current animation's name | ✓ | ✓ | ✓

```lua
components = {
    --The render_component
    ["render"] = {
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
