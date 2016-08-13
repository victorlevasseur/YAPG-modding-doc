# Lua template syntax

This section describes how objects of a specific type are represented
when **serialized in a YAPG template file** (written in Lua).

**The runtime Lua representation (used in lua functions during runtime) of objects
has nothing to do with this "descriptive" format.** See [Lua runtime](lua.md) to
see how to get/change attributes/objects values during runtime.

## Numbers (float, double, int, unsigned_int)

Attributes of type "float", "double", "int", "unsigned_int" can be any
value, with or without a point.

## string

Attributes of type "string" can be any string, written between `"`.

## bool

Attributes of type "bool" can be `true` or `false`.

## array

Attributes of type "array of T", with T a type.

```lua
{
    first_item, --keys and items must follow the YAPG template syntax to represent a value of type T
    second_item, -- another value!
    ...
}
```

## map

Attributes of type "Map associating T to U", with T and U types.

```lua
{
    first_key = first_item, --keys and items must follow the YAPG template syntax to represent a value of type T
    second_key = second_item,
    ... = ...
}
```

## function

Function are often used as callbacks called when something special happens in-game.

For a function returning type T with parameters of types X, Y, ... it's
written this way:

```lua
function(parameter_of_type_x, parameter_of_type_y, ...)
    --doing something there
    return object_of_type_t --an object of type T (lua runtime here as it's a script function!)
end
```

## Class

Classes can contain multiple attributes of different types. All the
objects described in the [Components](components.md)
and [Utility classes](utility-classes.md) are classes.

When in a lua template file, classes are represented with a
lua table with keys being the attributes' names (and the associated
values being the lua template syntax of the values).

For example, for a class name MyClass containing two attributes (a1 of
type T and a2 of type U), we can describe the class like this:

### Lua

```lua
{
    a1 = value_of_a1, -- must follow the YAPG template syntax to represent a value of type T
    a2 = value_of_a2, -- must follow the YAPG template syntax to represent a value of type U
}
```
