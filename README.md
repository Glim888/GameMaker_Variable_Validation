# Variable Scheme Validator (GameMaker / GML)

This script provides a generic **recursive validation function** for GameMaker projects.  
It allows you to validate variables against a defined **schema**, similar to lightweight JSON schema validation.

The function supports:
- Primitive type validation
- Min / Max constraints
- Multiple allowed types
- Recursive validation for structs and arrays

---

## Function

```gml
/// @desc Check if a variable matches a given scheme
/// @param {any} _value   The value to validate
/// @param {any} _scheme  The validation scheme (expects a stringified json)
/// @return {bool}        True if validation passed, false otherwise
function variable_check_scheme(_value, _scheme)
```

## Types

| Type Name   | Description                 |
| ----------- | --------------------------- |
| `string`    | Must be a string            |
| `numeric`   | Must be a number            |
| `boolean`   | Must be a boolean           |
| `callable`  | Must be a function / script |
| `undefined` | Must be undefined           |

### Basic Example with JSON_parse:
```gml
var value = "Hello";
var scheme = json_parse({type: string, min: 1, max: 10});

var ok = variable_check_scheme(value, scheme);
```

### Basic Example :
```gml
var value = "Hello";
var scheme = "{\"type\":\"string\", \"min\":1, \"max\":10}"; // is a stringified json, you also could do it as in the 1. example

var ok = variable_check_scheme(value, scheme);
```

### Multiple Allowed Types
```gml
var value = "Hello";
var scheme = "{\"type\":\"string|numeric\", \"min\":1, \"max\":10}"; // is a stringified json, you also could do it as in the 1. example

var ok = variable_check_scheme(value, scheme);
```

### Numeric Range Example
```gml
var value = 42;
var scheme = "{\"type\":\"numeric\", \"min\":0, \"max\":100}"; // is a stringified json, you also could do it as in the 1. example
```

### Deep Struct Example

```gml
var _data = {
    text: 3,
    number: 1,
    arr2: [
        {
            t1: "t1",
            nu: 5
        }
    ],
    str: {
        text2: "be8888e",
        number2: 4,
        struct2: {
            boolean: false,
            struct3: {
                call: void,
                boolean: true,
                numb: 4,
                t: "c",
                b2: "g",
                optional:undefined
            }
        }
    }
}

// is a stringified json, you also could do it as in the 1. example
var _validation = {
    text: "{\"type\":\"string|numeric\"}",
    number: "{\"type\":\"numeric\"}",
    arr2: [
        {
            t1: "{\"type\":\"string\"}",
            nu: "{\"type\":\"numeric\",}"
        }
    ],
    str: {
        text2: "{\"type\":\"string\",\"min\":\"1\",\"max\":\"10\"}",
        number2: "{\"type\":\"numeric\",\"min\":\"1\",\"max\":\"10\"}",
        struct2: {
            boolean: "{\"type\":\"boolean\"}",
            struct3: {
                call: "{\"type\":\"callable\"}",
                boolean: "{\"type\":\"boolean\"}",
                numb: "{\"type\":\"numeric\"}",
                t: "{\"type\":\"string\"}",
                b2: "{\"type\":\"string\"}",
                optional: "{\"type\":\"numeric|undefined\"}",
            }
        }
    } 
}

variable_check_scheme(_data, _validation)
```

### Notes

- Struct and array validation is recursive.
- JSON scheme must contain at least "type".
- "min" and "max" are optional.
- If multiple types are given, the first successful one passes.

