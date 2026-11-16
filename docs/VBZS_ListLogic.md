# VBZS_ListLogic

## Description

`VBZS_ListLogic` compares two FileMaker value lists using one of three logical operations: `AND`, `XOR` or `NOT`.

The operations follow the familiar principles of Boolean logic, applied to the individual values in return-delimited FileMaker lists.

Comparisons are case-insensitive, but values returned by the function retain their original capitalization.

The function processes values from top to bottom, starting with `lista`.

## Syntax

```text
VBZS_ListLogic ( type ; lista ; listb )
```

## Parameters

### `type`

Defines the logical operation to perform.

The following operations are supported:

| Type | Result |
|---|---|
| `AND` | Values from `lista` that also occur in `listb` |
| `XOR` | Values that occur exclusively in either `lista` or `listb` |
| `NOT` | Values from `lista` that do not occur in `listb` |

### `lista`

The primary value list.

Values in `lista` are processed from top to bottom.

The original value from `lista`, including its capitalization, is retained when that value qualifies for the result.

Duplicates in `lista` are preserved when they satisfy the selected logical operation.

### `listb`

The value list against which `lista` is compared.

Comparisons are case-insensitive.

Duplicates in `listb` do not increase the number of matches produced from `lista`. For matching purposes, the important question is whether a value occurs in `listb`, not how many times it occurs.

## Result

Returns a return-delimited FileMaker value list containing the values that satisfy the selected logical operation.

The function processes the lists from left to right and the values within each list from top to bottom.

The original capitalization of a returned value is preserved.

## Case-insensitive comparison

All comparisons are case-insensitive.

For example:

```text
AbC
abc
ABC
aBc
```

are considered identical when determining whether values match.

However, the function does not change the original capitalization of the value included in the result.

For example:

```filemaker
VBZS_ListLogic (
    "AND" ;
    "AbC" ;
    "aBc"
)
```

returns:

```text
AbC
```

The values match because the comparison is case-insensitive, but `AbC` is returned because that is the value encountered in `lista`.

## AND

`AND` returns values from `lista` that also occur in `listb`.

For example:

```filemaker
VBZS_ListLogic (
    "AND" ;
    "A¶B¶C" ;
    "B¶C¶D"
)
```

returns:

```text
B
C
```

### Duplicates with AND

Qualifying duplicates in `lista` are preserved.

For example:

```filemaker
VBZS_ListLogic (
    "AND" ;
    "A¶A" ;
    "C¶B¶a"
)
```

returns:

```text
A
A
```

Both values in `lista` match `a` in `listb`.

The comparison is case-insensitive, so `A` and `a` are considered equal.

The original `A` values from `lista` are returned.

Duplicates in `listb` do not create additional results.

For example:

```filemaker
VBZS_ListLogic (
    "AND" ;
    "A¶A" ;
    "A¶A¶A"
)
```

returns:

```text
A
A
```

The three occurrences of `A` in `listb` do not produce three matches. `lista` remains the primary list and contains two qualifying values.

## XOR

`XOR` returns values that occur exclusively in one of the two lists.

Values occurring in both lists are excluded.

For example:

```filemaker
VBZS_ListLogic (
    "XOR" ;
    "A¶B" ;
    "B¶C"
)
```

returns:

```text
A
C
```

`B` occurs in both lists and is therefore excluded.

Values exclusive to `lista` are processed first, followed by qualifying values from `listb`.

For example:

```filemaker
VBZS_ListLogic (
    "XOR" ;
    "A¶A" ;
    "C¶B"
)
```

returns:

```text
A
A
C
B
```

The duplicate `A` values from `lista` are preserved.

## NOT

`NOT` returns values from `lista` that do not occur in `listb`.

Unlike `AND` and `XOR`, the direction of the comparison is fundamental to the operation.

For example:

```filemaker
VBZS_ListLogic (
    "NOT" ;
    "A¶B¶C" ;
    "B¶C¶D"
)
```

returns:

```text
A
```

Reversing the lists performs a different comparison:

```filemaker
VBZS_ListLogic (
    "NOT" ;
    "B¶C¶D" ;
    "A¶B¶C"
)
```

returns:

```text
D
```

### Duplicates with NOT

Duplicates from `lista` are preserved when they satisfy the condition.

For example:

```filemaker
VBZS_ListLogic (
    "NOT" ;
    "A¶A" ;
    "C¶B"
)
```

returns:

```text
A
A
```

Both occurrences of `A` in `lista` are absent from `listb` and are therefore returned.

## List order

The function processes its input from left to right and top to bottom.

`lista` is processed first.

This means that, although `AND` and `XOR` are logically symmetrical operations, exchanging `lista` and `listb` can affect the order and capitalization of the returned values.

For example:

```filemaker
VBZS_ListLogic (
    "AND" ;
    "AbC" ;
    "aBc"
)
```

returns:

```text
AbC
```

while:

```filemaker
VBZS_ListLogic (
    "AND" ;
    "aBc" ;
    "AbC"
)
```

returns:

```text
aBc
```

The logical match is identical, but the returned representation follows the value encountered in `lista`.

## Logical overview

Given two lists A and B, the three operations can be summarized as:

```text
AND : values in A that are also in B
XOR : values in A or B, but not in both
NOT : values in A that are not in B
```

The operations follow these logical relationships:

```text
AND ( A ; B ) = AND ( B ; A )
XOR ( A ; B ) = XOR ( B ; A )
NOT ( A ; B ) ≠ NOT ( B ; A )
```

For `AND` and `XOR`, this equivalence describes which values logically qualify. Because the function preserves input order, duplicates and original capitalization, exchanging the actual lists may produce a differently ordered or formatted result.

## Notes

- `lista` and `listb` are return-delimited FileMaker value lists.
- Comparisons are case-insensitive.
- Returned values retain their original capitalization.
- Values are processed from top to bottom.
- `lista` is processed before `listb`.
- Qualifying duplicates from `lista` are preserved.
- Duplicates in `listb` do not increase the number of matches produced from `lista`.
- `AND` returns values from `lista` that also occur in `listb`.
- `NOT` returns values from `lista` that do not occur in `listb`.
- `XOR` returns values occurring exclusively in either list.
- With `XOR`, qualifying values from `lista` appear before qualifying values from `listb`.

## Design philosophy

`VBZS_ListLogic` applies simple logical operations directly to FileMaker value lists.

The function deliberately works with the lists as supplied rather than first converting them into unique sets. This means that the original order, capitalization and qualifying duplicate values from `lista` can remain meaningful.

`lista` acts as the primary list and is processed from top to bottom. `listb` provides the comparison used to determine whether each value satisfies the selected logical operation.

This makes `VBZS_ListLogic` useful when the values themselves are important, but their original representation and list order should not be discarded merely because a logical comparison is being performed.