# Rendering Device Frames

Use this reference when building or explaining public `bezelit device render ...` commands, especially when an automation client needs layout geometry before writing an output file.

## Public Render Surface

The public render styles are:

- `bezelit device render physical`
- `bezelit device render simple`
- `bezelit device render no-bezel`

Discover device IDs with:

```bash
bezelit device list
```

## Dry-Run Contract

Use `--dry-run` to request geometry without writing an output file:

```bash
bezelit \
  device render physical \
  --input input.png \
  --device iPhone16 \
  --dry-run
```

Current dry-run JSON shape:

```json
{
  "backgroundSize": {
    "width": 1358,
    "height": 2735
  },
  "deviceFrame": {
    "x": 22.48,
    "y": 30.48,
    "width": 1313.03,
    "height": 2673.04
  }
}
```

Semantics:

- `backgroundSize` is the final output raster size in pixels.
- `deviceFrame` is the frame rect inside the background (top-left origin).

## Dry-Run vs Real Render

- `--dry-run`: prints layout JSON, no file is written.
- Normal render: writes output file and prints only output path.

Dry run is the preferred preflight for automation.
Before running dry-run or render, retrieve the leaf schema and derive allowed values/constraints from it:

```bash
bezelit schema device render physical
```

## Negative Number Rule

For any negative numeric option value, prefer `=` form to avoid parser ambiguity:

```bash
--padding-bottom=-100
```

## Optional Full-Containment Check

Only apply this check when the goal is to keep the entire device frame visible.

Negative padding and some aspect choices can intentionally place part of the device frame outside the background (for example, to crop the device and show only a portion).

If full containment is required, validate:

```text
deviceFrame.x >= 0
deviceFrame.y >= 0
deviceFrame.x + deviceFrame.width <= backgroundSize.width
deviceFrame.y + deviceFrame.height <= backgroundSize.height
```

## Example Fit Iteration

1. Run dry-run with the real target command.
2. Decide whether your goal is full containment or intentional cropping.
3. If full containment is required, check containment failures and adjust padding/aspect.
4. Re-run dry-run until the layout matches intent.
5. Execute non-dry render with the same options.

## Output File Safety Warning

Non-dry render can clear/replace an existing output file when overwrite behavior is enabled.

Use dry-run first and only run non-dry render once command options and geometry are confirmed.
Use a temporary output path during iteration, then switch to final output path after validation.

## Connection Troubleshooting

If CLI reports an agent connection error:

1. Retry once.
2. Open Bezel It.
3. Retry the same command.
