---
name: bezel-it-command-line
description: Operate the Bezel It CLI (`bezelit`) to list devices, inspect schemas, dry-run layout geometry, and render media with device frames.
---

# Bezel Command Line

## Overview

Use this skill to work through the public `bezelit` CLI the way an end user or automation client would. Prefer the public command surface first, especially for `device list`, `device render ...`, and `--dry-run` geometry checks.

## Workflow

1. Identify the public command surface the user needs.
2. Load [references/schema-architecture.md](references/schema-architecture.md) and retrieve the leaf schema first (for example `bezelit schema device render physical`).
3. If the user needs rendering or geometry behavior, load [references/rendering-device-frames.md](references/rendering-device-frames.md).
4. Use schema-derived options/constraints to build the command, then use `--dry-run` first when fit, padding, or placement matters.
5. Render only after the geometry is acceptable.
6. If the CLI reports a connection failure, use the public troubleshooting guidance in the reference.

## Command Strategy

- Prefer the public command-line tool:
  - `bezelit`
- Use `bezelit device list` to discover device IDs instead of guessing them.
- Use the `schema` command tree (for example `bezelit schema device render physical`) when the user needs machine-readable command metadata.
- Treat dry-run JSON as the source of truth for automation-facing frame geometry.

## Input and Output Behavior

### Input
- `--input <path>` (required): must be an existing file. Supported formats are PNG/JPEG, GIF, MP4, and MOV.

### Output resolution (mutually exclusive modes)

1. **Explicit path** — `--output <path>` provided
   - File is written to the exact path.
   - `--export` is optional; if omitted, the export type is inferred from the output file extension.

2. **Generated filename in current directory** — `--export <type>` provided, `--output` omitted
   - File is written to the current working directory.
   - Filename is auto-generated as `<input-stem>-bezel.<export-ext>`.
   - Example: input `clip.mov` with `--export hevc` → `./clip-bezel.mov`.

3. **Match input type in current directory** — neither `--output` nor `--export` provided
   - File is written to the current working directory.
   - Filename is `<input-stem>-bezel.<input-ext>`.
   - Export type matches the input file type.

### Allowed export types per input
- Image (png, jpg, jpeg) → only `png`
- GIF → only `gif`
- Movie (mp4, mov) → `hevc`, `mp4`, `gif`

## References

- Schema architecture, progressive schema discovery, and agent retrieval flow:
  - [references/schema-architecture.md](references/schema-architecture.md)
- Render commands, dry-run layout output, frame semantics, padding math, and worked fitting examples:
  - [references/rendering-device-frames.md](references/rendering-device-frames.md)
