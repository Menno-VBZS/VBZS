# VBZS_Local2UTC

## Description

`VBZS_Local2UTC` converts or formats a local FileMaker timestamp for use in different timestamp representations, including ISO 8601 and UTC/Zulu time.

The function takes a local timestamp together with its normal GMT timezone offset. Daylight Saving Time is calculated automatically using the built-in Western European DST rules.

Depending on the requested output format, the function can:

- return the timestamp using the system format;
- format the timestamp for data entry;
- include the current GMT offset;
- return a full ISO 8601 representation;
- convert the local timestamp to UTC/Zulu time.

## Syntax

```text
VBZS_Local2UTC ( LocalTime ; TimeZone ; iso8601 )
```

## Parameters

### `LocalTime`

The local FileMaker timestamp to convert or format.

`LocalTime` should be supplied as a FileMaker timestamp.

For example:

```filemaker
TimeStamp (
    Date ( 10 ; 5 ; 2026 ) ;
    Time ( 15 ; 0 ; 0 )
)
```

### `TimeZone`

The normal GMT offset of the supplied local timestamp, expressed in hours.

For example:

```text
1
```

represents GMT+1 during standard time.

Fractional timezone offsets are supported. This allows timezones with half-hour offsets to be represented, for example:

```text
5.5
```

Daylight Saving Time is handled separately by the function and may add another hour to the effective offset.

### `iso8601`

Determines the representation of the returned timestamp.

The following values are supported:

| Value | Format |
|---|---|
| empty or `0` | Timestamp using the system format |
| `1` | Local timestamp with the current GMT offset |
| `A` | ANSI data-entry format |
| `D` | Database data-entry format |
| `F` | Full ISO 8601 representation including GMT offset |
| `Z` | UTC/Zulu representation |

## Daylight Saving Time

`VBZS_Local2UTC` contains built-in Daylight Saving Time rules for Western Europe.

The supplied `TimeZone` represents the normal GMT offset. During Daylight Saving Time, the function adds one additional hour to that offset.

For example:

```text
TimeZone = 1
```

represents:

```text
UTC+01:00
```

during standard time and:

```text
UTC+02:00
```

during Daylight Saving Time.

The built-in transitions are:

- Daylight Saving Time starts on the last Sunday of March at `00:00 UTC`.
- Daylight Saving Time ends on the last Sunday of October at `01:00 UTC`.

> **Important:** The built-in Daylight Saving Time calculation follows Western European rules. `VBZS_Local2UTC` should not be used to convert local timestamps from regions that use different DST rules, such as the United States.

## Standard format

When `iso8601` is empty or `0`, the timestamp is returned using the system timestamp format.

```filemaker
VBZS_Local2UTC (
    LocalTime ;
    1 ;
    ""
)
```

The actual representation therefore depends on the system settings of the computer on which FileMaker performs the calculation.

## Format `1`

Using:

```text
iso8601 = 1
```

returns the local timestamp together with the GMT offset that applies at that moment.

For example:

```filemaker
VBZS_Local2UTC (
    TimeStamp (
        Date ( 10 ; 5 ; 2026 ) ;
        Time ( 15 ; 0 ; 0 )
    ) ;
    1 ;
    1
)
```

returns:

```text
2026-10-05 15:00:00+02:00
```

The normal timezone is GMT+1, but October 5 falls within the built-in Daylight Saving Time period. The applicable offset is therefore `+02:00`.

The local clock time itself remains:

```text
15:00:00
```

## ANSI format

Using:

```text
iso8601 = "A"
```

returns the timestamp using an ANSI-style data-entry format:

```text
MM/DD/YYYY hh:mm:ss
```

For example:

```text
10/05/2026 15:00:00
```

This format can be useful when supplying timestamp data to the FileMaker Data API when an explicit `dateformats` option has not been specified.

The date is deliberately represented using the month/day/year order expected by that data-entry format.

## Database format

Using:

```text
iso8601 = "D"
```

returns the timestamp in database data-entry format:

```text
YYYY+MM+DD hh:mm:ss
```

For example:

```text
2026+10+05 15:00:00
```

This representation is also intended for data exchange such as FileMaker Data API operations.

The year-month-day representation avoids ambiguity introduced by regional date formats. It can therefore be interpreted consistently without depending on whether the receiving database or computer uses a day/month/year or month/day/year locale.

