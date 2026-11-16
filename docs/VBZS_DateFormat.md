# VBZS_DateFormat

## Description

`VBZS_DateFormat` converts a FileMaker date to text using a user-defined formatting template.

The template determines how the day, month and year are represented and which separators or additional characters are included in the result.

Day and month names can also be used. These names are automatically returned in the language of the system locale on the computer where FileMaker is running.

## Syntax

```text
VBZS_DateFormat ( thedate ; dateformat )
```

## Parameters

### `thedate`

The FileMaker date to format.

### `dateformat`

A template describing how the date should be returned.

The template can contain formatting codes for the individual parts of the date. Characters that are not reserved formatting characters are copied literally to the result.

## Format codes

The following formatting codes are available:

| Code | Result |
|---|---|
| `d` | Day number |
| `dd` | Day number, padded to two digits |
| `m` | Month number |
| `mm` | Month number, padded to two digits |
| `yy` | Year as two digits |
| `yyyy` | Year as four digits |
| `T` | Localized day name in lowercase |
| `TT` | Localized day name in proper case |
| `B` | Localized month name in lowercase |
| `BB` | Localized month name in proper case |

The reserved formatting characters are not case-sensitive.

For example:

```text
dd
DD
```

have the same meaning.

Likewise, the other formatting codes can be entered using either uppercase or lowercase characters.

## Literal characters

Characters that are not reserved by the formatting template are copied literally to the result.

This makes it possible to freely choose date separators and formatting.

For example:

```text
dd-mm-yyyy
```

produces a date such as:

```text
07-11-2026
```

while:

```text
dd/mm/yyyy
```

produces:

```text
07/11/2026
```

Spaces and other non-reserved characters can also be included directly in the template.

## Examples

### Numeric date

```filemaker
VBZS_DateFormat (
    Date ( 11 ; 7 ; 2026 ) ;
    "dd-mm-yyyy"
)
```

returns:

```text
07-11-2026
```

Changing only the literal separators:

```filemaker
VBZS_DateFormat (
    Date ( 11 ; 7 ; 2026 ) ;
    "dd/mm/yyyy"
)
```

returns:

```text
07/11/2026
```

### Different order

The components of the date can be placed in any desired order.

```filemaker
VBZS_DateFormat (
    Date ( 11 ; 7 ; 2026 ) ;
    "yyyy-mm-dd"
)
```

returns:

```text
2026-11-07
```

### Day and month names

Day and month names can be included using `T` and `B`.

For example:

```filemaker
VBZS_DateFormat (
    Date ( 11 ; 7 ; 2026 ) ;
    "TT d BB yyyy"
)
```

On a system using an English locale this would return:

```text
Saturday 7 November 2026
```

On a system using a Dutch locale, the day and month names are returned in Dutch instead.

## Localized day and month names

The following format codes are locale-dependent:

```text
T
TT
B
BB
```

`T` and `TT` return the name of the day.

`B` and `BB` return the name of the month.

The language is determined by the system locale of the computer on which FileMaker is running.

VBZS does not contain its own language tables and no language needs to be specified in the function.

> **Important:** The same FileMaker solution may therefore return day and month names in different languages when used on computers with different system locale settings.

This also applies when a calculation is evaluated on FileMaker Server rather than on a FileMaker Pro client. The locale of the system performing the calculation determines the language of the resulting day and month names.

Numeric date components such as `dd`, `mm` and `yyyy` are not affected by this language selection.

## Lowercase and proper case names

A single `T` or `B` returns the localized name in lowercase.

For example:

```text
T
```

may return:

```text
saturday
```

and:

```text
B
```

may return:

```text
november
```

Using the code twice returns the name in proper format:

```text
TT
```

may return:

```text
Saturday
```

and:

```text
BB
```

may return:

```text
November
```

The actual words depend on the system locale.

## Creating custom formats

Because non-reserved characters are copied literally, `dateformat` can be used to create many different date representations.

Examples include:

```text
dd-mm-yyyy
mm/dd/yyyy
yyyy-mm-dd
d/m/yy
TT d BB yyyy
```

The template controls the representation of the date without changing the underlying FileMaker date value.

## Notes

- `VBZS_DateFormat` returns the formatted date as text.
- `thedate` is a FileMaker date.
- `dateformat` defines the resulting text representation.
- `d` and `m` return day and month numbers without forced leading zeros.
- `dd` and `mm` return day and month numbers using two digits.
- `yy` returns a two-digit year.
- `yyyy` returns a four-digit year.
- `T` and `B` return lowercase localized day and month names.
- `TT` and `BB` return localized day and month names in proper format.
- Formatting codes are not case-sensitive.
- Non-reserved characters are copied literally.
- Day and month names use the locale of the system on which FileMaker performs the calculation.

## Design philosophy

FileMaker dates are values, but dates often need to be presented as text in a specific format for display, export, communication with another system or use in generated content.

`VBZS_DateFormat` separates the date value from its textual representation by using a small formatting template.

Common numeric date formats can be generated without depending on the date display format of a FileMaker field. At the same time, localized day and month names can be obtained without maintaining separate language tables in the FileMaker solution.

For localized names, the function deliberately uses the locale already available on the system running FileMaker rather than introducing its own translation or language mechanism.