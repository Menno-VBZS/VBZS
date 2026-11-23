# VBZS FileMaker Plug-in

VBZS requires a valid license for continued use. Without a valid license, the plug-in operates for a limited trial period. Standard, Developer and Enterprise licenses are available for different deployment scenarios.

The plug-in has been tested with FileMaker 2024, 2025, and 2026 (versions 21.x, 22.x, and 26.x). It may also work with older versions of FileMaker, but is unlikely to work with versions older than 19.6.

## Downloads

You can [download the MacOS plugin directly here](Binairy/VBZS_fmplugin_macOS.zip), it's codesigned and notarized. 

To download [the Windows version please use this link](Binairy/VBZS_fmx64_Windows.zip), codesigned.

For Ubuntu separate versions for each architecture are available [one for Ubuntu 24.04 AMD/Intel](Binairy/VBZS_ubuntu_amd64.zip) and [the other for Ubuntu 24.04 ARM64](Binairy/VBZS_ubuntu_arm64.zip) 

The current version is 0.9.5.0


## Check your download

Please use [the checksums provided in this file: SHA256SUMS](Binairy/SHA256SUMS) to check  any of the downloads, by placing the downloaded plugin and the [SHA256SUMS](Binairy/SHA256SUMS) in the same directory 

and run a terminal/commandline with: ```sha256sum -c --ignore-missing SHA256SUMS``` 

the result should be one of these: 
```VBZS_fmplugin_macOS.zip: OK```
```VBZS_fmx64_Windows.zip: OK``` 
```VBZS_ubuntu_amd64.zip: OK```
```VBZS_ubuntu_arm64.zip: OK```

## Function Reference

Plugin functions listed alphabetically:

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
| [VBZS_WeekFiscalToDate](docs/VBZS_WeekFiscalToDate.md) | Returns the starting date of a specified week using configurable ISO-style week numbering. |
| [VBZS_XChildren](docs/VBZS_XChildren.md) | Returns the direct child elements and attributes of an XML element. |
| [VBZS_XPath](docs/VBZS_XPath.md) | Retrieves content from XML using XPath with simplified namespace handling. |
| [VBZS_YearFiscal](docs/VBZS_YearFiscal.md) | Returns the ISO-style week-numbering year for a given date. |

Plugin information and registration:

| Function | Description | 
|---|---|
| [VBZS_LicenseInfo](docs/VBZS_LicenseInfo.md) | Returns information about the installed VBZS version and the currently active license |
| [VBZS_Register](docs/VBZS_Register.md) | Loads and activates a VBZS license for the current FileMaker session |
| [VBZS_Version](docs/VBZS_Version.md) | Returns the installed VBZS version in readable or fixed-length version-comparison format. |

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

- [VBZS_LicenseInfo](docs/VBZS_LicenseInfo.md)
- [VBZS_Register](docs/VBZS_Register.md) 
- [VBZS_Version](docs/VBZS_Version.md)

## General Conventions

VBZS functions are designed to follow normal FileMaker conventions wherever practical.

Function documentation describes the supported behaviour of each function. An implementation may sometimes accept input outside the documented range or format, but undocumented behaviour should not be relied upon.

Examples use FileMaker calculation syntax and return-delimited lists are represented using the FileMaker paragraph character:

```text
¶
```

## License

VBZS is proprietary, closed-source software.

A valid license is required for continued use of the licensed VBZS functions. Without a valid license, VBZS operates for a limited trial period.

VBZS licenses are cryptographically signed and contain the licensee, license type, licensed number of seats and the license generation for which the license was issued.

##### Licenses will be available from early december 2026 on website: [https://customers.vbzs.nl/vbzsplugin.html](https://customers.vbzs.nl/vbzsplugin.html)

### Standard License

A Standard License is intended for normal use by an individual or organization and supports between 1 and 25 licensed users.

A Standard License can be registered in two ways:

- by installing a valid `license.json` for FileMaker Pro;
- at runtime using `VBZS_Register`.

A Standard License entered through the VBZS registration dialog in FileMaker Pro is stored locally in `license.json` and is automatically loaded the next time FileMaker Pro starts.

### Developer License

A Developer License is intended for FileMaker developers who distribute VBZS as part of their own FileMaker solution.

Developer licenses support between 1 and 25 licensed users.

Developer licenses are runtime licenses and must be activated using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

They are not loaded from `license.json`.

A Developer License entered through the VBZS registration dialog is therefore valid for the current session only. The FileMaker solution should use `VBZS_Register` to register VBZS when a new session starts.

### Enterprise License

An Enterprise License is intended for deployments that require more than 25 licensed users.

Enterprise licenses are runtime licenses and must be activated using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

They are not loaded from `license.json`.

An Enterprise License entered through the VBZS registration dialog is therefore valid for the current session only. The FileMaker solution should use `VBZS_Register` to register VBZS when a new session starts.

### FileMaker Server

FileMaker Server uses runtime registration only.

Server-side solutions should register VBZS using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

This also makes registration suitable for hosted and cloud environments where direct access to the FileMaker Server file system may not be available.

### Trial period

When no valid license is available, VBZS operates in trial mode.

During the trial period, the licensed VBZS functions remain available.

After the trial period expires, licensed functions are disabled until a valid license is registered. License and version functions remain available so that VBZS can still be identified and registered.

A valid registration takes effect immediately and does not require FileMaker to be restarted.

### License generation

VBZS uses an internal license generation system that is independent of the normal plug-in version number.

A newer license generation may be used with VBZS versions that require an older generation. An older license generation cannot be used with a VBZS version that requires a newer generation.

License generations are changed only when a new license generation is intentionally introduced and are not automatically tied to normal VBZS version updates.

### License verification

License information is protected by a digital signature.

Changing protected information in a license, including the licensee, license type, number of seats or license generation, invalidates the license unless a new valid license signature is issued.

See [VBZS_Register](docs/VBZS_Register.md) and [VBZS_LicenseInfo](docs/VBZS_LicenseInfo.md) for information about registering VBZS and inspecting the active license.

See [LICENSE.md](LICENSE.md) for the complete legal license terms.