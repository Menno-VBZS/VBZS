# VBZS_WeekFiscalToDate

## Description

`VBZS_WeekFiscalToDate` calculates the date of the first day of a specified week number in a given year.

The function uses ISO-style week-numbering rules, but allows the first day of the week to be configured.

This makes it possible to use the conventional European Monday-based week as well as week structures that start on another day.

## Syntax

```text
VBZS_WeekFiscalToDate ( Yearnr ; WeeknrOfYear ; Firstweekday )
```

## Parameters

### `Yearnr`

The year for which the week number should be calculated.

The supported range is:

```text
1 ... 4000
```

### `WeeknrOfYear`

The week number for which the starting date should be returned.

`WeeknrOfYear` can be any natural number. It is not explicitly limited to 52 or 53.

The practical limit is determined by the resulting FileMaker date.

### `Firstweekday`

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
Firstweekday = 2
```

which makes Monday the first day of the week.

## Result

Returns the FileMaker date representing the first day of the requested week.

The result depends on:

- the supplied year;
- the requested week number;
- the configured first day of the week.

## Example

```filemaker
VBZS_WeekFiscalToDate (
    2026 ;
    41 ;
    2
)
```

returns:

```text
05-10-2026
```

With `Firstweekday = 2`, Monday is the first day of the week.

October 5, 2026 is therefore the first day of week 41.

## Week numbering

`VBZS_WeekFiscalToDate` uses the ISO principle for determining which week belongs to the new year.

Week 1 is the week containing at least four days of the new year.

If January 1 falls within the first four days of the configured week, that complete week is considered week 1 of the new year.

This means that the first days of week 1 can actually occur in December of the preceding calendar year.

If January 1 falls within the final three days of the configured week, January 1 is considered part of the last week of the preceding week-numbering year.

## Monday-based ISO week

With:

```text
Firstweekday = 2
```

the week starts on Monday.

The week containing January 1 belongs to the new year when January 1 occurs on:

```text
Monday
Tuesday
Wednesday
Thursday
```

If January 1 occurs on:

```text
Friday
Saturday
Sunday
```

that date belongs to the last week of the preceding week-numbering year.

This corresponds to the conventional ISO week-numbering system.

## Configurable first weekday

The same 4/3 rule is applied when another first weekday is selected.

For example:

```text
Firstweekday = 1
```

defines Sunday as the beginning of the week.

The week boundaries move accordingly, while the rule determining week 1 remains the same:

```text
First four days of the week
    → week belongs to the new year

Last three days of the week
    → week belongs to the preceding year
```

This allows the function to calculate week-based dates for calendars where the week does not start on Monday.

## Week-numbering year versus calendar year

The week-numbering year and calendar year do not always start on the same date.

For example, week 1 of a new year can begin during the final days of December.

Likewise, the first days of January may still belong to the final week of the preceding week-numbering year.

`Yearnr` therefore identifies the week-numbering year used in the calculation, while the resulting FileMaker date may fall in the preceding calendar year.

## Notes

- `Yearnr` supports years from `1` through `4000`.
- `WeeknrOfYear` is not restricted to 52 or 53.
- `Firstweekday` must be between `1` and `7`.
- FileMaker weekday numbering is used: Sunday = `1` through Saturday = `7`.
- `Firstweekday = 2` produces the conventional Monday-based European ISO week.
- Week 1 contains at least four days of the new year.
- A week containing January 1 within its final three days belongs to the preceding week-numbering year.
- The function returns the first day of the requested week as a FileMaker date.

## Design philosophy

Week numbers become less straightforward around the beginning and end of a calendar year.

In addition, not every application considers the same weekday to be the beginning of a week.

`VBZS_WeekFiscalToDate` separates these two concepts.

The ISO-style 4/3 rule determines which week belongs to a particular year, while `Firstweekday` determines where each week begins.

With Monday as the first weekday, the function follows the conventional European ISO week structure.

By changing `Firstweekday`, the same calculation can also be used for fiscal or organisational calendars whose weeks start on a different day.