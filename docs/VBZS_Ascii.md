# VBZS_Ascii

## Description

`VBZS_Ascii` is a simple whitelist-based character filter.

It is intended to clean text that may contain unwanted characters, particularly invisible characters introduced by copy/paste operations, imported data, external systems or character encoding.

The function is deliberately simple. It does not try to interpret, repair or intelligently convert the supplied text. Apart from a few explicitly handled characters, anything that is not allowed by the selected filter is simply removed.

Three filtering modes are available:

- default
- `eml`
- `xml`

## Syntax

```text
VBZS_Ascii ( text ; type )
```

## Parameters

### `text`

The text to be filtered.

Unsupported characters are removed from the result.

A few characters receive special treatment:

- non-breaking spaces are converted to normal spaces;
- a BOM (Byte Order Mark) is removed;
- other characters that are not allowed by the selected filter are removed.

### `type`

Determines which character filter is used.

The following values are supported:

| `type` | Filter |
|---|---|
| `""` | Default character filter |
| `"eml"` | Email character filter |
| `"xml"` | XML character filter |

Any other value is ignored and causes the default filter to be used.

## Default filter

When `type` is empty, the general-purpose character filter is used:

```filemaker
VBZS_Ascii ( text ; "" )
```

The default filter retains:

- upper- and lowercase Latin letters;
- commonly used accented characters;
- digits;
- common punctuation;
- a limited set of symbols;
- spaces;
- tabs;
- FileMaker carriage returns.

The character set includes:

```text
abcdefghijklmnopqrstuvwxyz
áàâäãåçéèêëíìîïñóòôöõøúùûüý
ABCDEFGHIJKLMNOPQRSTUVWXYZ
ÁÀÂÄÃÅÇÉÈÊËÍÌÎÏÑÓÒÔÖÕØÚÙÛÜÝ
0123456789
!"#$%&'()*+,-./ :;<=>?@[\]^_`{|}~
¢£¤¥¦§©¬®°±¶
```

Tab characters (`Char ( 9 )`) and FileMaker carriage returns (`Char ( 13 )`) are also preserved.

Line feed characters (`Char ( 10 )`) are removed.

## Email filter

Use `eml` to apply a more restrictive filter intended for cleaning text that will be used as an email address:

```filemaker
VBZS_Ascii ( text ; "eml" )
```

The email filter retains:

```text
abcdefghijklmnopqrstuvwxyz
áàâäãåçéèêëíìîïñóòôöõøúùûüý
ABCDEFGHIJKLMNOPQRSTUVWXYZ
ÁÀÂÄÃÅÇÉÈÊËÍÌÎÏÑÓÒÔÖÕØÚÙÛÜÝ
0123456789
@._
```

Tabs, carriage returns, line feeds, spaces and other punctuation are removed.

> **Important:** `eml` is a character filter, not an email address validator.

The function only removes characters that are not included in its email character set. It does not check the structure of the resulting address.

For example, the presence or position of `.`, `_` or `@` is not validated. A string that passes through the filter is therefore not necessarily a valid email address.

Use `eml` to **clean** an email address, not to validate one.

## XML filter

Use `xml` for text containing XML:

```filemaker
VBZS_Ascii ( text ; "xml" )
```

The XML filter preserves the broader character set required when filtering XML content, including XML markup characters such as `<` and `>`.

The character set includes:

```text
abcdefghijklmnopqrstuvwxyz
áàâäãåçéèêëíìîïñóòôöõøúùûüý
ABCDEFGHIJKLMNOPQRSTUVWXYZ
ÁÀÂÄÃÅÇÉÈÊËÍÌÎÏÑÓÒÔÖÕØÚÙÛÜÝ
0123456789
@._
<>"!#$%&'()*+,-./ :;<=>?@[\]^_`{|}~
¢£¤¥¦§©¬®°±¶
```

Tab characters (`Char( 9 )`) and FileMaker carriage returns (`Char ( 13 )`) are preserved.

Line feed characters (`Char ( 10 )`) are removed.

> **Important:** `xml` is a character filter. It does not validate, parse or repair XML.

Passing text through:
```filemaker
VBZS_Ascii ( text ; "xml" )
```

does not guarantee that the resulting text is well-formed XML.

Use the XML mode to remove unwanted characters from XML text, not to correct an invalid XML document.

## Carriage returns and whitespace

Whitespace handling depends on the selected mode:

| Character | default | `eml` | `xml` |
|---|:---|:---:|:---:|
| Normal space | Preserved | Removed | Preserved |
| Non-breaking space | Converted to normal space | Removed | Converted to normal space |
| Tab (`Char ( 9 )`)| Preserved | Removed | Preserved
| Carriage return (`Char ( 13 )`)| Preserved | Removed | Preserved
| Line feed (`Char ( 10 )`) | Removed | Removed | Removed |

This is particularly relevant when text originates outside FileMaker and contains different line-ending conventions.

## BOM removal

A BOM (Byte Order Mark) is removed from the supplied text.

BOM characters can occur when text originates from files of external systems using Unicode encodings. They are normally invisible, but can interfere with comparisons, parsing or other text processing.

`VBZS_Ascii` removes them instead of returning them as part of the cleaned text.

## Unsupported characters

Characters not included in the selected filter are simply remomed.

The function does not normally attempt to replace an unsupported character with a visually similar supported character.

For example, unsupported Unicode characters, unusual invisible characters or other characters outside the selected whitelist simply disappear from the result.

This behaviour is intentional.

## Examples

### General text cleaning

```filemaker
VBZS_Ascii ( text ; "" )
```

uses the default character set and removes characters outside that set.

This can be useful when imported or copied text contains unwanted invisible characters that are difficult to locate from within FileMaker.

### Cleaning an email address

```filemaker
VBZS_Ascii (
    email ;
    "eml"
)
```
removes characters outside the email filter.

The result is cleaned text intended for use as an email address. It is not a guarantee that the result actually represents a valid email address.

### Cleaning XML text

```filemaker
VBZS_Ascii (
    xml ;
    "xml"
)
```

retains the supported characters used in XML content and markup while removing characters outside the XML filter.

The result is not automatically guaranteed to be well-formed XML.

## Unknown filter types

If `type` contains any value other than:

```text
""
"eml"
"xml"
```

the value is ignored and the default filter is used.

For example:

```filemaker
VBZS_Ascii (
    text ;
    "something"
)
```

behaves as if the calculation were:

```filemaker
VBZS_Ascii(
    text ;
    ""
)
```

## Note!

- The function uses predefined character whitelists.
- Unsupported characters are removed rather than interpreted.
- Non-breaking spaces are converted to normal spaces where spaces are permitted.
- BOM characters are removed.
- `Char ( 9 )` is preserved by the default and `xml` filters.
- `Char ( 13 )` is preserved by the default and `xml` filters.
- `Char ( 10 )` is removed.
- `xml` cleans characters but does not validate an email address.
- `xml` cleans characters but does not validate or repair XML.
- An unknown `type` value falls back to the default filter.

## Design philosophy

`VBZS_Ascii` is deliberately a simple function.

Text obtained from external sources can contain invisible or unexpected characters that are difficult to identify in FileMaker and can cause problems during comparison, parsing, exporting or further processing.

The purpose of `VBZS_Ascii` is not to understand those characters. The function simply rettins a known set of useful characters and removes everything else.

The `eml` and `xml` modes provide more specialised whitelists, but follow exactly the same principle:

**keep what is expected, remove what is not, and do not pretend to validate the resulting data.**