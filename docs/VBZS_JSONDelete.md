# VBZS_JSONDelete

Removes one or more keys, array elements, or paths from a JSON document.

`VBZS_JSONDelete` goes considerably further than a simple JSON delete operation. It can process multiple delete paths in a single call and supports wildcards for both object members and array elements.

Wildcards can be configured to delete either the first complete match or all complete matches.

This makes the function particularly useful for filtering API responses, sanitizing JSON before logging, removing sensitive information, reducing large JSON structures, and processing JSON whose object names or array sizes are not known in advance.

## Syntax

```text
VBZS_JSONDelete ( json ; siblings ; keyOrIndexOrPath { ; keyOrIndexOrPath ; ... } )
```

## Parameters

| Parameter | Description |
|---|---|
| `json` | The JSON object or array to process. |
| `siblings` | Controls wildcard behaviour. Use `0` or empty to delete only the first complete wildcard match for each supplied path. Use `1` or any other non-empty value to delete all complete wildcard matches. |
| `keyOrIndexOrPath` | A key, array index, JSON path, or wildcard path to delete. One or more paths may be supplied. |

At least one `keyOrIndexOrPath` parameter is required.

## Return value

Returns the resulting JSON as compact JSON text.

The order of remaining object keys is preserved.

If the supplied JSON is invalid, the function returns:

```text
Invalid JSON.
```

If one of the supplied paths is syntactically invalid, the function returns:

```text
Invalid JSON path: "path"
```

No deletion is performed when a supplied path is syntactically invalid.

---

# Basic usage

## Delete an object member

Given:

```json
{
  "name": "John",
  "email": "john@example.com",
  "age": 42
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "email"
)
```

Returns:

```json
{"name":"John","age":42}
```

## Delete multiple object members

Multiple paths can be supplied in the same function call.

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "email" ;
    "age"
)
```

Given:

```json
{
  "name": "John",
  "email": "john@example.com",
  "age": 42
}
```

Returns:

```json
{"name":"John"}
```

There is no need to call `VBZS_JSONDelete` repeatedly.

---

# Nested paths

Object keys can be addressed with dot-separated paths.

Given:

```json
{
  "customer": {
    "name": "John",
    "address": {
      "street": "Main Street",
      "city": "Amsterdam",
      "country": "NL"
    }
  }
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "customer.address.street"
)
```

Returns:

```json
{"customer":{"name":"John","address":{"city":"Amsterdam","country":"NL"}}}
```

---

# Arrays

Array elements can be addressed using zero-based indexes.

Given:

```json
{
  "colors": [
    "red",
    "green",
    "blue"
  ]
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "colors[1]"
)
```

Returns:

```json
{"colors":["red","blue"]}
```

Array indexes are zero-based:

```text
[0] = first element
[1] = second element
[2] = third element
```

Only non-negative integer indexes are valid.

Valid:

```text
[0]
[1]
[123]
```

Invalid:

```text
[-1]
[+1]
[1.5]
[abc]
```

---

# Paths through arrays

Object and array navigation can be freely combined.

Given:

```json
{
  "customers": [
    {
      "name": "Alice",
      "password": "secret1"
    },
    {
      "name": "Bob",
      "password": "secret2"
    }
  ]
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "customers[1].password"
)
```

Returns:

```json
{"customers":[{"name":"Alice","password":"secret1"},{"name":"Bob"}]}
```

---

# Flexible array path notation

VBZS accepts several equivalent forms around array indexes.

These paths are equivalent:

```text
customers[1].name
customers.[1].name
customers[1]name
customers.[1]name
```

For readability, this form is recommended:

```text
customers[1].name
```

The additional forms can be useful when paths are constructed dynamically in FileMaker calculations.

---

# Wildcards

`VBZS_JSONDelete` supports two wildcard types.

Object member wildcard:

```text
*
```

Array element wildcard:

```text
[*]
```

These two wildcard types are deliberately different.

An object wildcard matches object members only.

An array wildcard matches array elements only.

---

# Array wildcard

The array wildcard is written as:

```text
[*]
```

Suppose the JSON contains:

```json
{
  "customers": [
    {
      "name": "Alice",
      "password": "secret1"
    },
    {
      "name": "Bob",
      "password": "secret2"
    },
    {
      "name": "Carol",
      "password": "secret3"
    }
  ]
}
```

The path:

```text
customers[*].password
```

means:

> Search the elements of the `customers` array for complete paths ending in `password`.

The result depends on `siblings`.

## siblings = 0

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "customers[*].password"
)
```

