# XsStructs

An XS script that introduces **struct-like functionality** into XS scripting.

## Why Structs?

The XS language provides arrays, vectors and primitive types, but it does not provide a direct way to group related
values together (especially if they are of different types).
As a result, related data is often stored across multiple arrays or many global variables.

XsStructs introduces a simple struct-like system.
You define a structure by listing its attributes and their types.
After a structure is defined, you can create instances of it.

Each instance stores one value per attribute defined in the structure.
Multiple instances of the same structure can exist at the same time, each holding different data.

This makes it possible to store grouped data together all using simple easy-to-remember functions.
To read more about these functions, continue reading below.

## Example usage

The following example shows how a player struct is defined and used to store player name and score.
You can copy and paste this code into your script to try it out. Remember to set up XsStructs before attempting to run the code, you can copy and paste the entire file above the `void main` function to make it work quickly.

```cpp
void main() {
    // 0. Initialize the structs script 
    initializeStructsScript();
    
    // 1. Define your struct
    defineStruct("Player");
    defineStructAttribute("Player", "name",  TYPE_STRING);
    defineStructAttribute("Player", "score", TYPE_INT);
    
    // 2. Create instances of your struct
    vector p1 = new("Player");
    vector p2 = new("Player");
    
    // 3. Set values on the instances
    structSetString(p1, "name", "MrKirby");
    structSetInt(p1, "score", 10);
   
    structSetString(p2, "name", "Bowser");
    structSetInt(p2, "score", 16);
    
    // 4. Retrieve values from the instances
    xsChatData(structGetString(p1, "name") + ": " + structGetInt(p1, "score"));
    // MrKirby: 10
    xsChatData(structGetString(p2, "name") + ": " + structGetInt(p2, "score"));
    // Bowser: 16
}
```

### Look inside a struct / instance

You can use printing functions to see inside a struct definition or the values stored in an instance.

```cpp
printStructDefinition("Player");

// |00| Player:
// |01|   - name: string
// |02|   - score: int

printStructInstance(p1);

// |00| Player():
// |01|   - name: 'MrKirby'
// |02|   - score: 10
```

_The prefix `|xx|` is necessary as chat output may suppress duplicates if there are any._

# Using XsStructs

## 1. Setting up the script

There's two ways of setting up XsStructs. You can use either option. The only advantage of the "include" method, is being able to replace the file more easily if there are any updates in the future.

### Option A - Using `include`

