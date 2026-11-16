# VBZS_JSONParameters

## Description

`VBZS_JSONParameters` creates FileMaker variables from the contents of a JSON object or JSON array.

The function is intended as a structured alternative to constructions where `Evaluate` and `Let` are used to create variables from calculation text such as:

```text
$var1 = 15;
$var2 = "Some value";
```

Instead of constructing FileMaker calculation code, structured data can be passed as JSON. `VBZS_JSONParameters` then creates the corresponding FileMaker variables when the data is needed.

The function supports:

- root-level JSON objects;
- root-level JSON arrays;
- local FileMaker variables;
- optional namespaces;
- FileMaker repeating variables when processing arrays;
- global variables when explicitly requested.

## Syntax

```text
VBZS_JSONParameters ( json ; i ; namespace )
```

## Parameters

### `json`

A valid JSON object or JSON array to process.

For a JSON object, the keys at the supplied level are converted to FileMaker variable names and the corresponding JSON values are assigned to those variables.

A root-level JSON array receives special processing and can be converted into indexed variables and FileMaker repeating variables.

### `i`

Reserved for future use.

This parameter must currently be left empty:

```text
""
```

### `namespace`

An optional prefix added to the FileMaker variables created by the function.

For example:

```text
customer_
```

combined with the JSON keys:

```text
id
name
```

creates:

```text
$customer_id
$customer_name
```

Namespaces are particularly useful in larger scripts where several JSON objects may contain identical key names.

## Result

The primary result of `VBZS_JSONParameters` is the set of FileMaker variables it creates.

When processing completes successfully, the function itself returns an empty result.

If an error occurs, the function returns:

```text
?
```

For example, invalid JSON causes the function to return `?`.

This follows the familiar FileMaker convention of returning `?` when a calculation cannot produce a valid result.

## Processing a JSON object

Given:

```json
{
    "id": 5678,
    "name": "Acme BV"
}
```

the following calculation:

```filemaker
VBZS_JSONParameters (
    json ;
    "" ;
    ""
)
```

creates:

```text
$id   = 5678
$name = Acme BV
```

The JSON keys become FileMaker variable names.

## Using namespaces

The same JSON can be loaded using:

```filemaker
VBZS_JSONParameters (
    json ;
    "" ;
    "customer_"
)
```

which creates:

```text
$customer_id   = 5678
$customer_name = Acme BV
```

A namespace is especially useful when multiple JSON structures are processed within the same script or script chain.

For example, an order and a customer may both contain:

```text
id
name
```

Without namespaces, one set of variables could overwrite the other.

Using:

```text
customer_
order_
```

produces separate variables such as:

```text
$customer_id
$customer_name

$order_id
$order_name
```

## Variable scope

A normal namespace creates local FileMaker variables.

For example:

```text
namespace = "customer_"
```

creates:

```text
$customer_id
$customer_name
```

Global variables can also be created when required.

Prefix the namespace itself with `$`:

```text
namespace = "$customer_"
```

and the resulting variables are global FileMaker variables instead of local variables.

This allows the same namespacing mechanism to be used for either variable scope.

## Nested objects and arrays

Nested objects and arrays encountered while processing a normal JSON object are deliberately not automatically expanded.

Consider:

```json
{
    "id": 1234,
    "customer": {
        "id": 5678,
        "name": "Acme BV"
    }
}
```

Automatically traversing the complete JSON structure could result in several objects attempting to create:

```text
$id
```

There would be no reliable way to determine which value should own that variable without automatically inventing namespaces.

If a contained object or array needs to be converted to variables, pass that object or array separately to `VBZS_JSONParameters`, preferably using an appropriate namespace.

Once an array itself is supplied as the root value, the function applies its special array processing as described below.

This keeps variable scope and naming under the control of the FileMaker developer.

## Root-level JSON arrays

A root-level JSON array is a deliberate exception to the non-recursive behaviour.

Arrays receive special processing because they commonly represent lists of similar objects, such as order lines returned by an API.

Given:

