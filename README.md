# cliamp-plugin-vu-meter

https://github.com/user-attachments/assets/3a7b2d94-856d-48e6-b452-dd98e391ee41

A row of ten analog-needle VU meters for [cliamp](https://cliamp.stream), one per spectrum band. Needles are drawn at sub-pixel resolution with Unicode braille (U+2800 to U+28FF) so diagonals stay smooth at any swing angle. Sibling of [led-burst](https://github.com/AlexZeitler/cliamp-plugin-led-burst) and [block-burst](https://github.com/AlexZeitler/cliamp-plugin-block-burst).

## Install

```sh
cliamp plugins install AlexZeitler/cliamp-plugin-vu-meter
```

Then start cliamp and press `v` to cycle visualizers until `vu-meter` appears.

To pin a specific version:

```sh
cliamp plugins install AlexZeitler/cliamp-plugin-vu-meter@v0.1.0
```

Remove with:

```sh
cliamp plugins remove vu-meter
```

## Tuning

The plugin is a single Lua file. Edit `~/.config/cliamp/plugins/vu-meter.lua` after installing if you want to customize:

| What | Where | Effect |
|------|-------|--------|
| Max swing angle | `SWING_DEG` | Maximum tilt from vertical at full level. Default 60. Wider meters reach this; narrower meters cap earlier so the tip stays inside the canvas. |
| Needle area height | `NEEDLE_CELL_H` | Number of braille rows for the needle. Default 3. Increase for a taller meter, decrease for a flatter one. |
| Meter widths | `METER_WIDTHS` | Candidate cells per meter, widest first. The picker chooses the widest that fits the panel. |
| Min gap | `MIN_GAP` | Minimum spaces between adjacent meters. Lower to fit wider meters in a narrow panel, raise for clearer separation. |
| Needle length fraction | `pivotSubY * 0.92` inside `renderNeedleRows` | Fraction of canvas height used as the needle length. |
| Pegged color | `level > 0.92` | Input level at which the needle flips from white to red. |

## How it works

cliamp gives Lua visualizer plugins a `bands` table of 10 mono spectrum values (0.0 to 1.0) per frame. `vu-meter` draws one needle per band. Each needle is a single straight line of constant length, pivoting from the bottom center of a small braille canvas. The Bresenham line is rasterized into 2x4 sub-pixel braille cells so the diagonal stays smooth at any swing angle.

The needle length is fixed at roughly the full canvas height, and the maximum swing angle is reduced for narrow meters so the tip just reaches the side wall instead of overshooting and getting clipped. This keeps the visible needle the same length at every angle.

There is no smoothing on the level: the needle follows the raw band value every frame, which keeps it responsive to fast transients.

The visualization is symmetric (each meter sits next to the next) and not stereo: Lua plugins do not have access to separate L/R channels.

## Requirements

- cliamp with Lua plugin support
- A terminal with 256-color ANSI support and Unicode braille glyphs (effectively every modern terminal)

## Related plugins

- [AlexZeitler/cliamp-plugin-led-burst](https://github.com/AlexZeitler/cliamp-plugin-led-burst) - Stereo LED matrix that bursts outward from a center divider, with green / yellow / red tiers and peak hold.
- [AlexZeitler/cliamp-plugin-block-burst](https://github.com/AlexZeitler/cliamp-plugin-block-burst) - Nested LED pyramid where each tier responds to a different frequency range.

## License

MIT - see [LICENSE](LICENSE).
