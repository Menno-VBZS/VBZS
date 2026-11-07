# VBZS_XChildren

## Description

`VBZS_XChildren` returns the names of the direct child elements in a supplied XML element.

The function is intended to help FileMaker developers explore the structure of an XML document one level at a time.

XML namespace prefixes are returned as part of the element name. Attributes belonging to a child element are listed immediately below that element and are prefixed with `@`.

## Syntax

```text
VBZS_XChildren ( xml )
```

## Parameters

### `xml`

A well-formed XML document or XML element to inspect.

## Result

Returns a return-delimited list containing:

- the names of all direct child elements;
- the namespace prefix of each element, when present;
- the attributes belonging to each child element.

Attributes are prefixed with `@` and appear directly below the element to which they belong.

The function only returns direct children. It does not recursively list descendants at deeper levels.

## Example

Given the following XML:

```xml
<Invoice>
    <cbc:ID schemeID="invoice">12345</cbc:ID>
    <cac:Customer>
        <cbc:Name>Acme BV</cbc:Name>
    </cac:Customer>
</Invoice>
```

The following calculation:

```filemaker
VBZS_XChildren ( xml )
```

returns:

```text
cbc:ID
@schemeID
cac:Customer
```

The `cbc:Name` element is not included because it is not a direct child of `Invoice`. It is a child of `cac:Customer`.

## Exploring an unknown XML document

`VBZS_XChildren` is designed to help explore unfamiliar XML without first knowing its complete structure.

A typical workflow is:

1. Supply the complete XML document to `VBZS_XChildren`.
2. Review the elements at the current level.
3. Select the relevant element.
4. Inspect that element or construct an XPath to its children.
5. Repeat until the required value has been located.

For example, an initial call might reveal:

```text
cac:AccountingCustomerParty
```

Inspecting that part of the XML may then reveal:

```text
cac:Party
```

The next level may contain:

```text
cbc:Name
cbc:CompanyID
```

Once the required element has been identified, `VBZS_XPath` can retrieve its value from the original XML document.

## Namespace handling

VBZS treats an XML namespace prefix as part of the element name from the FileMaker developer's point of view.

For example:

```xml
<cac:Customer>
```

is returned as:

```text
cac:Customer
```

There is no separate namespace registration step.

This allows developers to work with the element names exactly as they appear in the XML document.

## Important

The supplied XML must be well-formed.

`VBZS_XChildren` examines only the direct children of the supplied XML element. To inspect a deeper level, supply the relevant well-formed element or use `VBZS_XPath` to address the required path in the original XML document.

## Related functions

- `VBZS_XPath`