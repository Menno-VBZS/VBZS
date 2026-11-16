# VBZS_Fraction

## Description

`VBZS_Fraction` returns the digits of the fractional part of a number as text.

The result always contains the number of characters specified by `digits`. Leading zeros are preserved and missing digits are added as trailing zeros.

If the fractional part contains more digits than requested, the result is truncated. The function does not round the number.

## Syntax

```text
VBZS_Fraction ( number ; digits )
```

## Parameters

### `number`

The number from which the fractional digits are extracted.

The integer part of the number is ignored.

For negative numbers, the minus sign is also ignored. The function returns only the digits of the fractional part.

### `digits`

A non-negative integer specifying the exact number of fractional digits to return.

If `digits` is `0`, the function returns empty text.

`digits` must be an integer. Fractional values for `digits` are not supported and may produce unusable results.

## Result

Returns the requested number of fractional digits as text.

The result is returned as text rather than as a number so that leading zeros are preserved.

The result is adjusted to exactly the length specified by `digits`:

- if the fractional part contains fewer digits, zeros are added on the right;
- if the fractional part contains more digits, the excess digits are removed;
- no rounding is performed.

## Examples

### Preserve leading zeros

```filemaker
VBZS_Fraction (
    10,0003 ;
    4
)
```

returns:

```text
0003
```

The result is text. If it were returned as a number, the leading zeros would be lost.

### Add trailing zeros

```filemaker
VBZS_Fraction (
    10,12 ;
    4
)
```

returns:

```text
1200
```

The fractional part contains only two digits, so two zeros are added on the right.

### Negative numbers

```filemaker
VBZS_Fraction (
    -10,12 ;
    4
)
```

returns:

```text
1200
```

The minus sign and integer part do not affect the result.

### Truncate excess digits

```filemaker
VBZS_Fraction (
    10,123456 ;
    4
)
```

returns:

```text
1234
```

The additional digits are removed. The value is not rounded to `1235`.

### Return no fractional digits

```filemaker
VBZS_Fraction (
    10,1234 ;
    0
)
```

returns empty text.

## Fixed-length results

For the same `digits` value, every result has the same length.

For example, with `digits` set to `4`:

| Number | Result |
|---:|---|
| `10,2` | `2000` |
| `10,12` | `1200` |
| `10,1234` | `1234` |
| `10,123456` | `1234` |
| `10,0003` | `0003` |
| `-10,12` | `1200` |

This makes the function particularly useful when numbers are incorporated into text and every fractional part must have a consistent number of characters.

## Truncation and rounding

`VBZS_Fraction` truncates the fractional part when it contains more digits than requested.

It does not round the number.

> **Important:** If rounding is required, round the number before passing it to `VBZS_Fraction`.

For example, this calculation:

```filemaker
VBZS_Fraction (
    10,123456 ;
    4
)
```

returns:

```text
1234
```

It does not return:

```text
1235
```

## Invalid `digits` values

`digits` is intended to be a non-negative integer.

A fractional value for `digits` is not a meaningful instruction and may produce an unusable result. Validate or convert `digits` before calling the function when its value comes from user input or an external source.

## Notes

- The function returns text.
- Only the fractional digits are returned.
- The integer part is not included.
- The sign of a negative number is not included.
- Leading zeros are preserved.
- Missing digits are added as zeros on the right.
- Excess digits are truncated.
- The function does not round.
- `digits = 0` returns empty text.
- `digits` should be a non-negative integer.

## Design philosophy

`VBZS_Fraction` is intended for situations where numbers are incorporated into text and each fractional part must have a consistent length.

Returning the result as text preserves leading zeros, while padding and truncation ensure that every result contains exactly the requested number of characters.

The function performs no rounding because its purpose is to format the existing fractional digits. When rounding is required, the number should be rounded before it is passed to `VBZS_Fraction`.