# VBZS_AddWorkingdays

## Description

`VBZS_AddWorkingdays` adds a specified number of working days to a starting date.

By default, Monday through Friday are considered working days. However, not everybody has the same working week. The optional `workdays` parameter allows any day of the week to be defined as either a working day or a non-working day.

This makes the function suitable for both conventional Monday-to-Friday schedules and alternative working patterns where, for example, Saturday or Sunday is a working day and another weekday is not.

The starting date itself does not have to be a working day.

## Syntax

```text
VBZS_AddWorkingdays ( startdate ; workdays ; daystoadd )
```

## Parameters

### `startdate`

The FileMaker date from which the calculation starts.

The starting date is not counted as one of the working days to add. Counting starts with the following calendar day.

It is not relevant whether `startdate` itself is a working day.

### `workdays`

Defines which days of the week are considered working days.

If `workdays` is empty, the standard Monday-to-Friday working week is used.

When specified, `workdays` must:

- contain exactly 7 characters;
- contain only `0` and `1`;
- contain at least one `1`.

The positions follow FileMaker's weekday order:

| Position | Day |
|---:|---|
| 1 | Sunday |
| 2 | Monday |
| 3 | Tuesday |
| 4 | Wednesday |
| 5 | Thursday |
| 6 | Friday |
| 7 | Saturday |

A `1` indicates a working day and a `0` indicates a non-working day.

The standard Monday-to-Friday working week can therefore be represented as:

```text
0111110
```

Because this is the default, there is normally no need to specify this pattern explicitly. Leave `workdays` empty instead.

An invalid `workdays` pattern causes the function to return `startdate` unchanged, regardless of which day of the week `startdate` represents.

### `daystoadd`

The number of working days to add to `startdate`.

`daystoadd` must be a positive integer for working days to be added.

If `daystoadd` is `0` or negative, the function returns `startdate` unchanged.

## Result

Returns the calculated FileMaker date after adding the requested number of working days.

Only days defined as working days by `workdays` are counted.

If `workdays` is empty, Monday through Friday are counted as working days.

## Examples

### Standard working week

The standard Monday-to-Friday schedule does not require a `workdays` pattern:

```filemaker
VBZS_AddWorkingdays (
    Date ( 11 ; 7 ; 2026 ) ;
    "" ;
    1
)
```

November 7, 2026 is a Saturday. The next working day is Monday, November 9.

Result:

```text
09/11/2026
```

The same calculation starting on a working day:

```filemaker
VBZS_AddWorkingdays (
    Date ( 11 ; 9 ; 2026 ) ;
    "" ;
    1
)
```

returns:

```text
10/11/2026
```

The starting date is therefore only the starting point of the calculation. It is never counted as one of the working days being added.

### Alternative working week

A different working-week pattern can be specified when required.

For example:

```text
0011111
```

defines the following week:

| Day | Working day |
|---|---|
| Sunday | No |
| Monday | No |
| Tuesday | Yes |
| Wednesday | Yes |
| Thursday | Yes |
| Friday | Yes |
| Saturday | Yes |

Using this pattern:

```filemaker
VBZS_AddWorkingdays (
    startdate ;
    "0011111" ;
    5
)
```

adds five working days according to a Tuesday-through-Saturday schedule.

## Invalid working-week patterns

The following patterns are invalid:

```text
011110
```

Only six positions are supplied.

```text
01111100
```

Eight positions are supplied.

```text
011X110
```

A character other than `0` or `1` is present.

```text
0000000
```

No working day is defined.

When an invalid pattern is supplied, `VBZS_AddWorkingdays` returns `startdate` unchanged.

## Notes

- The default working week is Monday through Friday.
- Leave `workdays` empty to use the default working week.
- The order of the `workdays` pattern is Sunday through Saturday, following FileMaker's weekday convention.
- `startdate` does not have to be a working day.
- `startdate` itself is never counted as one of the days to add.
- A `daystoadd` value of `0` or less returns `startdate` unchanged.
- An invalid `workdays` pattern returns `startdate` unchanged.

## Design philosophy

A working day is not necessarily the same for every person, company or application.

Simply excluding Saturday and Sunday assumes a conventional Monday-to-Friday schedule. `VBZS_AddWorkingdays` deliberately avoids making that assumption.

Instead, the function separates the starting date from the definition of the working week. This allows the same calculation to be used for conventional office schedules as well as alternative working patterns, without requiring different calculation logic.