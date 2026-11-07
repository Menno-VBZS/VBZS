# VBZS_XPath

## Description

`VBZS_XPath` evaluates an XPath expression against a supplied XML document and returns the raw content selected by that expression.

The function is designed to make XML navigation straightforward for FileMaker developers. Namespace prefixes can be used directly in the XPath as they appear in the XML document. No separate namespace registration is required.

## Syntax

```text
VBZS_XPath ( xml ; xpath )
```

## Parameters

### `xml`

The well-formed XML document to query.

### `xpath`

The XPath expression identifying the element or content to return.

## Result

Returns the raw content selected by the XPath expression.

If the selected element contains plain text or a number, that value is returned directly.

If the selected element contains child elements, their raw XML content is returned. This output is not guaranteed to be a well-formed XML document.

## Basic example

Given:

```xml
<Invoice>
    <cbc:ID>12345</cbc:ID>
</Invoice>
```

The following calculation:

```filemaker
VBZS_XPath (
    xml ;
    "/Invoice/cbc:ID"
)
```

returns:

```text
12345
```

## Namespace handling

Namespace prefixes can be used directly in the XPath expression.

Given an XML structure such as:

```xml
<Invoice>
    <cac:AccountingCustomerParty>
        <cac:Party>
            <cbc:Name>Acme BV</cbc:Name>
        </cac:Party>
    </cac:AccountingCustomerParty>
</Invoice>
```

the value can be retrieved using:

```filemaker
VBZS_XPath (
    xml ;
    "/Invoice/cac:AccountingCustomerParty/cac:Party/cbc:Name"
)
```

The result is:

```text
Acme BV
```

There is no need to register `cac` or `cbc` separately. Use the prefixes exactly as they appear in the XML document.

## Important: the input must be well-formed XML

`VBZS_XPath` always requires well-formed XML as its input.

This requirement applies to every call, including calls that use the result of another function.

A fragment containing multiple top-level elements is not well-formed XML and cannot be passed directly to `VBZS_XPath`.

## Important: the result contains raw content

`VBZS_XPath` returns the raw content of the selected element. It does not automatically wrap that content in a new root element.

For example, given:

```xml
<cac:Party>
    <cbc:Name>Acme BV</cbc:Name>
    <cbc:CompanyID>12345</cbc:CompanyID>
</cac:Party>
```

selecting the content of `cac:Party` may return:

```xml
<cbc:Name>Acme BV</cbc:Name>
<cbc:CompanyID>12345</cbc:CompanyID>
```

This result contains two top-level elements. It is therefore an XML fragment, not a well-formed XML document.

The result cannot be used directly as the XML parameter of another `VBZS_XPath` call.

> **Important:** `VBZS_XPath` requires well-formed XML as input, but its output is deliberately the raw content of the selected element. When a selected element contains child elements, the returned content is not guaranteed to be well-formed XML.

## Working with complex elements

Do not assume that every result returned by `VBZS_XPath` can be used as XML input for another call.

When an element contains child elements:

1. Use `VBZS_XChildren` to discover the available elements.
2. Construct the complete XPath to the required value.
3. Apply that XPath to the original well-formed XML document.

For example:

```filemaker
VBZS_XPath (
    xml ;
    "/Invoice/cac:AccountingCustomerParty/cac:Party/cbc:CompanyID"
)
```

returns:

```text
12345
```

This is preferable to extracting `cac:Party` first and then trying to query the returned XML fragment.

## Recommended workflow

Use the two XML functions together:

1. Use `VBZS_XChildren` to discover the structure of an unfamiliar XML document.
2. Continue exploring the relevant child elements.
3. Construct the complete path to the required value.
4. Use `VBZS_XPath` with the original well-formed XML document.
5. Use the returned text or number directly in FileMaker.

In short:

```text
Unknown XML
    ↓
VBZS_XChildren
    ↓
Discover the structure
    ↓
Construct the required XPath
    ↓
VBZS_XPath
    ↓
Retrieve the required value
```

## Design philosophy

`VBZS_XPath` is primarily intended to retrieve data from XML. It is not intended to extract a section of an XML document and automatically turn that section into a new XML document.

The function therefore returns the content that was found without adding a root element or otherwise rewriting the XML.

This keeps the result predictable and avoids silently modifying the original XML content.

## Related functions

- `VBZS_XChildren`