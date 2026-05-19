# AGENTS.md

## Project Goal

Manitux is a downstream transformation of Manitux. The goal is to simplify the Manitux codebase as much as practical and evolve it into a new, leaner application named Manitux.

Prioritize changes that reduce surface area, remove unused features, clarify naming, and make the project easier to build, understand, and maintain. Keep behavior stable unless a task explicitly asks to remove or replace it.

## Working Guidelines

- Treat this repository as a Manitux-derived codebase in transition to Manitux.
- Prefer small, reversible changes over broad rewrites.
- Preserve license headers and third-party notices.
- Do not remove platform, addon, codec, or packaging code unless the removal target is explicit.
- When renaming Manitux/XBMC identifiers to Manitux, check build scripts, addon metadata, resources, translations, and runtime paths together.
- Keep generated files, dependency downloads, and build directories outside source whenever possible.

## Build Notes

The primary local development target is Ubuntu Linux. Follow the existing Manitux build documentation unless Manitux-specific instructions are added:

- `docs/README.Ubuntu.md`
- `docs/README.Linux.md`

Use an out-of-source build directory such as `../manitux-build` or `/tmp/manitux-build`.

Typical X11 configure command:

```sh
cmake . -B ../manitux-build -DCMAKE_INSTALL_PREFIX=../manitux-kodi-runtime -DCORE_PLATFORM_NAME=x11 -DAPP_RENDER_SYSTEM=gl
```

Typical build command:

```sh
cmake --build ../manitux-build -- -j$(getconf _NPROCESSORS_ONLN)
```

## Validation

For C++ or CMake changes, at minimum run CMake configure for the intended platform. Run a build when feasible. If the full build is too expensive, document the exact configure/build command attempted and the first failure.

## Style

- Match the existing C++ and CMake style in nearby files.
- Use existing helpers and project conventions before introducing new abstractions.
- Keep comments useful and sparse.
- Prefer ASCII in newly created files unless the surrounding file already uses another character set.
