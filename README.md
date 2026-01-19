# Variable Scheme Validator (GameMaker / GML)

This script provides a **generic recursive validation function** for GameMaker projects.  
It allows you to validate variables against a defined **schema**, similar to a lightweight JSON schema system.

Supported features:

- Primitive type validation  
- Min / Max constraints  
- Multiple allowed types  
- Constant value constraints  
- Recursive validation for structs and arrays  
- Optional custom error messages  

---
## Function

```gml
/// @desc Check if a variable matches a given scheme
/// @param {any} _value   The value to validate
/// @param {any} _scheme  The validation scheme (struct or stringified JSON)
/// @return {bool}        True if validation passed, false otherwise
function variable_check_scheme(_value, _scheme)
```

## Types

| Type Name   | Description                      |
| ----------- | -------------------------------- |
| `string`    | Must be a string                 |
| `numeric`   | Must be a real number            |
| `boolean`   | Must be a boolean                |
| `callable`  | Must be a function / script      |
| `undefined` | Must be undefined                |
| `base_64`   | Must be valid Base64             |
| `datetime`  | Must be ISO-8601 datetime string |
| `guid`      | Must be a valid GUID / UUID      |
| `any`       | Always valid                     |

### Basic Example with json_stringify:
```gml
var value = "Hello";
var scheme = json_stringify({type: "string", min: 1, max: 10});

var ok = variable_check_scheme(value, scheme);
```

### Basic Example (as stringified json):
```gml
var value = "Hello";
var scheme = "{\"type\":\"string\", \"min\":1, \"max\":10}"; // is a stringified json

var ok = variable_check_scheme(value, scheme);
```

### Multiple Allowed Types
```gml
var value  = "Hello";
var scheme = json_stringify({ "type":"string|numeric" });

var ok = variable_check_scheme(value, scheme);
```

### Numeric Range Example
```gml
var value  = 42;
var scheme = json_stringify({ "type":"numeric", "min":0, "max":100 });

var ok = variable_check_scheme(value, scheme);

```

### Constant Value Constraint
```gml
var value  = "red";
var scheme = json_stringify({ "type":"string", "const":"red,green,blue" });

var ok = variable_check_scheme(value, scheme);
```

### Deep Struct Example

```gml
var _player_scheme = { 
    data: [
        {
           id: json_stringify({type: "numeric", min:1, message: "id must be > 0"}),
           playerName: json_stringify({type: "string", min: 3, max: 16, message: "playerName length must be 3-16"}),
           playerNamecolorID: json_stringify({type: "numeric", min: 0, max: 10, message: "playerNamecolorID must be 0-10"}),
           playerAvatarID: json_stringify({type: "numeric", min: 0, max: 54, message: "playerAvatarID must be 0-54"}),
           playerDescription: json_stringify({type: "string", min: 0, max: 64, message: "playerDescription length must be 0-64"}),
           playerCountryCode: json_stringify({type: "string", min: 2, max: 2, message: "playerCountryCode must be 2 letters"}),
           views: json_stringify({type: "numeric", min: 0, message: "views must be >= 0"}),
           downloads: json_stringify({type: "numeric", min: 0, message: "downloads must be >= 0"}),
           sales: json_stringify({type: "numeric", min: 0, message: "sales must be >= 0"}),
           engagements: json_stringify({type: "numeric", min: 0, message: "engagements must be >= 0"}),
           ratings: json_stringify({type: "numeric", min: 0, message: "ratings must be >= 0"}),
           avgRating: json_stringify({type: "numeric", min: 0, max: 5, message: "avgRating must be 0-5"}),
           createdAt: json_stringify({type: "datetime|undefined", message: "createdAt must be datetime"}),
           guid_example: json_stringify({type: "guid", message: "guid_example must be a guid"}),
    } ]
}

var _http_player_response = {
    "data": [
        {
             "id": 5,
             "playerName": "Glim888v2",
             "playerAvatarID": 2,
             "playerNamecolorID": 10,
             "playerDescription": "test12345",
             "playerCountryCode": "DE",
             "views": 150006,
             "downloads": 150001,
             "sales": 150,
             "engagements": 1000001,
             "ratings": 251,
             "avgRating": 4.8,
             "createdAt": "2025-12-23T00:00:00.000Z",
             "guid_example": "07a9b9a9-275f-4ecd-9dc9-d146acbe0a38"
        }, 
        {
             "id": 5,
             "playerName": "Glim888v2",
             "playerAvatarID": 2,
             "playerNamecolorID": 10,
             "playerDescription": "test12345",
             "playerCountryCode": "DE",
             "views": 150006,
             "downloads": 150001,
             "sales": 150,
             "engagements": 1000001,
             "ratings": 251,
             "avgRating": 4.8,
             "createdAt": undefined,
             "guid_example": "07a9b9a9-275f-4ecd-9dc9-d146acbe0a38"
        }
    ]
}

var _ok = variable_check_scheme(_http_player_response, _player_scheme);
show_debug_message(_ok)
```
### Custom Error Message Example

```gml
var scheme = json_stringify({
    "type":"string",
    "min":5,
    "message":"Name must be at least 5 characters long!"
});
```

### Notes

- Struct and array validation is recursive.
- JSON scheme must contain at least "type".
- "min" and "max" are optional.
- If multiple types are given, the first successful one passes.

