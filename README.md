# Documentation

The following modules, examples, and tests have been conducted under the following expectations and limitations:

- The modules are designed for Lua 5.1 and should work in any Lua version, but some features may not work as intended - or at all - on other versions.
- The `DataSerializer` module is designed to be used in the same directory as the module `ArgumentBuilder` - and will not work out of the box without this requirement being met.
- No other libraries or dependencies are required to run these modules. They are designed for a normal Lua environment.

## ArgumentBuilder Module

The ArgumentBuilder module provides utilities for building and validating function arguments in Lua. It ensures that the arguments passed to a function meet the expected criteria, such as type and presence.

### Functions

- **BuildArguments(Options)**: Constructs a function that validates and processes arguments based on the provided options.

  - Options: A table containing configuration for argument validation. Key fields include:
    - Expected: A table defining the expected arguments.
    - TypeCheck: A boolean indicating whether to perform type checking (default is true).
    - SetDefaults: A boolean indicating whether to set default values for missing arguments (default is false).
    - ForceType: A boolean indicating whether to force the argument to the expected type if it does not match (default is false).

- **Cleanse(Value, Object, ForceType)**: Validates and optionally coerces a value to match the expected type.

  - Value: The value to validate.
  - Object: A table defining the expected type and default value.
  - ForceType: A boolean indicating whether to coerce the value to the expected type if it does not match.

### Example Usage

```lua
local ArgumentBuilder = require("ArgumentBuilder")

local function Example1(...)
  local validator = ArgumentBuilder.BuildArguments({
    Expected = { Name = { Type = "string" }, Age = { Type = "number" } },
    TypeCheck = true,
    SetDefaults = false
  })

  local args = validator({
    Name = "John",
    Age = 30
  })
  
  print(args.Name, args.Age) -- Successfully prints both, as they match the types and are not missing.
end

local function Example2(...)
  local validator = ArgumentBuilder.BuildArguments({
    Expected = { Name = { Type = "string" }, Age = { Type = "number", Value = 25 } },
    TypeCheck = true,
    SetDefaults = true
  })

  local args = validator({
    Name = "John"
  })
  
  print(args.Name, args.Age) -- Prints "John" and 25, as the 'Age' argument is missing, and SetDefaults is true.
end

local function Example3(...)
  local validator = ArgumentBuilder.BuildArguments({
    Expected = { Name = { Type = "string" }, Age = { Type = "number" } },
    TypeCheck = true,
    ForceType = true
  })

  local args = validator({
    Name = "John",
    Age = "30"
  })
  
  print(args.Name, args.Age) -- Prints "John" and 30, as the 'Age' argument is coerced to a number by "ForceType".
end
```

## DataSerializer Module

The `DataSerializer` module provides functions for serializing Lua data types (strings, numbers, functions, and tables) into a string representation that can be stored or transmitted.

### Functions

- **SerializeString(Value)**: Serializes a string, escaping special characters.

  - `Value`: The string to serialize.

- **SerializeNumber(Value)**: Serializes a number into a string representation.

  - `Value`: The number to serialize.

- **SerializeFunction(Value)**: Serializes a function into a loadable string representation using `loadstring`.

  - `Value`: The function to serialize.

- **SerializeTable(Value, Compress, Level)**: Serializes a table into a string representation, with options for compression and indentation level.

  - `Value`: The table to serialize.
  - `Compress`: A boolean indicating whether to compress the output via eliminating whitespace (default is `false`).
  - `Level`: The starting indentation level (default is `1`).

- **SerializeAny(Value, Compress)**: Serializes any Lua data type by automatically selecting the appropriate serialization function.

  - `Value`: The value to serialize.
  - `Compress`: A boolean indicating whether to compress the output via eliminating whitespace (applies to tables).

### Example Usage

```lua
local DataSerializer = require("DataSerializer")

local myTable = { name = "John", age = 30 }
local serialized = DataSerializer.SerializeAny({ Value = myTable, Compress = false })

print(serialized)
```

### Serializer Performance

The `DataSerializer` module is designed for performance and efficiency, with minimal overhead. It is designed to be used in performance-critical applications where speed and memory are crucial.

The following graph illustrates the performance of the `DataSerializer` module across different Lua data types: "string", "table", "function", and "number". Each data type was serialized 100,000 iterations to measure the time taken for serialization. This is using the default settings (no compression).

| Data Type | Iterations | Time Taken (seconds) |
|-----------|------------|----------------------|
| String    | 100,000    | 0.31                 |
| Table     | 100,000    | 2.35                 |
| Function  | 100,000    | 4.99                 |
| Number    | 100,000    | 0.19                 |

These results demonstrate the efficiency of the `DataSerializer` module, with relatively quick serialization times across various data types. This makes it suitable for performance-critical applications where speed and memory usage are of concern.
