# VBZS_FrontTabPanels

## Description

`VBZS_FrontTabPanels` returns the names of the currently visible tab panels and slide panels on the current FileMaker layout.

The function can be used with or without a parameter.

Without a parameter, it returns all named tab and slide panels that are currently visible.

When a parameter is supplied, it acts as a filter and only the visible panels whose names occur in the supplied list are returned.

This makes `VBZS_FrontTabPanels` useful both for discovering the current visible panel structure and for testing whether specific panels are currently visible.

Note: This function is intended for use in FileMaker Pro and has no meaningful use in a server-side context.

## Syntax

```text
VBZS_FrontTabPanels ( param )
```

## Requirements

Tab panels and slide panels must have an object name assigned in FileMaker's Inspector.

The function identifies panels by their object name. A panel without an object name cannot be returned or matched against `param`.

For this reason, panels that need to be detected by `VBZS_FrontTabPanels` should always be given a unique and meaningful object name.

## Parameters

### `param`

An optional return-delimited list of panel object names.

If `param` is empty, all currently visible named tab panels and slide panels are returned.

If `param` contains one or more names, only visible panels whose names occur in that list are returned.

The order of the values in `param` does not determine the order of the result.

## Result

Returns a return-delimited list containing the object names of the matching visible tab and slide panels.

Nested visible panels are returned from the outermost panel to the innermost panel.

## Without a parameter

To retrieve all currently visible named tab and slide panels:

```filemaker
VBZS_FrontTabPanels ( "" )
```

Suppose the currently visible panel structure is:

```text
Customer
└── Details
    └── Address
```

The result is:

```text
Customer
Details
Address
```

All three panels are currently visible and have an object name, so all three are returned.

## Nested panels

`VBZS_FrontTabPanels` also detects visible panels that are located inside other visible tab or slide panels.

For example:

```text
Customer
└── Details
    └── Address
```

If `Customer`, `Details` and `Address` represent the currently visible route through nested panel objects, all three can be returned.

The order is from outside to inside:

```text
Customer
Details
Address
```

This makes it possible to determine more than just the front panel of a single tab control. The function can describe the complete visible panel path through nested panel objects on the layout.

## Filtering visible panels

When `param` is supplied, it is treated as a return-delimited list of panel names to look for.

For example:

```filemaker
VBZS_FrontTabPanels (
    "Details¶Address"
)
```

If the currently visible panels are:

```text
Customer
Details
Address
```

the result is:

```text
Details
Address
```

`Customer` is visible, but it is not returned because it was not included in `param`.

## Parameter order

The order of names in `param` does not control the order of the result.

For example:

```filemaker
VBZS_FrontTabPanels (
    "Address¶Customer"
)
```

may return:

```text
Customer
Address
```

if `Customer` is an outer visible panel and `Address` is a visible panel nested inside it.

The parameter determines which visible panels may be returned. It does not sort them.

## Similarity to FilterValues

When `param` is supplied, its behaviour is conceptually similar to FileMaker's native `FilterValues` function.

First, `VBZS_FrontTabPanels` determines which named tab and slide panels are currently visible.

The supplied list then acts as a filter on that result.

Conceptually:

```text
Visible panels
      ↓
Filter using param
      ↓
Matching visible panels
```

This means `param` answers the question:

```text
Which of these panels are currently visible?
```

rather than:

```text
Return these panels in this order.
```

## Example: testing selected panels

Suppose the visible structure is:

```text
Main
└── Customer
    └── Contact
```

Calling:

```filemaker
VBZS_FrontTabPanels (
    "Contact¶Orders¶Main"
)
```

returns:

```text
Main
Contact
```

if both `Main` and `Contact` are currently visible.

`Orders` is not returned because it is not currently visible.

The result follows the visible layout structure, not the order of the parameter list.

## Using the function as a visibility test

Because `param` filters the visible panel list, the function can be used to test only the panels that are relevant to a particular calculation or script.

For example:

```filemaker
VBZS_FrontTabPanels (
    "Customer¶Supplier"
)
```

can be used when a calculation only needs to know whether the visible interface currently contains the `Customer` or `Supplier` panel.

There is no need for the calculation to inspect or process unrelated visible panels.

## Notes

- Both tab panels and slide panels are supported.
- Panels must have an object name assigned in FileMaker's Inspector.
- Without `param`, all currently visible named panels are returned.
- With `param`, only matching visible panels are returned.
- `param` is a return-delimited list of object names.
- The order of `param` does not determine the result order.
- Nested visible panels are supported.
- Nested panels are returned from outermost to innermost.
- A visible outer panel is not automatically included when `param` is used. Its name must also occur in `param`.
- The result is a return-delimited list.

## Design philosophy

FileMaker layouts can contain tab controls and slide controls nested inside other panel objects. Determining which combination of panels is currently visible can therefore become considerably more complicated than simply identifying one active tab.

`VBZS_FrontTabPanels` reduces that state to a simple FileMaker value list.

Without a parameter, the function answers:

```text
Which named tab and slide panels are visible right now?
```

With a parameter, it answers:

```text
Which of the panels I am interested in are visible right now?
```

The optional filtering behaviour follows the familiar principle of FileMaker's `FilterValues` function: the parameter restricts the values that may be returned without defining their order.

This allows scripts and calculations to respond to the current visible layout state without having to reproduce the complete panel structure in FileMaker logic.