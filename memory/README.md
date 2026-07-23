# Not the live memory store

Live Learning Memory is stored under the **Data Root** (`.learn/memory/` in the
project directory), not here.

The plugin install directory is read-only when installed from a marketplace, so
nothing is written here at runtime. This folder only holds bundled seed/reference
material. See `commands/learn.md` → "Data Root" for details.