Only the first complete wildcard match is removed.

Returns:

```json
{"customers":[{"name":"Alice"},{"name":"Bob","password":"secret2"},{"name":"Carol","password":"secret3"}]}
```

## siblings = 1

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "customers[*].password"
)
```

All complete wildcard matches are removed.

Returns:

```json
{"customers":[{"name":"Alice"},{"name":"Bob"},{"name":"Carol"}]}
```

This is useful for removing information such as passwords, tokens, internal identifiers, or temporary metadata from every object in an array.

---

# Object member wildcard

The object member wildcard is written as:

```text
*
```

Given:

```json
{
  "departments": {
    "sales": {
      "name": "Sales",
      "internalCode": "S-100"
    },
    "support": {
      "name": "Support",
      "internalCode": "P-200"
    },
    "finance": {
      "name": "Finance",
      "internalCode": "F-300"
    }
  }
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "departments.*.internalCode"
)
```

Returns:

```json
{"departments":{"sales":{"name":"Sales"},"support":{"name":"Support"},"finance":{"name":"Finance"}}}
```

The dynamically named members `sales`, `support`, and `finance` do not need to be known beforehand.

---

# Wildcards are not recursive

A wildcard operates only at its position in the supplied path.

The path:

```text
*.password
```

does not mean:

> Find every key named `password` anywhere in the JSON.

It means:

> For each root-level object member, check whether that member has a direct child named `password`.

Likewise:

```text
customers[*].password
```

means:

> For each element of the `customers` array, check for its direct child named `password`.

It does not automatically search deeper levels.

For example:

```json
{
  "customers": [
    {
      "account": {
        "password": "secret"
      }
    }
  ]
}
```

This path does not match:

```text
customers[*].password
```

The correct path is:

```text
customers[*].account.password
```

---

# Combining object and array wildcards

Object and array wildcards can be combined in the same path.

Consider:

```json
{
  "regions": {
    "north": {
      "customers": [
        {
          "name": "Alice",
          "password": "secret1"
        },
        {
          "name": "Bob",
          "password": "secret2"
        }
      ]
    },
    "south": {
      "customers": [
        {
          "name": "Carol",
          "password": "secret3"
        }
      ]
    }
  }
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "regions.*.customers[*].password"
)
```

Returns:

```json
{"regions":{"north":{"customers":[{"name":"Alice"},{"name":"Bob"}]},"south":{"customers":[{"name":"Carol"}]}}}
```

A single path has removed `password` from every customer, even though neither the region names nor the number of customers needed to be known beforehand.

---

# Multiple wildcard paths

Multiple deletion rules can be applied in one call.

Given:

```json
{
  "users": [
    {
      "name": "Alice",
      "email": "alice@example.com",
      "password": "secret1",
      "token": "ABC123"
    },
    {
      "name": "Bob",
      "email": "bob@example.com",
      "password": "secret2",
      "token": "XYZ789"
    }
  ],
  "metadata": {
    "internalId": "123456",
    "createdBy": "admin"
  }
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "users[*].password" ;
    "users[*].token" ;
    "metadata.internalId"
)
```

Returns:

```json
{"users":[{"name":"Alice","email":"alice@example.com"},{"name":"Bob","email":"bob@example.com"}],"metadata":{"createdBy":"admin"}}
```

This is substantially simpler than iterating over the array and repeatedly modifying the JSON from FileMaker.

---

# Removing complete array elements

The array wildcard can identify the array elements themselves.

Given:

```json
{
  "temporary": [
    {
      "id": 1
    },
    {
      "id": 2
    },
    {
      "id": 3
    }
  ],
  "status": "ready"
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "temporary[*]"
)
```

Returns:

```json
{"temporary":[],"status":"ready"}
```

The `temporary` array remains, but all its elements are removed.

To remove the complete `temporary` member instead:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "temporary"
)
```