1. Place the `structs.xs` file in your XS scripts folder:  
   `C:\Users\<user>\Games\Age of Empires 2 DE\<steamId>\resources\_common\xs\`  
   (Or any other folder in which AoE2 DE will look for scripts)

2. Then add this to the top of your script:
   ```cpp
   include "structs.xs";
   ```

### Option B - Copy and paste the entire code

Copy and paste the entire content of `structs.xs` at the top of your XS script.

## 2. Update default configuration

Because arrays are used internally, there's a configured max for most aspects of the script.
You can update these before initializing the script to fit your needs. 
Make sure to update these defaults inside your `void main` function **before** initializing Xs Structs.

The following values can be updated:

### `MAX_STRUCT_COUNT`

Default: `10`

The maximum number of structs that can be defined at once.

### `MAX_STRUCT_ATTRIBUTES`

Default: `10`

The maximum number of attributes that can be defined per struct.

### `MAX_INSTANCE_PER_STRUCT`

Default: `100`

The maximum number of instances that can be created per struct.

### `STRUCT_PRINT_BUFFER_SIZE`

Default: `200`

The maximum number of lines for the string buffer used by all printing functions.
Usually 200 is more than enough, but if you have exceptionally large or deeply nested structs, you might want to
increase this value so printing doesn't get cut off.

## 3. Initializing the script

To use XsStructs, you need to call `initializeStructsScript` in your script's `main` function.
This is necessary because the script needs to initialize internal arrays for storing the defined structures, instances
logging and error handling.

```cpp
void main() {
    /* Any configuration changes should be here */

    initializeStructsScript();
}
```

Note, if you do **not** define your structs in the `void main()` function, you most likely won't need to call this 
function as there's a high-priority rule that runs to do this automatically. This rule does not run
before the `void main()` function.

## 4. Defining structs

To define a struct, you need to call `defineStruct` and provide a name for your struct.
You can then define attributes for your struct using `defineStructAttribute`.

```cpp
void main() {
    ...
    defineStruct("Player");
    defineStructAttribute("Player", "name",  TYPE_STRING);
    defineStructAttribute("Player", "score", TYPE_INT);
}
```

### Types

Any type you can define in XS scripting can be used inside a struct. On top of that, you can even nest
structs inside other structs.

If you want to learn more about the different types in XS, check out the [UGC documentation](https://ugc.aoe2.rocks/general/xs/beginner/#22-variables). 

| Type            | Variable               | Used to store...                             | Default value    | Type Value |
|-----------------|------------------------|----------------------------------------------|------------------|------------|
| `int`           | `TYPE_INT`             | ... integers (whole numbers)                 | `0`              | `0`        |
| `bool`          | `TYPE_BOOL`            | ... booleans (true/false)                    | `false`          | `1`        |
| `float`         | `TYPE_FLOAT`           | ... floats (decimal values)                  | `0.0`            | `2`        |
| `string`        | `TYPE_STRING`          | ... strings (text)                           | `""`             | `3`        |
| `vector`        | `TYPE_VECTOR`          | ... vectors (x, y, z)                        | `(0, 0, 0)`      | `4`        |
| `struct`        | `TYPE_STRUCT`          | ... structs                                  | `cInvalidVector` | `5`        |
| `int[]`         | `TYPE_INT_ARRAY`       | ... an array of integers                     | `-1`             | `100`      |
| `bool[]`        | `TYPE_BOOL_ARRAY`      | ... an array of booleans                     | `-1`             | `101`      |
| `float[]`       | `TYPE_FLOAT_ARRAY`     | ... an array of floats                       | `-1`             | `102`      |
| `string[]`      | `TYPE_STRING_ARRAY`    | ... an array of strings                      | `-1`             | `103`      |
| `vector[]`      | `TYPE_VECTOR_ARRAY`    | ... an array of vectors                      | `-1`             | `104`      |
| `struct[]`      | `TYPE_STRUCT_ARRAY`    | ... an array of structs                      | `-1`             | `105`      |
| `<type>[][]`*   | `TYPE_<type>_ARRAY_2D` | ... an array of arrays of \<type\>           | `-1`             | `20x`      |
| `<type>[][][]`* | `TYPE_<type>_ARRAY_3D` | ... an array of arrays of arrays of \<type\> | `-1`             | `30x`      |

\*: Even more deeply nested arrays (4D, 5D, etc.) are also supported. 
These do not have a dedicated type variable. Instead, use an int where for every dimension you add 100 to the type.
So if you'd want strings nested 4 deep, you'd use `403` where normally you'd use `TYPE_STRING_ARRAY`. 

#### Type technicalities

If you're not interested in printing the struct instances, you could replace any `array` type with `TYPE_INT` and any
struct type with `TYPE_VECTOR`. The `struct` and `array` types only exist so that the printing functions can print
them in a readable format.

## 5. Instantiating structs

To create an instance of a struct, you need to call `new` and provide the name of the struct you want to create.

```cpp
vector p1 = new("Player");
```

The new instance is returned as a vector. This vector is used to 'represent' the instance and has to be used
for all further operations on the instance.

If you ever need to delete an instance, you can call `deleteInstance` function with the vector returned by `new`.
This way the `MAX_INSTANCE_PER_STRUCT` limit is not reached too quickly.

```cpp
deleteInstance(p1);
```

Note: Currently, the arrays used for instances are not re-used after deletion. This shouldn't be a problem in almost all 
cases, but if you create thousands upon thousands of instances, there is a chance memory will eventually be exhausted.

If you, for a function want to differentiate between types of structs, you can use the `getStructName` function.

```cpp
getStructName(p1); // returns "Player"
```

Or, if you want to check if a struct is a specific type, you can use the `isInstance` function.

```cpp
isInstance(p1, "Player"); // returns true
```

## 6. Setting values on instances

To set a value on an instance, you need to call one of the `structSet<type>` functions. Where `<type>` is the type
of the attribute you want to set.

```cpp
structSetString(p1, "name", "MrKirby");
structSetInt(p1, "score", 10);
```

## 7. Reading values from instances

To read a value from an instance, you need to call one of the `structGet<type>` functions. Where `<type>` is the type
of the attribute you want to read.

```cpp
structGetString(p1, "name"); // returns "MrKirby"
structGetInt(p1, "score"); // returns 10
```

## 8. Error handling

There's a simple error handling system in place. This can be used to silently handle errors that occur during runtime.
The only error that will be printing directly in chat is when you've forgotten to initialize the script.

Almost all functions will return an "error" value if something went wrong.

For example, if you try and define an attribute on a struct that doesn't exist, the function will return `false`;
You could then check for this value and call the `printRecentError` function.

```cpp
// !!! THIS CONTAINS AN ERROR ON PURPOSE !!!
defineStruct("Player");
bool success = defineStructAttribute("Playre", "name",  TYPE_STRING);

