# VBZS_LicenseInfo

## Description

`VBZS_LicenseInfo` returns information about the installed VBZS version and the currently active license.

The function reports:

- the installed VBZS version;
- the licensee;
- the country;
- the license date;
- the license type;
- the number of licensed seats, where applicable;
- the source of the active license.

`VBZS_LicenseInfo` remains available when the trial period has expired.

## Syntax

```text
VBZS_LicenseInfo
```

## Parameters

None.

## Return value

Returns return-delimited text containing the VBZS version and current license information.

For example:

```text
0.9.4.1
Licensed to: Van Beek Business Software NL 2026-11-14
Standard License - 17 seats
Source: File
```

For an unlicensed installation:

```text
0.9.4.1
Unlicensed
Source: Trial
```

## License sources

The final line identifies how the current license was activated.

### Trial

```text
Source: Trial
```

No valid license is currently active.

### File

```text
Source: File
```

A Standard License was loaded from the local `license.json` when FileMaker Pro started.

### Runtime

```text
Source: Runtime
```

The current license was registered in memory, for example using:

```filemaker
VBZS_Register ( order ; licenseCode )
```

A Runtime license remains active for the current FileMaker session only.

## License types

`VBZS_LicenseInfo` may report:

```text
Standard License - 17 seats
Developer License - 12 seats
Enterprise License
```

Developer and Enterprise licenses are always Runtime licenses.

## License information

The displayed license information is obtained from the cryptographically verified license code.

For a file-based Standard License, descriptive values stored separately in `license.json` are not used as the authoritative license information.

## Notes

- The function has no parameters.
- The first line always contains the installed VBZS version.
- The result uses FileMaker return characters between lines.
- License generation and order number are not displayed.
- `VBZS_LicenseInfo` is available even when the trial period has expired.
- A license registered with `VBZS_Register` is reported as `Source: Runtime`.

## See also

- `VBZS_Register`
- `VBZS_Version`