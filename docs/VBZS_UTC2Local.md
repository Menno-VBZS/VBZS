# VBZS_UTC2Local

## Description

`VBZS_UTC2Local` converts a UTC timestamp or numeric UTC time value to local time.

The conversion uses the supplied GMT timezone offset and automatically applies the built-in European Daylight Saving Time correction when applicable.

The function can also preserve fractional seconds, with the number of decimal places controlled by the `decimals` parameter.

## Syntax

```text
VBZS_UTC2Local ( UTC ; timezone ; decimals )
```

## Parameters

### `UTC`

The UTC time to convert.

The value can be supplied as a FileMaker timestamp or as a numeric UTC time value.

A common use is FileMaker's:

```filemaker
Get ( CurrentTimeUTCMicroseconds )
```

Dividing this value by `1000000` produces a UTC value expressed in seconds while preserving the fractional part:

```filemaker
Get ( CurrentTimeUTCMicroseconds ) / 1000000
```

This can be passed directly to `VBZS_UTC2Local`.

### `timezone`

The normal GMT offset of the desired local timezone, expressed in hours.

The supported range is:

```text
-12 ... +12
```

Fractional timezone offsets are supported.

For example:

```text
1
```

represents GMT+1 during standard time.

Offsets containing fractions of an hour can also be used:

```text
5.5
5.25
```

The function applies its built-in European Daylight Saving Time correction in addition to this normal GMT offset.

### `decimals`

The number of decimal places to return for the fractional part of the seconds.

Supported values are:

```text
0 ... 6
```

For example:

```text
0
```

returns whole seconds, while:

```text
6
```

returns microsecond precision when that precision is available in the supplied UTC value.

## Result

Returns the UTC value converted to local time using:

1. the supplied normal GMT offset;
2. the built-in European Daylight Saving Time correction;
3. the requested number of fractional second digits.

The result uses the system's timestamp representation.

## Example

```filemaker
VBZS_UTC2Local (
    Get ( CurrentTimeUTCMicroseconds ) / 1000000 ;
    1 ;
    6
)
```

may return:

```text
06/10/2026 22:38:45,697535
```

In this example:

- the UTC value is obtained from `Get ( CurrentTimeUTCMicroseconds )`;
- dividing by `1000000` converts the microsecond value to seconds while retaining the fractional part;
- `timezone = 1` specifies a normal offset of GMT+1;
- the built-in Daylight Saving Time calculation determines whether an additional hour applies;
- `decimals = 6` retains six digits of fractional seconds.

## Fractional seconds

The `decimals` parameter determines how many fractional second digits are included in the result.

For example, with a local time equivalent to:

```text
22:38:45,697535
```

using:

```text
decimals = 6
```

preserves:

```text
697535
```

as the fractional part of the second.

Using:

```text
decimals = 0
```

returns the timestamp without fractional seconds.

The supported range is 0 through 6 decimal places.

## Fractional timezone offsets

Timezone offsets are not limited to whole hours.

For example:

```text
timezone = 5.5
```

represents an offset of five hours and thirty minutes from GMT before any applicable built-in Daylight Saving Time correction.

Quarter-hour offsets can also be represented:

```text
timezone = 5.25
```

This makes the timezone calculation suitable for locations whose normal GMT offset does not consist of complete hours.

## Daylight Saving Time

`VBZS_UTC2Local` contains built-in Daylight Saving Time rules for Europe.

The supplied `timezone` represents the normal GMT offset. During Daylight Saving Time, the function adds one additional hour when the built-in DST rules determine that summer time applies.

For example:

```text
timezone = 1
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

> **Important:** The automatic Daylight Saving Time calculation uses European DST rules. Regions using different DST rules cannot rely on the automatic DST correction for local-time conversion.

## Relationship with VBZS_Local2UTC

`VBZS_UTC2Local` performs the opposite conversion to `VBZS_Local2UTC`.

Conceptually:

```text
VBZS_Local2UTC

local timestamp
      +
timezone
      +
European DST
      ↓
UTC
```

while:

```text
VBZS_UTC2Local

UTC
      +
timezone
      +
European DST
      ↓
local timestamp
```

The functions can therefore be used for opposite sides of timestamp conversion when the same timezone and European DST rules apply.

## Notes

- `UTC` can be supplied as a FileMaker timestamp or numeric UTC time value.
- `timezone` represents the normal GMT offset in hours.
- `timezone` can contain fractional hours.
- The documented timezone range is `-12` through `+12`.
- European Daylight Saving Time is calculated automatically.
- The built-in DST calculation is not suitable for regions using different DST transition rules.
- `decimals` specifies the number of fractional second digits.
- The documented range for `decimals` is `0` through `6`.
- `decimals = 0` returns whole seconds.
- `decimals = 6` can retain microsecond precision when present in the input.
- The result is returned using the system timestamp representation.

## Design philosophy

FileMaker provides:

```filemaker
Get ( CurrentTimeUTCMicroseconds )
```

which makes a precise UTC time available, but applications often need that value represented as a local timestamp.

`VBZS_UTC2Local` combines the UTC value with a GMT timezone offset and the built-in European Daylight Saving Time rules to produce that local timestamp.

Support for fractional timezone offsets allows the same calculation mechanism to work with timezones that differ from GMT by whole, half or quarter hours.

The `decimals` parameter allows the caller to decide how much of the available sub-second precision should be retained, from normal whole-second timestamps through microsecond precision.