```json
[
    {
        "test": "value"
    },
    {
        "test": "value #2"
    },
    {
        "test": "value #3"
    }
]
```

and:

```text
namespace = "demo_"
```

the array is first split into individual variables.

The complete JSON objects are stored using zero-based array indexes:

```text
$demo_0 = {"test":"value"}
$demo_1 = {"test":"value #2"}
$demo_2 = {"test":"value #3"}
```

Each array element is subsequently processed as a variable list.

The `test` values therefore become a FileMaker repeating variable:

```text
$demo_test
$demo_test[2]
$demo_test[3]
```

containing:

```text
value
value #2
value #3
```

This recursive processing is specific to the handling of a root-level array. It does not cause arbitrary nested objects and arrays to be recursively traversed.

## Array indexing and FileMaker repetitions

There are two different numbering conventions involved when processing an array.

The complete JSON array elements use JSON-style zero-based numbering:

```text
$demo_0
$demo_1
$demo_2
```

The extracted values use FileMaker repeating variables:

```text
$demo_test
$demo_test[2]
$demo_test[3]
```

The first repetition in FileMaker can be referenced without explicitly specifying `[1]`.

The first representation therefore corresponds to the original JSON array indexing, while the second follows FileMaker's normal repetition behaviour.

## `max_repetition`

Whenever a JSON array is processed, `VBZS_JSONParameters` creates:

```text
$max_repetition
```

This variable contains the number of objects in the root-level array that was just processed.

If a namespace is supplied, a namespaced version is created as well.

For example:

```text
namespace = "demo_"
```

with three array objects creates:

```text
$max_repetition      = 3
$demo_max_repetition = 3
```

The non-namespaced `$max_repetition` is always created deliberately.

This allows a script to process the array immediately after calling `VBZS_JSONParameters` without needing to take the namespace into account when determining the number of array objects.

The namespaced version remains available as part of the particular dataset.

## Processing variable-length arrays

`$max_repetition` is particularly useful when processing data returned by an external API.

An order, for example, might contain only one order line or a large number of order lines.

After loading the JSON:

```filemaker
VBZS_JSONParameters (
    orderLines ;
    "" ;
    "order_"
)
```

the generated variables may resemble:

```text
$order_article
$order_article[2]
$order_article[3]

$order_quantity
$order_quantity[2]
$order_quantity[3]

$order_price
$order_price[2]
$order_price[3]
```

while:

```text
$max_repetition
```

contains the number of objects in the current JSON array.

A script can therefore process repetitions:

```text
1 ... $max_repetition
```

without having to know the size of the order beforehand.

## Avoiding artifacts from earlier arrays

`$max_repetition` also defines how much of the repeating variable structure belongs to the array that was most recently processed.

This is important because variables or repetitions created during an earlier operation may still exist in the current script context.

For example, processing an array with ten elements may create repetitions through:

```text
$order_article[10]
```

If another array containing only three objects is subsequently loaded, values from the earlier operation may still exist at higher repetition numbers.

The newly created:

```text
$max_repetition = 3
```

indicates that only repetitions 1 through 3 belong to the current array.

> **Important:** When processing an array, use `$max_repetition` to determine how many objects and corresponding repetitions belong to the current result.

## Passing structured data between scripts

JSON provides a convenient way to pass structured data between FileMaker scripts without requiring global variables as the transport mechanism.

A script can receive JSON through:

```filemaker
Get ( ScriptParameter )
```

and convert it into appropriately namespaced variables:

```filemaker
VBZS_JSONParameters (
    Get ( ScriptParameter ) ;
    "" ;
    "order_"
)
```

This may create:

```text
$order_id
$order_customer
$order_total
```

A subscript can return another JSON object, after which the calling script can process the returned structure using:

```filemaker
VBZS_JSONParameters (
    Get ( ScriptResult ) ;
    "" ;
    "result_"
)
```

This allows structured data to be transferred between scripts as JSON and converted to local FileMaker variables only where those variables are needed.

## Converting JSON keys to FileMaker variable names