Returns:

```json
{"status":"ready"}
```

---

# First match versus all matches

The `siblings` parameter matters primarily when a supplied path contains a wildcard.

## siblings = 0

For each supplied wildcard path:

```text
find the first complete match
delete that match
stop resolving that path
```

For object wildcards, the first match follows the original order of the object members in the supplied JSON.

For array wildcards, the search starts at array index `0`.

## siblings = 1

For each supplied wildcard path:

```text
find every complete match
delete all matches
```

For explicit paths without wildcards, `siblings` makes no practical difference because an explicit path can identify at most one concrete location.

---

# Complete wildcard matches

A wildcard is resolved as part of the complete path.

Given:

```json
{
  "objects": {
    "a": {
      "value": 1
    },
    "b": {
      "other": 2
    },
    "c": {
      "value": 3
    }
  }
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "objects.*.value"
)
```

The wildcard does not simply select the first object member.

VBZS looks for the first object member for which the complete path ends successfully in `value`.

The first complete match is:

```text
objects.a.value
```

With:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "objects.*.value"
)
```

both of these are removed:

```text
objects.a.value
objects.c.value
```

The member `objects.b` does not match because it has no `value` child.

---

# Stable multiple array deletions

Deleting multiple indexes from an array can be error-prone because removing one element changes the indexes of elements that follow it.

`VBZS_JSONDelete` avoids that problem by first resolving every requested path against the original JSON.

Array deletions are then applied from higher indexes to lower indexes.

Given:

```json
{
  "items": [
    "zero",
    "one",
    "two",
    "three",
    "four"
  ]
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "items[1]" ;
    "items[3]"
)
```

Returns:

```json
{"items":["zero","two","four"]}
```

The values originally at indexes `1` and `3` are removed.

The removal of index `1` does not cause the second delete path to accidentally target another element.

---

# Overlapping paths

Delete paths may overlap.

Given:

```json
{
  "customer": {
    "name": "Alice",
    "address": {
      "city": "Amsterdam",
      "country": "NL"
    }
  }
}
```

This call is valid:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "customer.address.city" ;
    "customer.address"
)
```

Returns:

```json
{"customer":{"name":"Alice"}}
```

Once the complete `address` object is scheduled for deletion, separately deleting `address.city` becomes unnecessary.

`VBZS_JSONDelete` combines all resolved delete paths into one deletion plan before modifying the JSON.

---

# Order of operations

`VBZS_JSONDelete` does not execute every delete parameter immediately.

Conceptually, processing occurs in this order:

1. Parse the JSON.
2. Validate every supplied path.
3. Resolve every supplied path against the original JSON.
4. Build one combined deletion plan.
5. Apply the deletion plan to a copy of the original JSON.
6. Return the resulting compact JSON.

This design is particularly important when working with arrays or overlapping delete paths.

Every requested path is therefore interpreted against the same original JSON structure.

---

# Missing paths

A syntactically valid path that does not exist is not an error.

Given:

```json
{
  "name": "Alice"
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "address.city"
)
```

Returns:

```json
{"name":"Alice"}
```

Nothing is deleted because no complete match exists.

A missing path is therefore different from an invalid path.

---

# Invalid wildcard syntax

A wildcard must occupy an entire path segment.

Valid object wildcard:

```text
customers.*.name
```

Valid array wildcard:

```text
customers[*].name
```

Invalid examples:

