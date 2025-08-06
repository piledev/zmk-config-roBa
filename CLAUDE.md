# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration for a custom split keyboard called "roBa" with integrated PMW3610 trackball support. The keyboard uses Seeeduino XIAO BLE microcontrollers and features a 6-layer keymap with advanced functionality including trackball scrolling, combos, and Bluetooth connectivity.

## Build System

### Primary Build Commands
```bash
# Build firmware via GitHub Actions (recommended)
git push  # Triggers automatic build on GitHub

# Local build (requires ZMK development environment)
west build -d build/roBa_R -b seeeduino_xiao_ble -- -DSHIELD=roBa_R -DZMK_CONFIG="$(pwd)/config"
west build -d build/roBa_L -b seeeduino_xiao_ble -- -DSHIELD=roBa_L -DZMK_CONFIG="$(pwd)/config"
```

### Build Targets
- `roBa_R`: Right half with USB-UART RPC support
- `roBa_L`: Left half 
- `settings_reset`: Factory reset firmware

### Keymap Visualization
```bash
# Generate keymap visualization (requires keymap-drawer)
keymap parse -c keymap-drawer/roBa.yaml -z config/roBa.keymap > keymap-drawer/roBa.svg
```

## Architecture

### Hardware Configuration
- **Microcontroller**: Seeeduino XIAO BLE (both halves)
- **Trackball**: PMW3610 sensor integrated in right half
- **Matrix**: 4x11 key matrix per half
- **Connectivity**: Bluetooth LE with USB fallback

### Key Files Structure

**Firmware Configuration** (`/config/`):
- `roBa.keymap`: Main keymap with 6 layers and custom behaviors (170 lines)
- `west.yml`: Dependency management for ZMK and PMW3610 driver

**Hardware Definitions** (`/boards/shields/roBa/`):
- `roBa.dtsi`: Common hardware definitions (GPIO matrix, encoder, trackball)
- `roBa_L.overlay` / `roBa_R.overlay`: Split-specific device tree overlays
- `roBa_L.conf` / `roBa_R.conf`: Build configuration for each half

**Build Configuration**:
- `build.yaml`: GitHub Actions matrix for 3 build targets
- `.github/workflows/build.yml`: Automated firmware builds on push

### Layer Architecture
- **Layer 0**: Default layout (QWERTY-based with modifications)
- **Layer 1**: Numbers and symbols (NUM)
- **Layer 2**: Extended symbols (SIGN) 
- **Layer 3**: Function keys (FUNCTION)
- **Layer 4**: Mouse operations (MOUSE)
- **Layer 5**: Trackball scrolling (SCROLL)
- **Layer 6**: Bluetooth management (conditional layer)

## Development Workflow

### Making Keymap Changes
1. Edit `config/roBa.keymap` for layout modifications
2. Test changes by pushing to GitHub (builds automatically)
3. Download firmware from Actions artifacts
4. Flash via bootloader mode or ZMK Studio

### Hardware Modifications
1. Modify device tree files in `boards/shields/roBa/`
2. Update `roBa.dtsi` for common changes
3. Update specific overlays for split-specific changes
4. Rebuild and test on hardware

### Custom Dependencies
- PMW3610 trackball driver is pulled from `kumamuk-git/zmk-pmw3610-driver`
- ZMK main branch is used as base firmware
- Dependencies defined in `config/west.yml`

## Key Features

### Trackball Integration
- PMW3610 sensor configuration in device tree
- Automatic layer switching to scroll mode (layer 5)
- Configurable sensitivity and behavior

### Advanced Keymap Features
- **Combo keys**: Multi-key press combinations for modifier access
- **Conditional layers**: Layer 6 (Bluetooth) activated by multiple layer keys
- **Balanced mod-tap**: Quick tap for characters, hold for modifiers
- **Custom macros**: Layer switching and complex key sequences

### Split Keyboard Support
- Independent left/right builds
- Bluetooth communication between halves
- USB-UART RPC on right half for debugging

## Common Development Tasks

### Testing Changes
- Firmware builds automatically on every push via GitHub Actions
- Check Actions tab for build status and download artifacts
- Use ZMK Studio for real-time keymap editing (right half with RPC support)

### Debugging
- Right half includes USB-UART RPC snippet for debugging
- Use `settings_reset` target to factory reset keyboard settings
- Check device tree compilation with local west build

### Layout Visualization
- Visual keymap in `keymap-drawer/roBa.svg` 
- Configuration in `keymap-drawer/roBa.yaml` (310 lines)
- Regenerate after keymap changes for documentation

## Hardware Debugging

### Device Tree Issues
- Verify GPIO assignments in `.dtsi` files
- Check pin conflicts between trackball, encoder, and key matrix
- Validate I2C/SPI configurations for sensors

### Trackball Problems
- Driver configuration in `west.yml` dependency
- Sensor initialization in device tree `&trackball` block
- Layer switching behavior in keymap