# VBZS_OntDiakriet

## Description

`VBZS_OntDiakriet` converts supported accented and special characters to predefined ASCII equivalents.

The function is intended for situations where text must be supplied to web services or other external systems that do not reliably accept diacritics or certain extended characters.

`VBZS_OntDiakriet` operates as a character filter with replacement:

- supported normal characters are preserved;
- supported characters with diacritics are replaced by their ASCII equivalent;
- a small number of special characters are replaced by two ASCII characters;
- characters for which no supported conversion exists are removed.

## Syntax

```text
VBZS_OntDiakriet ( text )
```

## Parameters

### `text`

The text to be converted.

## Result

Returns the filtered text with supported diacritics and special characters replaced by predefined ASCII equivalents.

Characters that cannot be preserved or converted by the function are removed from the result.

## Example

```filemaker
VBZS_OntDiakriet ( "Üßéř" )
```

returns:

```text
Uszer
```

In this example the conversions include:

```text
Ü  →  U
ß  →  sz
é  →  e
ř  →  r
```

## Diacritic replacement

Supported Latin characters containing accents, umlauts, cedillas, carons and similar diacritics are converted to their corresponding character without the diacritic.

Examples include conversions such as:

```text
á  →  a
é  →  e
Ü  →  U
ç  →  c
ř  →  r
```

Capitalization is preserved where an equivalent capital ASCII character is available.

The purpose is to convert the actual text rather than merely perform a diacritic-insensitive comparison.

## Multi-character replacements

A limited number of special characters are converted to two ASCII characters instead of a single character.

These include:

```text
ß
æ
œ
ﬀ
ﬁ
ﬂ
ﬃ
ﬄ
ﬅ
ǉ
ǌ
Þ
Ĳ
```

and their capital variants where such variants exist.

These characters require a predefined multi-character replacement because simply removing a diacritic would not produce a useful ASCII representation.

For example:

```text
ß  →  sz
```

as demonstrated by:

```text
Üßéř  →  Uszer
```

## Unsupported characters

`VBZS_OntDiakriet` is not a general-purpose Unicode transliteration function.

Characters for which the function has no supported conversion are removed.

This means that the function should be considered a filter with replacement rather than a function that attempts to find an approximate ASCII representation for every possible Unicode character.

Conceptually:

```text
Supported normal character
    → keep

Supported character with diacritic
    → replace

Supported special character
    → replace with predefined ASCII representation

Unsupported character
    → remove
```

## FileMaker searches

FileMaker itself already performs many text searches without distinguishing between common diacritic variants.

For example, a FileMaker search for:

```text
uber
```

can also find:

```text
über
```

and vice versa.

`VBZS_OntDiakriet` is therefore not primarily intended to make FileMaker searches diacritic-insensitive.

The function is useful when the text itself must actually be converted before it is stored, exported or supplied to another system.

## Use with external systems

Some external systems and web services accept only a limited character set or behave unpredictably when supplied with accented or other extended characters.

In such situations:

```filemaker
VBZS_OntDiakriet ( text )
```

can be used to create a restricted representation of the text before it is sent to the external system.

Because unsupported characters are removed, the returned value should be considered converted text rather than a lossless representation of the original value.

## Notes

- The function returns text.
- Supported diacritics are replaced by predefined ASCII equivalents.
- Capitalization is preserved where applicable.
- Some special characters are converted to two ASCII characters.
- Unsupported characters are removed.
- The function is a filter with replacement.
- The function is not a general Unicode transliteration mechanism.
- The conversion changes the actual text.
- FileMaker's own search behaviour may already be sufficient when only diacritic-insensitive searching is required.

## Design philosophy

`VBZS_OntDiakriet` was created for situations where text needs to be supplied to systems that cannot reliably handle diacritics or certain extended characters.

Its purpose is deliberately limited.

Rather than trying to understand arbitrary Unicode characters or perform language-dependent transliteration, the function uses a known set of conversions.

Characters with a supported equivalent are converted. Characters that cannot be handled by the defined character set are removed.

This makes the behaviour predictable when generating text for systems that require a restricted character set.