```text
customers.na*e
customers.name*
customers.*name
customers[1*]
customers[*1]
customers[**]
```

Other malformed paths include:

```text
.name
customers..
customers.
```

If an invalid path is supplied, the function returns:

```text
Invalid JSON path: "the-invalid-path"
```

No deletions are performed.

---

# Invalid JSON

When the first parameter does not contain valid JSON, the function returns:

```text
Invalid JSON.
```

JSON validation occurs before path validation.

---

# Example: preparing an API response

Suppose an API returns:

```json
{
  "customer": {
    "id": 1001,
    "name": "Alice",
    "internal": {
      "databaseId": 83921,
      "syncToken": "abc-123"
    },
    "orders": [
      {
        "id": 501,
        "total": 125.5,
        "audit": {
          "createdBy": "system",
          "internalReference": "REF-001"
        }
      },
      {
        "id": 502,
        "total": 42.0,
        "audit": {
          "createdBy": "system",
          "internalReference": "REF-002"
        }
      }
    ]
  }
}
```

The FileMaker solution needs the customer and order information, but not the internal or audit data.

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "customer.internal" ;
    "customer.orders[*].audit"
)
```

Returns:

```json
{"customer":{"id":1001,"name":"Alice","orders":[{"id":501,"total":125.5},{"id":502,"total":42.0}]}}
```

One calculation has removed both a nested object and an object from every element of an array.

---

# Example: sanitizing JSON before logging

Consider:

```json
{
  "request": {
    "endpoint": "/customers",
    "token": "SECRET-TOKEN"
  },
  "response": {
    "status": 200,
    "customers": [
      {
        "name": "Alice",
        "email": "alice@example.com",
        "password": "secret1"
      },
      {
        "name": "Bob",
        "email": "bob@example.com",
        "password": "secret2"
      }
    ]
  }
}
```

Before writing this JSON to a diagnostic log:

```filemaker
Set Variable [
    $safeJSON ;
    Value:
        VBZS_JSONDelete (
            $json ;
            1 ;
            "request.token" ;
            "response.customers[*].password"
        )
]
```

Returns:

```json
{"request":{"endpoint":"/customers"},"response":{"status":200,"customers":[{"name":"Alice","email":"alice@example.com"},{"name":"Bob","email":"bob@example.com"}]}}
```

The useful structure remains available for diagnostics while selected sensitive values have been removed.

---

# Example: dynamically named objects

Suppose identifiers are used as object member names:

```json
{
  "devices": {
    "device-A": {
      "name": "Scanner 1",
      "secret": "AAA"
    },
    "device-B": {
      "name": "Scanner 2",
      "secret": "BBB"
    },
    "device-C": {
      "name": "Scanner 3",
      "secret": "CCC"
    }
  }
}
```

The device identifiers are not known in advance.

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "devices.*.secret"
)
```

Returns:

```json
{"devices":{"device-A":{"name":"Scanner 1"},"device-B":{"name":"Scanner 2"},"device-C":{"name":"Scanner 3"}}}
```

Without the object wildcard, the FileMaker solution would first have to enumerate the keys and construct separate paths for each device.

---

# Example: deeply mixed dynamic structures

The function becomes particularly useful when object member names and array sizes are both unknown.

Given:

```json
{
  "companies": {
    "north": {
      "employees": [
        {
          "name": "Alice",
          "private": {
            "token": "111",
            "note": "internal"
          }
        },
        {
          "name": "Bob",
          "private": {
            "token": "222",
            "note": "internal"
          }
        }
      ]
    },
    "south": {
      "employees": [
        {
          "name": "Carol",
          "private": {
            "token": "333",
            "note": "internal"
          }
        }
      ]
    }
  }
}
```

One option is to remove selected properties:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "companies.*.employees[*].private.token" ;
    "companies.*.employees[*].private.note"
)
```

Returns:

```json
{"companies":{"north":{"employees":[{"name":"Alice","private":{}},{"name":"Bob","private":{}}]},"south":{"employees":[{"name":"Carol","private":{}}]}}}
```

If the complete `private` object is unnecessary, a simpler calculation is:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "companies.*.employees[*].private"
)
```

