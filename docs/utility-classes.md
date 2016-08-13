# Utility classes

Utility classes are classes used by components to represent some
specific data structures.

## Vocabulary

In the attribute's tables below:

-   The "U" column tells if the attributes can be unserialized from
    lua/XML.
-   The "R" column tells if the attributes can be changed at runtime
    with lua scripting using the `myObject.attribute = newValue` syntax.

"✓" means "yes", "…" means that it will be included in a future version
(planned) and nothing means "no".

## animation class

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
duration | float | The total duration of the animation, in seconds. | ✓ | …
frame | array of [frame](#frame-class) | The frames of the animation. | ✓ | …

### Lua serialization example

```lua
{
    duration = 1,
    frames = {
        {
            rect = frameRect1, --see the definition of Frame for more information
            relative_duration = 0.6,
        },
        {
            rect = frameRect2,
            relative_duration = 0.4,
        },
    },
}
```

## frame class

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
rect | [int_rect](#int_rect-class) | The part of the texture (defined in the RenderComponent) to use during this frame. | ✓ | …
relative_duration | float | The relative duration of this frame, relatively to the [Animation](#animation-class) total duration. | ✓ | …

## int_rect class

Represents a axis-aligned rectangle.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
x | int | The X position of the top-left corner. | ✓ | …
y | int | The Y position of the top-left corner. | ✓ | …
width | int | The width of the rectangle. | ✓ | …
height | int | The height of the rectangle. | ✓ | …


## polygon class

Represents a convex polygon.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
points | array of [sf_vector_2f](#sf_vector_2f-class) | The points of the hitbox | ✓ |

### Lua serialization example

```lua
{
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
}
```

## sf_vector_2f class

Represents a point with two float coordinates.

### Attributes

Name | Type | Description | [U](#vocabulary) | [R](#vocabulary)
-----|------|-------------|------------------|-----------------
x | float | The X coordinate | ✓ | ✓
y | float | The Y coordinate | ✓ | ✓
