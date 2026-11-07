# VBZS_TrimAll_CR

## Description

`VBZS_TrimAll_CR` removes redundant carriage returns from a FileMaker value list.

Leading and trailing carriage returns are removed and multiple consecutive carriage returns between values are reduced to a single carriage return.

The result is a return-delimited list without empty values.

## Syntax

```text
VBZS_TrimAll_CR ( text )
```

## Parameters

### `text`

The text or return-delimited list from which redundant carriage returns should be removed.

## Result

Returns the supplied text with redundant carriage returns removed.

For example:

```filemaker
VBZS_TrimAll_CR ( "¶¶A¶¶B¶C¶" )
```

returns:

```text
A¶B¶C
```

## Spaces

`VBZS_TrimAll_CR` only removes redundant carriage returns.

Extra spaces within or around values are not removed.

If spaces also need to be removed or normalized, use FileMaker's native `TrimAll()` function separately.

## Example

Given:

```text
¶
¶
A
¶
B
C
¶
```

the function removes the empty values and returns:

```text
A
B
C
```

or, as FileMaker return-delimited text:

```text
A¶B¶C
```

## Notes

- Leading carriage returns are removed.
- Trailing carriage returns are removed.
- Consecutive carriage returns between values are reduced to one.
- Empty values are therefore removed from the list.
- Extra spaces are not removed.
- The order and contents of the remaining values are preserved.

## Design philosophy

FileMaker value lists can easily acquire empty values when lists from different sources are combined or manipulated.

`VBZS_TrimAll_CR` provides a simple way to remove those empty values without modifying the contents of the remaining values.

The function deliberately operates only on carriage returns. Space handling is left to FileMaker's native `TrimAll()` function.