Returns:

```json
{"companies":{"north":{"employees":[{"name":"Alice"},{"name":"Bob"}]},"south":{"employees":[{"name":"Carol"}]}}}
```

The second version expresses the intention more directly when the complete object may be removed.

---

# Example: remove the first match only

The `siblings` parameter can be useful when you deliberately want to remove only one match.

Given:

```json
{
  "queue": [
    {
      "status": "temporary",
      "value": "A"
    },
    {
      "status": "temporary",
      "value": "B"
    },
    {
      "status": "temporary",
      "value": "C"
    }
  ]
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    0 ;
    "queue[*].status"
)
```

Returns:

```json
{"queue":[{"value":"A"},{"status":"temporary","value":"B"},{"status":"temporary","value":"C"}]}
```

Only the first complete wildcard match is removed.

With:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "queue[*].status"
)
```

the result becomes:

```json
{"queue":[{"value":"A"},{"value":"B"},{"value":"C"}]}
```

---

# Example: several independent transformations

A single call may contain paths affecting entirely different parts of the JSON.

Given:

```json
{
  "session": {
    "token": "ABC",
    "user": "Alice"
  },
  "customers": [
    {
      "name": "Customer A",
      "internalId": 100
    },
    {
      "name": "Customer B",
      "internalId": 200
    }
  ],
  "debug": {
    "requestId": "XYZ",
    "duration": 150
  }
}
```

Calculation:

```filemaker
VBZS_JSONDelete (
    $json ;
    1 ;
    "session.token" ;
    "customers[*].internalId" ;
    "debug"
)
```

Returns:

```json
{"session":{"user":"Alice"},"customers":[{"name":"Customer A"},{"name":"Customer B"}]}
```

This is often easier to maintain than a chain of separate JSON delete calculations.

---

# Recommended use cases

`VBZS_JSONDelete` is particularly suitable for:

- Removing internal fields from API responses.
- Sanitizing JSON before writing it to logs.
- Removing tokens, passwords, secrets, or transient metadata.
- Reducing JSON before storing it in FileMaker fields.
- Preparing payloads for other web services.
- Removing properties from every element in an array.
- Processing objects whose member names are not known beforehand.
- Deleting several unrelated paths in one operation.
- Safely deleting several array indexes without index-shift problems.
- Filtering deeply nested structures containing both objects and arrays.
- Replacing FileMaker loops that only exist to delete repeated JSON properties.

---

# Notes

- Returned JSON is compact and contains no formatting whitespace.
- The order of remaining object members is preserved.
- Array indexes are zero-based.
- Array indexes must be non-negative integers.
- The object wildcard matches object members only.
- The array wildcard matches array elements only.
- Wildcards are not recursive.
- Wildcards must occupy complete path segments.
- With `siblings = 0`, only the first complete wildcard match for each path is removed.
- With `siblings = 1`, all complete wildcard matches for each path are removed.
- `siblings` has no practical effect on explicit paths without wildcards.
- Missing but syntactically valid paths are ignored.
- Invalid path syntax returns an error message and causes no deletions.
- All paths are resolved against the original JSON before any deletion takes place.
- Array deletions are applied from higher indexes to lower indexes to prevent index shifting.
- Overlapping deletion paths are combined into one deletion plan.
- Invalid JSON and invalid JSON paths are returned as readable function results rather than FileMaker plug-in errors.

---

# See also

Depending on the task, the following FileMaker functions may also be useful:

- `JSONDeleteElement`
- `JSONGetElement`
- `JSONListKeys`
- `JSONSetElement`

`VBZS_JSONDelete` is most useful when a deletion involves multiple paths, arrays, wildcard matching, or dynamic JSON structures that would otherwise require iteration or repeated JSON operations in FileMaker.