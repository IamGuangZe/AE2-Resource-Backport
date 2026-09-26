# GTLcore Compat Backport

Compatibility layer for GregTech Leisure (`gtlcore`) on 1.20.1.

## Why

`gtlcore` adds eight buttons to AE2's pattern encoding terminal (six
"pattern recipe x2/x3/x5 /2/3/5" plus "quick upload" and "undo quick
upload"). Its `ProcessingEncodingPanelMixin` adds six of them unguarded:

```java
widgets.add("modify1", ...);   // ... through modify6
```

`WidgetContainer.add()` resolves the name through
`ScreenStyle.getWidget()`, which throws
`IllegalStateException: Screen is missing required widget: modify1` when
the style does not define it - so the terminal fails to open.

The style chain for the wired and wireless pattern encoding terminal is

```
screens/wtlib/modify_wireless_pattern_encoding_terminal.json   (gtlcore)
  -> screens/terminals/modify_pattern_encoding_terminal.json   (gtlcore)
       -> screens/terminals/encoding/modify_processing.json    <- this file
```

`Expanded AE Backport` ships `modify_processing.json` as a copy of the
1.21.1 file, which only defines `mult2/3/8` and `div2/3/8`. A resource
pack overrides the mod jar, so `gtlcore`'s own version of that file is
shadowed and `modify1`-`modify6` are lost.

## What this pack does

* `assets/ae2/screens/terminals/encoding/modify_processing.json` -
  superset of the 1.21.1 file plus `gtlcore`'s six widget positions.
  The file is only ever included by `gtlcore`, so the extra entries are
  inert when `gtlcore` is not installed.
* `assets/gtlcore/textures/guis/states.png` - a verbatim copy of
  `gtlcore`'s 48x48 icon sheet, so the button icons can be restyled from
  a resource pack. Tiles are 16x16:

  | button | tile |
  |---|---|
  | modify1 x2 | (0, 0) |
  | modify2 x3 | (16, 0) |
  | modify3 x5 | (32, 0) |
  | modify4 /2 | (0, 16) |
  | modify5 /3 | (16, 16) |
  | modify6 /5 | (32, 16) |
  | undo quick upload | (0, 32) |
  | (unused) | (32, 32) |

  The "quick upload" button uses AE2's `Icon.ARROW_UP`, i.e.
  `assets/ae2/textures/guis/states.png` rect (0, 48, 16, 16).

## Merge order

`merge-backports.sh` sorts the `*Backport*` folders, so this pack is
merged after `Expanded AE Backport` and its `modify_processing.json`
wins.
