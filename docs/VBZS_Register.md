# VBZS_Register

## Description

`VBZS_Register` loads and activates a VBZS license for the current FileMaker session.

The function validates a cryptographically signed VBZS license code and, when valid, makes the licensed VBZS functions immediately available.

Runtime registration is supported for all VBZS license types:

- Standard
- Developer
- Enterprise

A license loaded with `VBZS_Register` is held in memory. The function does not create or modify `license.json`.

For Developer and Enterprise licenses, `VBZS_Register` is the required registration method.

## Syntax

```text
VBZS_Register ( order ; licenseCode )
```

## Parameters

### order

The order number belonging to the license.

For example:

```text
VBZS-2026-00127
```

The supplied order number must exactly match the order number contained in the signed license code.

### licenseCode

The complete VBZS license code supplied with the license.

A VBZS license code uses the following general format:

```text
VBZS1.payload.signature
```

The license code contains cryptographically protected license information, including:

- licensee;
- country;
- order number;
- license date;
- license generation;
- license type;
- number of licensed seats.

The license code should always be passed to `VBZS_Register` unchanged.

## Result

Returns:

```text
1
```

when the license has been successfully validated and registered.

Returns:

```text
0
```

when registration fails.

A failed registration does not remove or replace an already active valid license.

## Example

```filemaker
VBZS_Register (
    "VBZS-2026-00127" ;
    $licenseCode
)
```

When `$licenseCode` contains the valid license issued for order:

```text
VBZS-2026-00127
```

the function returns:

```text
1
```

The license becomes active immediately.

No restart of FileMaker is required.

---

# Runtime registration

`VBZS_Register` creates a runtime registration.

This means the validated license is stored in memory for the current FileMaker process.

It is not written to disk.

When FileMaker Pro is closed, the runtime registration disappears.

Likewise, a runtime registration on FileMaker Server disappears when the relevant FileMaker Server process or service is restarted.

The solution should therefore execute `VBZS_Register` again when a new runtime session begins if the license depends on runtime registration.

---

# License types

## Standard License

A Standard License may be registered using:

```text
license.json
```

or:

```filemaker
VBZS_Register ( order ; licenseCode )
```

When a Standard License is registered using `VBZS_Register`, it behaves as a runtime license for that session.

`VBZS_Register` itself never writes or modifies `license.json`.

## Developer License

A Developer License may only be activated through runtime registration.

Use:

```filemaker
VBZS_Register ( order ; licenseCode )
```

A Developer License is intended for a developer who distributes VBZS as part of a FileMaker solution.

The license is valid for the licensed number of seats, up to the applicable Developer License limit.

The FileMaker solution should execute `VBZS_Register` whenever a new session requires the license.

A Developer License cannot be activated automatically from `license.json`.

## Enterprise License

An Enterprise License may only be activated through runtime registration.

Use:

```filemaker
VBZS_Register ( order ; licenseCode )
```

An Enterprise License is not loaded from `license.json`.

The FileMaker solution should execute `VBZS_Register` whenever a new session requires the license.

---

# FileMaker Server

FileMaker Server uses runtime registration only.

A local `license.json` file is not used for Server registration.

A server-side solution should therefore execute:

```filemaker
VBZS_Register ( order ; licenseCode )
```

as part of its initialization process.

This design avoids relying on direct access to the FileMaker Server file system and also works in hosted and cloud environments where customers may not have access to server directories.

---

# Runtime registration has priority

If FileMaker Pro starts with a valid Standard License loaded from `license.json`, that license initially becomes active.

A subsequent successful call to:

```filemaker
VBZS_Register ( order ; licenseCode )
```

replaces the active license in memory for the remainder of that session.

For example:

```text
FileMaker Pro starts
→ Standard License loaded from license.json
→ Source: File
```

Later:

```text
VBZS_Register receives another valid license
→ Source: Runtime
```

The runtime license then becomes the active license.

The existing `license.json` is not deleted or modified.

At the next FileMaker Pro start, the runtime registration is gone and the local Standard License can be loaded again.

---

# Failed registration

A failed registration never invalidates an already active license.

For example:

```text
valid Standard License active
→ invalid VBZS_Register call
→ Standard License remains active
```

Likewise:

```text
valid Runtime License active
→ invalid VBZS_Register call
→ existing Runtime License remains active
```

The new license is only installed in memory after all required validation has succeeded.

---

# Validation

Before a license is accepted, VBZS validates the license code.

The validation includes:

1. Recognizing the VBZS license-code format.
2. Decoding the signed payload.
3. Verifying the RSA/SHA-512 digital signature.
4. Parsing the protected license information.
5. Confirming that the license belongs to VBZS.
6. Comparing the supplied order number with the signed order number.
7. Verifying that the license generation is valid for the installed VBZS version.

Only after these checks succeed is the license made active.

---

# License generation

VBZS licenses contain an internal license generation number.

This generation is independent of the normal VBZS software version.

For example, several VBZS releases may all use license generation 1:

```text
VBZS 0.9.4.1 → generation 1
VBZS 0.9.5.0 → generation 1
VBZS 1.0.0.0 → generation 1
```

A later commercial release may intentionally require:

```text
generation 2
```

A newer license may always be used with a VBZS release requiring an older generation.

For example:

```text
Generation 2 license
Generation 1 required
→ valid
```

The reverse is not valid:

```text
Generation 1 license
Generation 2 required
→ invalid
```

License generation is an internal VBZS licensing mechanism and does not need to be managed by the FileMaker solution.

---

# Cryptographic protection

The license information is part of the signed license code.

Changing information such as:

```text
licensee
license type
seats
order
generation
```

without generating a new valid signature makes the license code invalid.

The FileMaker solution does not need to verify these values separately.

VBZS performs the cryptographic verification internally.

---

# Recommended solution initialization

For Developer and Enterprise licenses, a solution can register VBZS from an initialization script.

For example:

```filemaker
Set Variable [
    $registered ;
    Value:
        VBZS_Register (
            Settings::VBZSOrder ;
            Settings::VBZSLicenseCode
        )
]
```

The result can then be tested:

```filemaker
If [ $registered = 1 ]
    # VBZS registration succeeded
Else
    # VBZS registration was not accepted
End If
```

The registration data may be stored in a location appropriate to the solution's own security model.

---

# Relationship with the trial period

When no valid license is available, VBZS operates in trial mode.

When the trial period expires, licensed VBZS functions are no longer executed.

A successful call to:

```filemaker
VBZS_Register ( order ; licenseCode )
```

immediately activates the license.

Licensed functions can then be used again without restarting FileMaker.

---

# Relationship with VBZS_LicenseInfo

After registration, use:

```filemaker
VBZS_LicenseInfo
```

to retrieve information about the active license.

A license loaded using `VBZS_Register` is reported with:

```text
Source: Runtime
```

---

# Notes

- Registration is performed entirely by VBZS.
- A successful registration takes effect immediately.
- `VBZS_Register` does not require a FileMaker restart.
- `VBZS_Register` does not write `license.json`.
- Standard, Developer, and Enterprise licenses may all be registered at runtime.
- Developer and Enterprise licenses require runtime registration.
- FileMaker Server uses runtime registration only.
- An invalid registration does not remove an already valid license.
- Runtime registration has priority over a previously loaded file-based license.
- Runtime registration lasts until the FileMaker process or relevant Server process ends.
- The license code must not be modified.
- The private key used to issue VBZS licenses is not present in the plug-in.
- VBZS contains only the public information required to verify authentic licenses.

## See also

- `VBZS_LicenseInfo`
- `VBZS_Version`