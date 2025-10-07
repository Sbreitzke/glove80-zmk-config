# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK firmware configuration repository for the MoErgo Glove80 wireless split contoured keyboard. It builds custom firmware using the Nix build system and ZMK (Zephyr's Mechanical Keyboard) firmware framework.

## Build System Architecture

The project uses **Nix** as its build system with two build approaches:

1. **GitHub Actions (Primary)**: Automated builds on push/PR using `.github/workflows/build.yml`
2. **Local Docker builds**: Using `build.sh` for local development

### Build Process Flow

The build combines firmware for both keyboard halves:
- `config/default.nix` orchestrates the build:
  - Builds left half (`glove80_lh`) using `glove80.keymap` and `glove80.conf`
  - Builds right half (`glove80_rh`) using the same config files
  - Combines both into a single `glove80.uf2` file via `firmware.combine_uf2`

## Building Firmware

### Local Docker Build
```bash
./build.sh [branch]
# Builds against specified ZMK branch (default: main)
# Output: glove80.uf2 in project root
```

The script:
- Builds Docker image with Nix and ZMK dependencies (moergo-sc/zmk)
- Runs container with current directory mounted as `/config`
- Executes `nix-build ./config` to generate firmware
- Outputs `glove80.uf2` file

### GitHub Actions Build
Push to GitHub triggers automatic build. Download artifacts from Actions tab.

## Configuration Files

- `config/glove80.keymap`: Keyboard layout defined in ZMK devicetree syntax
  - Layers: DEFAULT (0), LOWER (1), MAGIC (2), FACTORY_TEST (3)
  - Defines behaviors (tap-dance, hold-tap), macros, and key bindings
  - Uses devicetree syntax with `#include` directives for ZMK behaviors and key codes
- `config/glove80.conf`: ZMK Kconfig settings (currently empty/default)
- `config/default.nix`: Nix build configuration that combines left/right firmware

## Keymap Editing

The keymap uses ZMK devicetree format with:
- Layer definitions in the `keymap` node
- Custom behaviors (e.g., `layer_td` tap-dance, `magic` hold-tap)
- Macros for Bluetooth profiles and RGB control
- Standard ZMK includes: `<behaviors.dtsi>`, `<dt-bindings/zmk/keys.h>`, etc.

When modifying the keymap, preserve the devicetree structure and binding cell counts.

## ZMK Version

The build pulls from the official Glove80 ZMK distribution at `moergo-sc/zmk`. The Dockerfile pre-caches dependencies for the `main` branch and the three most recent tags.

## Resources

- [Official MoErgo Glove80 Support](https://moergo.com/glove80-support)
- [ZMK Documentation](https://zmk.dev/docs)
- [Glove80 ZMK Distribution](https://github.com/moergo-sc/zmk)