JSON key names may contain characters that are unsuitable for FileMaker variable names.

`VBZS_JSONParameters` therefore sanitizes the keys before creating variables.

The following characters are converted to an underscore:

```text
, + - * / \ ^ & = ≠ > < [ ] { } ; : $
```

Multiple consecutive underscores are reduced to a single underscore.

For example, a conversion that would otherwise produce:

```text
name___value
```

is normalized to:

```text
name_value
```

## Parentheses and repetitions

Parentheses receive special treatment.

```text
(  →  .
)  →  .
```

This is particularly relevant when processing JSON originating from the FileMaker Data API, where parentheses can indicate repetitions.

Additional normalization is performed on combinations that may already exist in a JSON key or may be created during key conversion.

The following combinations:

```text
_.
._
..
```

are normalized to:

```text
.
```

This prevents intermediate conversions from leaving unnecessary combinations of underscores and periods in the resulting FileMaker variable name.

## Maximum root-level elements

The current implementation supports a maximum of:

```text
999
```

elements at the root level.

For a JSON object, this means a maximum of 999 root-level keys.

For a root-level JSON array, this means a maximum of 999 objects.

If the supplied JSON exceeds this limit, the function does not process it.

> **Important:** `VBZS_JSONParameters` currently supports a maximum of 999 root-level elements.

Nested structures do not increase the root-level key count of an ordinary JSON object because they are not automatically expanded.

## Error handling

A successful call returns an empty result:

```text
""
```

If processing fails, the function returns:

```text
?
```

Invalid JSON is one example of input that results in `?`.

The result can therefore be checked when a script needs to determine whether the supplied JSON was successfully processed.

For example:

```filemaker
Set Variable [
    $result ;
    Value: VBZS_JSONParameters (
        Get ( ScriptParameter ) ;
        "" ;
        "order_"
    )
]

If [ $result = "?" ]
    # JSON could not be processed
End If
```

## Notes

- The supplied JSON must be a valid JSON object or JSON array.
- The `i` parameter is reserved and must currently be left empty.
- A successful call returns an empty result.
- An error returns `?`.
- JSON keys are converted into FileMaker variable names.
- Namespaces help prevent variables from unrelated JSON structures from overwriting each other.
- Namespaces normally create local `$variables`.
- Starting the namespace with `$` creates global `$$variables`.
- Nested objects and arrays encountered in a normal JSON object are not automatically expanded.
- A separately supplied root-level array receives special recursive processing.
- Root-array elements are made available as zero-based indexed variables.
- Keys within those array elements are loaded as FileMaker repeating variables.
- `$max_repetition` is always created when an array is processed.
- `$max_repetition` contains the number of objects in the root-level array that was just processed.
- A namespaced `max_repetition` is additionally created when a namespace is used.
- A maximum of 999 root-level elements can be processed.

## Design philosophy

`VBZS_JSONParameters` is designed to make structured parameter passing practical in FileMaker.

Instead of building strings containing FileMaker variable assignments and evaluating them using `Evaluate` and `Let`, data can remain JSON while it is passed between scripts and converted to variables only when those variables are actually needed.

Namespaces make it possible to work with several JSON datasets containing common key names without constantly risking accidental variable collisions.

Nested JSON structures encountered while processing a normal JSON object are processed explicitly rather than recursively. This prevents the function from inventing namespaces, overwriting variables unexpectedly or recursively traversing structures whose meaning is known much better by the FileMaker developer than by the function.

Root-level arrays are treated differently because they represent a common and predictable structure: a variable-length collection of similar objects.

Such arrays are split into indexed JSON objects and those objects are subsequently processed into FileMaker repeating variables. `$max_repetition` contains the number of objects in the most recently loaded root-level array and therefore determines the relevant range of repetitions.

This makes patterns such as processing webshop order lines straightforward even when the number of items is not known beforehand.

The central principle is:

**Use JSON to transport structured data, namespaces to keep datasets separate, and FileMaker variables only where the data is actually needed.**