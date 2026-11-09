# VBZS FileMaker Plug-in

VBZS is a cross-platform plug-in for FileMaker that provides additional calculation functions for common development tasks.

The plug-in focuses on small, practical functions that simplify operations which are missing from FileMaker, require unnecessarily complex calculations, or are useful enough to provide as native plug-in functions.

This documentation describes the individual VBZS functions, their parameters, behaviour, limitations and practical use.

You can [download the MacOS plugin directly here](Binairy_0.9.4.0/VBZS_fmplugin_macOS_0940.zip), it is codesigned and notarized. 

To download [the Windows version pleas use this link](Binairy_0.9.4.0/VBZS_fmx64_Windows_0940.zip), but  be aware that the Windows version has not yet been codesigned. The certificate has been requested, but will take up until November 20 to be awarded and I will published the signed version a.s.a.p..

## Function Reference

Functions are listed alphabetically.

| Function | Description |
|---|---|
| [VBZS_AddWorkingDays](docs/VBZS_AddWorkingDays.md) | Adds working days to a date using a configurable working-week pattern. |
| [VBZS_Ascii](docs/VBZS_Ascii.md) | Filters text using predefined character sets for general text, email addresses and XML. |
| [VBZS_DateFormat](docs/VBZS_DateFormat.md) | Converts a FileMaker date to text using a configurable date-format template. |
| [VBZS_Easter](docs/VBZS_Easter.md) | Calculates Easter Sunday for a given year. |
| [VBZS_Fraction](docs/VBZS_Fraction.md) | Returns the fractional digits of a number as fixed-length text. |
| [VBZS_FrontTabPanels](docs/VBZS_FrontTabPanels.md) | Returns the named tab and slide panels currently visible on a FileMaker layout. |
| [VBZS_JSONParameters](docs/VBZS_JSONParameters.md) | Creates FileMaker variables from JSON objects and arrays, with support for namespaces and repeating variables. |
| [VBZS_ListLogic](docs/VBZS_ListLogic.md) | Compares two lists of values using `AND`, `XOR` or `NOT` logic. |
| [VBZS_Local2UTC](docs/VBZS_Local2UTC.md) | Formats or converts a local timestamp to ISO 8601 or UTC/Zulu representations. |
| [VBZS_ModKeys](docs/VBZS_ModKeys.md) | Makes the current FileMaker modifier-key states available as separate variables. |
| [VBZS_OntDiakriet](docs/VBZS_OntDiakriet.md) | Filters text while replacing supported diacritics and special characters with ASCII equivalents. |
| [VBZS_ResponseCode](docs/VBZS_ResponseCode.md) | Extracts the HTTP response code from an `Insert from URL` response header and creates response variables. |
| [VBZS_QualifiedEmailAddresses](docs/VBZS_QualifiedEmailAddresses.md) | Validates one or more email addresses and returns only the addresses that meet the validation rules |
| [VBZS_TrimAll_CR](docs/VBZS_TrimAll_CR.md) | Removes empty values from a return-delimited FileMaker list without modifying spaces. |
| [VBZS_UTC2Local](docs/VBZS_UTC2Local.md) | Converts a UTC timestamp or numeric UTC value to local time with European DST correction. |
| [VBZS_Version](docs/VBZS_Version.md) | Returns the installed VBZS version in readable or fixed-length version-comparison format. |
| [VBZS_WeekFiscalToDate](docs/VBZS_WeekFiscalToDate.md) | Returns the starting date of a specified week using configurable ISO-style week numbering. |
| [VBZS_XChildren](docs/VBZS_XChildren.md) | Returns the direct child elements and attributes of an XML element. |
| [VBZS_XPath](docs/VBZS_XPath.md) | Retrieves content from XML using XPath with simplified namespace handling. |
| [VBZS_YearFiscal](docs/VBZS_YearFiscal.md) | Returns the ISO-style week-numbering year for a given date. |

## Function Groups

### Dates and Times

- [VBZS_AddWorkingdays](docs/VBZS_AddWorkingdays.md)
- [VBZS_DateFormat](docs/VBZS_DateFormat.md)
- [VBZS_Easter](docs/VBZS_Easter.md)
- [VBZS_Local2UTC](docs/VBZS_Local2UTC.md)
- [VBZS_UTC2Local](docs/VBZS_UTC2Local.md)
- [VBZS_WeekFiscalToDate](docs/VBZS_WeekFiscalToDate.md)
- [VBZS_YearFiscal](docs/VBZS_YearFiscal.md)

### Text and Lists

- [VBZS_Ascii](docs/VBZS_Ascii.md)
- [VBZS_Fraction](docs/VBZS_Fraction.md)
- [VBZS_ListLogic](docs/VBZS_ListLogic.md)
- [VBZS_OntDiakriet](docs/VBZS_OntDiakriet.md)
- [VBZS_QualifiedEmailAddresses](docs/VBZS_QualifiedEmailAddresses.md) 
- [VBZS_TrimAll_CR](docs/VBZS_TrimAll_CR.md)

### XML

- [VBZS_XChildren](docs/VBZS_XChildren.md)
- [VBZS_XPath](docs/VBZS_XPath.md)

### JSON

- [VBZS_JSONParameters](docs/VBZS_JSONParameters.md)

### FileMaker Interface and Environment

- [VBZS_FrontTabPanels](docs/VBZS_FrontTabPanels.md)
- [VBZS_ModKeys](docs/VBZS_ModKeys.md)

### HTTP and Integration

- [VBZS_ResponseCode](docs/VBZS_ResponseCode.md)

### Plug-in Information

- [VBZS_Version](docs/VBZS_Version.md)

## General Conventions

VBZS functions are designed to follow normal FileMaker conventions wherever practical.

Function documentation describes the supported behaviour of each function. An implementation may sometimes accept input outside the documented range or format, but undocumented behaviour should not be relied upon.

Examples use FileMaker calculation syntax and return-delimited lists are represented using the FileMaker paragraph character:

```text
¶
```

## Versioning

The installed VBZS version can be retrieved using:

```filemaker
VBZS_Version ( "" )
```

For automated version comparison:

```filemaker
VBZS_Version ( 1 )
```

or:

```filemaker
VBZS_Version ( "autoupdate" )
```

See docs/VBZS_Version.md for details.

## License

VBZS is proprietary, closed-source software provided free of charge.

This version may be used free of charge in personal, professional and commercial FileMaker solutions under the terms of the VBZS Freeware License.

The source code is not distributed and remains the intellectual property of the author.

The license applies to the VBZS version with which it is distributed. Versions released under the VBZS Freeware License remain licensed under those terms.

Future versions of VBZS may be released under different license terms.

See [LICENSE.md](LICENSE.md) for the complete license terms.