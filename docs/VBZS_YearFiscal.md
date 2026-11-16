# VBZS_YearFiscal

## Description

`VBZS_YearFiscal` returns the week-numbering year to which a given date belongs.

For most dates, the returned year is identical to the calendar year. The distinction becomes relevant during the first and last week of a year, where a week can span two different calendar years.

The function uses ISO-style week-numbering rules while allowing the first day of the week to be configured.

## Syntax

```text
VBZS_YearFiscal ( date ; startingDay )
```

## Parameters

### `date`

The FileMaker date for which the week-numbering year should be determined.

### `startingDay`

Defines which day is considered the first day of the week.

The numbering follows FileMaker's weekday convention:

| Value | Day |
|---:|---|
| `1` | Sunday |
| `2` | Monday |
| `3` | Tuesday |
| `4` | Wednesday |
| `5` | Thursday |
| `6` | Friday |
| `7` | Saturday |

For the conventional European ISO week:

```text
startingDay = 2
```

which makes Monday the first day of the week.

## Result

Returns the year to which the week containing `date` belongs.

The result may differ from the calendar year of `date` during the first or last days of a calendar year.

## Example

```filemaker
VBZS_YearFiscal (
    Date ( 1 ; 1 ; 2027 ) ;
    2
)
```

returns:

```text
2026
```

January 1, 2027 is a Friday.

With Monday as the first day of the week, Friday falls within the final three days of that week. The complete week therefore belongs to the preceding week-numbering year:

```text
2026
```

## Week-numbering rule

`VBZS_YearFiscal` uses the ISO principle that week 1 must contain at least four days of the new year.

If January 1 occurs within the first four days of the configured week, that complete week belongs to the new year.

If January 1 occurs within the final three days of the configured week, that week belongs to the preceding week-numbering year.

This means that calendar year and week-numbering year are not always identical around the beginning and end of a year.

## Monday-based ISO year

With:

```text
startingDay = 2
```

the week begins on Monday.

If January 1 occurs on:

```text
Monday
Tuesday
Wednesday
Thursday
```

the week belongs to the new week-numbering year.

If January 1 occurs on:

```text
Friday
Saturday
Sunday
```

the date belongs to the final week of the preceding week-numbering year.

This corresponds to the conventional ISO week-numbering system.

## Dates at the end of December

The reverse situation can occur at the end of December.

A date in the final days of December may already belong to week 1 of the following week-numbering year.

The function therefore does not simply return:

```filemaker
Year ( date )
```

Instead, it determines which week contains the supplied date and then returns the year to which that complete week belongs.

## Configurable first weekday

The same 4/3 rule is used when a different first weekday is selected.

For example:

```text
startingDay = 1
```

makes Sunday the first day of the week.

The week boundaries move accordingly, but the principle remains the same:

```text
At least four days in the new year
    → week belongs to the new year

Three or fewer days in the new year
    → week belongs to the preceding year
```

This makes the function useful for week-based or fiscal calendars that do not use Monday as the first day of the week.

## Relationship with VBZS_WeekFiscalToDate

`VBZS_YearFiscal` uses the same week-numbering principle as `VBZS_WeekFiscalToDate`.

The two functions approach the calculation from opposite directions.

`VBZS_YearFiscal` determines:

```text
date
    ↓
week-numbering year
```

while `VBZS_WeekFiscalToDate` determines:

```text
week-numbering year
+
week number
+
first weekday
    ↓
date
```

Using the same `startingDay` / `Firstweekday` convention keeps both calculations consistent.

## Notes

- The function returns the week-numbering year for a FileMaker date.
- The result normally equals the calendar year.
- Differences occur around the first and last week of a calendar year.
- `startingDay` defines the first day of the week.
- FileMaker weekday numbering is used: Sunday = `1` through Saturday = `7`.
- `startingDay = 2` produces the conventional Monday-based ISO week structure.
- A week belongs to the new year when it contains at least four days of that year.
- The returned year may therefore differ from `Year ( date )`.

## Design philosophy

For most dates, determining the year is as simple as using FileMaker's:

```filemaker
Year ( date )
```

Week-based calendars introduce an exception around the transition between calendar years.

The first days of January can still belong to the final week of the preceding year, while the final days of December can already belong to week 1 of the following year.

`VBZS_YearFiscal` handles this boundary and returns the year associated with the complete week rather than simply the calendar year of the supplied date.

Together with `VBZS_WeekFiscalToDate`, this provides consistent handling of week-based dates using either the normal Monday-based ISO calendar or another configured first day of the week.