## Full ISO 8601 format

Using:

```text
iso8601 = "F"
```

returns the local timestamp together with the GMT offset applying at that moment.

The format is:

```text
YYYY-MM-DD hh:mm:ss+hh:mm
```

For example:

```filemaker
VBZS_Local2UTC (
    TimeStamp (
        Date ( 10 ; 5 ; 2026 ) ;
        Time ( 15 ; 0 ; 0 )
    ) ;
    1 ;
    "F"
)
```

returns:

```text
2026-10-05 15:00:00+02:00
```

The timestamp continues to represent the local clock time.

The GMT offset identifies the relationship between that local time and UTC.

## UTC/Zulu format

Using:

```text
iso8601 = "Z"
```

converts the supplied local timestamp to UTC.

The format is:

```text
YYYY-MM-DDThh:mm:ssZ
```

For example:

```filemaker
VBZS_Local2UTC (
    TimeStamp (
        Date ( 10 ; 5 ; 2026 ) ;
        Time ( 15 ; 0 ; 0 )
    ) ;
    1 ;
    "Z"
)
```

returns:

```text
2026-10-05T13:00:00Z
```

At this date, the local GMT+1 timezone is using Daylight Saving Time, making the effective offset:

```text
+02:00
```

The local time:

```text
15:00:00
```

is therefore converted to:

```text
13:00:00Z
```

## `F` versus `Z`

The distinction between `F` and `Z` is important.

`F` represents the local timestamp and includes its current offset from GMT:

```text
2026-10-05 15:00:00+02:00
```

`Z` represents the same moment converted to UTC:

```text
2026-10-05T13:00:00Z
```

Both values therefore describe the same moment in time, but from different timezone perspectives.

## Fractional timezone offsets

`TimeZone` is not limited to whole hours.

For regions with a half-hour offset, a fractional value can be supplied.

For example:

```text
5.5
```

represents an offset of five hours and thirty minutes before any applicable built-in DST correction.

Keep in mind that the automatic DST calculation still follows the Western European DST rules.

A fractional `TimeZone` value therefore does not automatically make the function appropriate for a region whose Daylight Saving Time rules differ from Western Europe.

## Regional limitation

The timezone itself is supplied by the caller, but the Daylight Saving Time rules are built into the function.

This distinction is important.

Changing:

```text
TimeZone
```

changes the basic GMT offset, but it does not change the rules used to determine when Daylight Saving Time starts and ends.

Consequently, supplying the GMT offset of another region is only appropriate when that region follows the same DST transition rules.

`VBZS_Local2UTC` is primarily intended for timestamps using Western European DST rules.

## Notes

- `LocalTime` should be supplied as a FileMaker timestamp.
- `TimeZone` specifies the normal GMT offset in hours.
- Fractional timezone offsets are supported.
- Daylight Saving Time is calculated automatically.
- The DST rules are fixed to the Western European transition dates and times.
- Empty or `0` uses the system timestamp representation.
- `1` returns local time with the applicable GMT offset.
- `A` produces `MM/DD/YYYY hh:mm:ss`.
- `D` produces `YYYY+MM+DD hh:mm:ss`.
- `F` produces local time in full ISO 8601 form with its GMT offset.
- `Z` converts the timestamp to UTC/Zulu time.
- `F` retains the local clock time.
- `Z` changes the clock time to its UTC equivalent.
- Fractional seconds are not part of the documented output format.

## Design philosophy

`VBZS_Local2UTC` is intended for situations where a FileMaker timestamp needs to be exchanged with other systems in a predictable representation.

A FileMaker timestamp by itself represents a date and time but does not necessarily communicate the relationship of that local time to UTC. This becomes important when timestamps are sent through APIs or exchanged between systems in different locations.

`VBZS_Local2UTC` combines three pieces of information:

```text
local timestamp
+
normal GMT offset
+
Western European DST rules
```

and can represent the result in several formats suitable for display, data entry or data exchange.

The `F` and `Z` formats address two different needs.

Use `F` when the local timestamp and its UTC offset should both remain visible.

Use `Z` when the moment should be converted to an unambiguous UTC/Zulu timestamp.

The `A` and `D` formats provide alternative data-entry representations, with `D` particularly useful when the date should be interpreted independently of regional date settings.