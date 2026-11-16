# VBZS_QualifiedEmailAddresses

## Syntax

```text
VBZS_QualifiedEmailAddresses ( ListOfEmailAddresses { ; CharacterSet } )
```

## Description

`VBZS_QualifiedEmailAddresses` validates one or more email addresses and returns only the addresses that meet the validation rules.

The function is intended for practical validation of email addresses before they are stored, processed, or used for sending email.

Multiple email addresses may be supplied in a single parameter. The function recognizes several commonly used separators, allowing input copied from fields, email applications, spreadsheets, or manually entered lists to be processed directly.

The function does not correct invalid email addresses. Invalid addresses are simply omitted from the result.

## Parameters

### ListOfEmailAddresses

One or more email addresses.

Multiple addresses may be separated by:

- FileMaker returns
- line feeds
- tabs
- spaces
- commas
- semicolons

Multiple consecutive separators are treated as separators and do not generate empty values.

### CharacterSet

Optional.

Specifies which alphabetic characters may occur in an email address.

Supported values are:

| Value | Allowed alphabetic characters |
|---|---|
| empty or omitted | Latin characters including European diacritics |
| `Latin` | ASCII Latin letters `A-Z` and `a-z` only |
| `Greek` | Default character set plus Greek |
| `Cyrillic` | Default character set plus Cyrillic |
| `All` | Default character set plus Greek and Cyrillic |

The value is not case-sensitive.

An empty or unrecognized value uses the default character set.

The `Latin` option can be used when compatibility with email systems that reject international or accented characters is required.

## Result

The function returns a return-separated list containing only the valid email addresses.

Invalid addresses are omitted.

If no valid addresses are found, the function returns an empty result.

The original spelling and capitalization of accepted addresses are preserved.

## Validation

Each email address is checked independently.

The following rules are applied:

- The address must contain exactly one `@`.
- The local part before `@` may not be empty.
- The domain after `@` may not be empty.
- The local part may contain no more than 64 characters.
- The complete email address may contain no more than 254 characters.
- Consecutive periods (`..`) are not allowed in the local part.
- The local part may not start or end with a period.
- The domain may not start or end with a period.
- The domain must contain at least one period.
- The final domain component must contain at least two characters.
- Domain labels may not start or end with a hyphen.
- The final domain component may contain alphabetic characters only.
- Characters not permitted by the selected character set are rejected.

The local part additionally supports the following characters:

```text
.!#$%&'*+-/=?^_`{|}~
```

Digits `0-9` are permitted.

Domain names are limited to alphabetic characters, digits, periods and hyphens, subject to the selected character set.

## Examples

### Single email address

```text
VBZS_QualifiedEmailAddresses ( "john@example.com" )
```

Returns:

```text
john@example.com
```

---

### Multiple addresses

```text
VBZS_QualifiedEmailAddresses (
    "john@example.com; jane@example.com,info@example.nl"
)
```

Returns:

```text
john@example.com
jane@example.com
info@example.nl
```

Semicolons and commas are automatically interpreted as separators.

Spaces, tabs and returns can also be used as separators.

---

### Invalid addresses are removed

Given:

```text
john@example.com
john..doe@example.com
john@example
john@exam_ple.com
jane@example.net
```

Using:

```text
VBZS_QualifiedEmailAddresses ( addresses )
```

Returns:

```text
john@example.com
jane@example.net
```

Invalid addresses do not generate an error and are not included in the result.

## Character sets

### Default

Without the optional `CharacterSet` parameter, European Latin characters with diacritics are accepted.

Given:

```text
john@example.com
björn@example.com
søren@example.dk
```

Using:

```text
VBZS_QualifiedEmailAddresses ( addresses )
```

Returns:

```text
john@example.com
björn@example.com
søren@example.dk
```

### Latin

Use `Latin` when accented or other non-ASCII alphabetic characters should not be accepted.

```text
VBZS_QualifiedEmailAddresses ( addresses ; "Latin" )
```

For:

```text
john@example.com
björn@example.com
søren@example.dk
```

Returns:

```text
john@example.com
```

`Latin` permits only the alphabetic characters:

```text
A-Z
a-z
```

This restriction applies to alphabetic characters only. Digits and permitted email punctuation remain available.

### Greek

```text
VBZS_QualifiedEmailAddresses ( addresses ; "Greek" )
```

The `Greek` option permits the default European Latin character set and Greek characters.

For example:

```text
john@example.com
björn@example.com
δοκιμή@example.com
```

all three addresses are accepted.

Greek characters with accents and polytonic Greek characters are supported.

### Cyrillic

```text
VBZS_QualifiedEmailAddresses ( addresses ; "Cyrillic" )
```

The `Cyrillic` option permits the default European Latin character set and Cyrillic characters.

For example:

```text
john@example.com
björn@example.com
тест@example.com
```

all three addresses are accepted.

### All

```text
VBZS_QualifiedEmailAddresses ( addresses ; "All" )
```

`All` permits:

- Latin
- European Latin characters with diacritics
- Greek
- Cyrillic

For example:

```text
john@example.com
björn@example.com
søren@example.dk
δοκιμή@example.com
тест@example.com
```

all five addresses are accepted.

## Input normalization

`VBZS_QualifiedEmailAddresses` is designed to handle lists entered by users.

For example:

```text
john@example.com; jane@example.com, info@example.nl sales@example.net
```

is interpreted as four individual email addresses.

The recognized separators are logically treated as FileMaker value separators before the individual addresses are validated.

The function does not remove invalid characters in order to make an address valid.

For example:

```text
john @example.com
```

is not silently changed into:

```text
john@example.com
```

The function validates the supplied values rather than correcting them.

## Notes

`VBZS_QualifiedEmailAddresses` is intended as a practical email-address validator.

Email standards permit constructs and international character combinations that are not necessarily accepted by every mail server, mail client, or receiving environment.

For this reason, the function deliberately applies a restricted and predictable set of validation rules rather than attempting to accept every theoretically valid email-address syntax.

The default character set supports European Latin characters with diacritics. The `Latin` option provides a more restrictive compatibility mode for environments where accented characters in email addresses are not accepted.

The `Greek`, `Cyrillic`, and `All` options should be used when the target email infrastructure is known to support these character sets.

The function validates the syntax and character usage of an email address. It does not verify whether:

- the domain exists;
- the domain has mail servers;
- the mailbox exists;
- the receiving server accepts the address;
- an email can actually be delivered.

## See also

The output of `VBZS_QualifiedEmailAddresses` is a FileMaker return-separated value list and can therefore be used directly with FileMaker list and value functions such as `GetValue`, `ValueCount`, and `FilterValues`.