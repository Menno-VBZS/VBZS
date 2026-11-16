# VBZS_ModKeys

## Description

`VBZS_ModKeys` reads the modifier keys reported by FileMaker and stores the state of each supported key in a separate FileMaker variable.

Note: This function is intended for use in FileMaker Pro and has no meaningful use in a server-side context.

Each variable contains:

```text
0
```

when the modifier key is not active, or:

```text
1
```

when the modifier key is active at the moment the function is evaluated.

The function is based on FileMaker's native:

```filemaker
Get ( ModifierKeys )
```

Instead of returning a single combined bit value, `VBZS_ModKeys` provides the state of each modifier key as an individual variable.

## Syntax

```text
VBZS_ModKeys ( prefix )
```

## Parameters

### `prefix`

An optional namespace added before the name of each variable created by the function.

If `prefix` is empty, the function creates:

```text
$apple
$alt
$ctrl
$caps
$shift
```

If a namespace is supplied, it is inserted before each variable name.

For example:

```filemaker
VBZS_ModKeys ( "mod_" )
```

creates:

```text
$mod_apple
$mod_alt
$mod_ctrl
$mod_caps
$mod_shift
```

## Result

`VBZS_ModKeys` does not return a calculation result.

Its only result is the set of FileMaker variables created when the function is evaluated.

## Created variables

Without a namespace, the following variables are created:

| Variable | Modifier key |
|---|---|
| `$apple` | Apple Command key |
| `$alt` | Alt or Option key |
| `$ctrl` | Control key |
| `$caps` | Caps Lock |
| `$shift` | Shift key |

Each variable contains either `0` or `1`.

Multiple variables can contain `1` at the same time when several modifier keys are active simultaneously.

## Example without a namespace

```filemaker
VBZS_ModKeys ( "" )
```

creates:

```text
$apple
$alt
$ctrl
$caps
$shift
```

For example, if Shift and Alt are active when the function is evaluated, the variables may contain:

```text
$apple = 0
$alt   = 1
$ctrl  = 0
$caps  = 0
$shift = 1
```

The function itself returns an empty result.

## Example with a namespace

```filemaker
VBZS_ModKeys ( "keyboard_" )
```

creates:

```text
$keyboard_apple
$keyboard_alt
$keyboard_ctrl
$keyboard_caps
$keyboard_shift
```

The namespace can help prevent the generated variables from conflicting with variables already used by a script.

## Global variables

A namespace beginning with `$` creates global FileMaker variables instead of local variables.

For example:

```filemaker
VBZS_ModKeys ( "$keyboard_" )
```

creates:

```text
$$keyboard_apple
$$keyboard_alt
$$keyboard_ctrl
$$keyboard_caps
$$keyboard_shift
```

Without the additional `$` in the namespace, normal local FileMaker variables are created.

## Platform behaviour

`VBZS_ModKeys` creates the same set of variables on macOS and Windows.

On macOS, `$apple` represents the Apple Command key as reported by FileMaker's `Get ( ModifierKeys )`.

On Windows, `$apple` is still created, but its value is always:

```text
0
```

FileMaker's `Get ( ModifierKeys )` does not provide an equivalent value for the Windows key. Consequently, `VBZS_ModKeys` does not create a separate variable for the Windows key.

Creating `$apple` on both platforms keeps the set of generated variables consistent between macOS and Windows.

## Evaluation time

The variables reflect the modifier-key state at the moment `VBZS_ModKeys` is evaluated.

If the state of the keyboard changes afterward, the generated variables are not automatically updated.

Evaluate the function again whenever the current modifier-key state is required.

## Notes

- The function is based on FileMaker's `Get ( ModifierKeys )`.
- The combined modifier-key value is split into separate variables.
- Every generated variable contains either `0` or `1`.
- Several modifier-key variables can contain `1` simultaneously.
- `prefix` is optional.
- An empty `prefix` creates `$apple`, `$alt`, `$ctrl`, `$caps` and `$shift`.
- A normal prefix acts as a namespace for local variables.
- A prefix beginning with `$` creates global variables.
- `$apple` is created on both macOS and Windows.
- On Windows, `$apple` is always `0`.
- No variable is created for the Windows key.
- The function itself returns an empty result.

## Design philosophy

FileMaker's native `Get ( ModifierKeys )` returns a combined numeric value representing the active modifier keys.

Using that result normally requires the developer to interpret the individual bit values before a script or calculation can determine which keys are active.

`VBZS_ModKeys` performs that interpretation once and exposes the individual states as simple FileMaker variables.

This allows a script to use variables such as:

```text
$shift
$alt
$ctrl
```

without repeatedly decoding the combined value returned by `Get ( ModifierKeys )`.

The optional namespace makes the generated variables suitable for use in larger scripts, while maintaining a consistent variable set across macOS and Windows.