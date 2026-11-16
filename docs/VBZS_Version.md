# VBZS_Version

## Description

`VBZS_Version` returns the version number of the installed VBZS plug-in.

The version can be returned in two different representations:

- a normal human-readable version;
- a fixed-length representation intended for version checking and auto-update routines.

## Syntax

```text
VBZS_Version ( parameter )
```

## Parameters

### `parameter`

Determines how the version number is returned.

Use an empty parameter for the normal version representation:

```text
""
```

Use either:

```text
1
```

or:

```text
"autoupdate"
```

for the fixed-length auto-update representation.

## Standard version representation

With an empty parameter:

```filemaker
VBZS_Version ( "" )
```

the version is returned in its normal human-readable form.

For example:

```text
0.9.1.1
```

The VBZS version consists of four numeric components separated by periods.

## Auto-update representation

Using:

```filemaker
VBZS_Version ( 1 )
```

or:

```filemaker
VBZS_Version ( "autoupdate" )
```

returns the same version as a fixed 8-digit representation.

For example:

```text
0.9.1.1
```

is returned as:

```text
00090101
```

Each of the four version components occupies two positions:

```text
0  →  00
9  →  09
1  →  01
1  →  01
```

Together:

```text
00 09 01 01
```

becomes:

```text
00090101
```

## Version comparison

The fixed-length representation is intended to make version checking easier.

For example, these versions:

```text
0.9.1.1
0.9.3.4
0.10.0.0
1.0.0.0
```

are represented as:

```text
00090101
00090304
00100000
01000000
```

Because every version component always occupies two positions, the representations can be compared without first extracting the individual components from the normal dotted version number.

This makes the format useful for determining whether an installed plug-in should be replaced by a newer version.

## Example

To display or retrieve the normal version:

```filemaker
VBZS_Version ( "" )
```

returns, for example:

```text
0.9.1.1
```

For version checking:

```filemaker
VBZS_Version ( 1 )
```

returns:

```text
00090101
```

The same representation can be requested using:

```filemaker
VBZS_Version ( "autoupdate" )
```

which also returns:

```text
00090101
```

## Notes

- The version consists of four numeric components.
- Each component has a range of `0` through `99`.
- The normal representation separates the components with periods.
- An empty parameter returns the normal version representation.
- `1` returns the fixed-length auto-update representation.
- `"autoupdate"` also returns the fixed-length auto-update representation.
- The auto-update representation always contains eight digits.
- Each version component occupies exactly two positions.
- Leading zeros are added where required.
- The fixed-length representation is intended for version checking and update routines.

## Design philosophy

A normal dotted version number is easy to read:

```text
0.9.1.1
```

but is less convenient when a FileMaker solution needs to determine whether the installed plug-in should be replaced by another version.

`VBZS_Version` therefore provides a second representation specifically for version checking.

The four version components are converted into fixed-width two-digit values:

```text
0.9.1.1
```

becomes:

```text
00090101
```

This keeps the normal version readable for users while providing a predictable representation for automated version comparisons.