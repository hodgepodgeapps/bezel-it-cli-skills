# Schema Architecture and Retrieval

Use this reference only when the user asks about schema shape, allowed options/values, constraints, or command-discovery behavior.

## Public Retrieval Surface

The public machine-readable contract is exposed via:

```bash
bezelit schema <subcommand>
```

Progressive discovery flow:

1. Root:
```bash
bezelit schema
```
2. Group:
```bash
bezelit schema device
bezelit schema gradients
bezelit schema pro-version-status
```
3. Leaf:
```bash
bezelit schema device render physical
bezelit schema device render simple
bezelit schema device render no-bezel
```

## Schema Shape

Each schema response provides command metadata needed by an agent:

- `command`, `usage`, `summary`
- `subcommands`
- `options` (name, type, defaults, allowed values)
- `constraints` (`requireOptions`, `allowAtMostOneOptionFrom`)
- `examples`
- `output` contract

For render workflows, leaf schema calls are usually enough to build valid commands without guessing.

## Command Build Checklist

1. Fetch the leaf schema for the exact command you plan to run.
2. Select option values only from each option's schema `allowed` list.
3. Apply schema `constraints` before execution.
4. Run `--dry-run` first for render commands.
5. Run non-dry render only after dry-run output matches intent.

## Agent Contract Guidance

- Prefer schema output over hardcoded option assumptions.
- Read `allowed` values from schema when selecting enum-like options (`aspect`, `padding`, `export`).
- Respect conditional constraints before constructing commands.
- Use schema examples as templates, then fill user-specific values.

## Schema-Driven Agent Loop

```text
schema = run("bezelit schema device render <style>")
args = build_args_from_schema(schema):
  - include required options
  - pick enum values from option.allowed when present
  - satisfy schema.constraints
dry = run("bezelit device render <style> " + args + " --dry-run")
if dry output matches intent:
  run("bezelit device render <style> " + args + " --output <path>")
```
