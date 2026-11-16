# VBZS_Easter

## Description

`VBZS_Easter` calculates the date of Easter Sunday for a given year.

The calculation uses Gauss's Easter algorithm.

## Syntax

```text
VBZS_Easter ( theYear )
```

## Parameters

### `theYear`

The year for which Easter Sunday should be calculated.

## Result

Returns a FileMaker date containing the calculated date of Easter Sunday for the specified year.

## Example

```filemaker
VBZS_Easter ( 2026 )
```

returns:

```text
05/04/2026
```

## Supported range

`VBZS_Easter` provides a reliable Easter date for years from:

```text
1583 - 2100
```

The function does not reject years outside this range and may still return a date. A result outside the supported range should not be considered reliable.

> **Important:** A returned date does not imply that the supplied year is within the supported range. Use `VBZS_Easter` for years from 1583 through 2100.

## Notes

- The function calculates Easter Sunday.
- The calculation is based on Gauss's Easter algorithm.
- The result is returned as a FileMaker date.
- The supported range is 1583 through 2100.
- Years outside the supported range may produce a result, but that result is not guaranteed to be correct.

## Design philosophy

Calculating Easter Sunday requires more than a fixed month and day because its date changes every year.

`VBZS_Easter` provides this calculation as a single FileMaker function, allowing Easter Sunday to be used as the basis for other date calculations.

For example, once Easter Sunday is known, Easter-related dates can be calculated using normal FileMaker date arithmetic.