if (success == false) {
    printRecentError();
    // |00| Error: Unable to find struct for name [Playre] - Occurred at: defineStructAttribute('Playre', 'name')
}
```

All `structSet<type>` functions return a boolean value indicating whether the operation was successful or not.

All `structGet<type>` also return an error if something went wrong, but they have to adhere to the return type of the
function you're using. Below you can find the error values for each type. 

| Type     | Error Value      |
|----------|------------------|
| `int`    | `-1`             |
| `bool`   | `false`          |
| `float`  | `-1.0`           |
| `string` | `""`             |
| `vector` | `cInvalidVector` |

Because these values can just be the values you'd expect from your struct, you can use the
`MOST_RECENT_GET_FAILED` variable. This will be `true` if the last `structGet<type>` function failed.

---

# Larger example

Below is a larger example that shows how to use XsStructs to store player data for a non-existing RPG scenario.
By no means is this a complete example, but it should give you an idea of how to use the script and what potential
it has. 

Especially the helper functions like `playerHasAbility` and `playerAddXp` show very clearly how readable the code
remains when using structs.

```cpp
include "structs.xs";

void main() {
    initializeStructsScript();

    defineStruct("Player");
    defineStructAttribute("Player", "name",      TYPE_STRING);
    defineStructAttribute("Player", "level",     TYPE_INT);
    defineStructAttribute("Player", "xp",        TYPE_INT);
    defineStructAttribute("Player", "gold",      TYPE_FLOAT);
    defineStructAttribute("Player", "abilities", TYPE_STRING_ARRAY);
    defineStructAttribute("Player", "stats",     TYPE_STRUCT);

    defineStruct("Stats");
    defineStructAttribute("Stats", "hp", TYPE_INT);
    defineStructAttribute("Stats", "maxHp", TYPE_INT);
    defineStructAttribute("Stats", "attack", TYPE_INT);
    defineStructAttribute("Stats", "mArmor", TYPE_INT);
    defineStructAttribute("Stats", "pArmor", TYPE_INT);
    defineStructAttribute("Stats", "speed", TYPE_FLOAT);

    // ... read player stats (from file)

    vector player = new("Player");
    structSetString(player, "name", "Kirby");
    structSetInt(player, "level", 12);
    structSetInt(player, "xp", 0);
    structSetFloat(player, "gold", 471.10);

    int abilities = xsArrayCreateString(3, "", "p1-abilities");
    xsArraySetString(abilities, 0, "dash-lvl1");
    xsArraySetString(abilities, 1, "heal-lvl2");
    xsArraySetString(abilities, 2, "luck-lvl1");
    structSetInt(player, "abilities", abilities);

    vector stats = new("Stats");
    structSetInt(stats, "hp", 38);
    structSetInt(stats, "maxHp", 45);
    structSetInt(stats, "attack", 12);
    structSetInt(stats, "mArmor", 1);
    structSetInt(stats, "pArmor", 4);
    structSetFloat(stats, "speed", 1.12);
    structSetVector(player, "stats", stats);    
}

bool playerHasAbility(vector player = cInvalidVector, string ability = "") {
    if (ability == "" || isInstance(player, "Player") == false) {
        return (false);
    }

    int abilities = structGetInt(player, "abilities");
    for(i = 0; < xsArrayGetSize(abilities)) {
        if (xsArrayGetString(abilities, i) == ability) {
            return (true);
        }
    }

    return (false);
}

bool playerAddXp(vector player = cInvalidVector, int xp = 0) {
    if (isInstance(player, "Player") == false) {
        return (false);
    }

    int newXp = structGetInt(player, "xp") + xp;

    if (newXp >= 200) {
        structSetInt(player, "xp", newXp - 200);
        structSetInt(player, "level", structGetInt(player, "level") + 1);

        vector stats = structGetVector(player, "stats");

        structSetInt(stats, "maxHp", structGetInt(stats, "maxHp") + 1);
        structSetInt(stats, "attack", structGetInt(stats, "attack") + 1);
        structSetInt(stats, "mArmor", structGetInt(stats, "mArmor") + 1);
        structSetInt(stats, "pArmor", structGetInt(stats, "pArmor") + 1);
        structSetFloat(stats, "speed", structGetFloat(stats, "speed") + 0.03);
    } else {
        structSetInt(player, "xp", newXp);
    }
}
```