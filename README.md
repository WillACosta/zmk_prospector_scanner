# Prospector Scanner Monorepo

> [!NOTE]
> Work In Progress (WIP): This workspace is currently undergoing active setup, configuration, and testing.

Welcome to the unified workspace for the ZMK Prospector Scanner. This monorepo uses Git Submodules to organize the configuration and driver repositories in one place, while maintaining independent upstream tracks.

## Custom Modifications

This fork includes several custom enhancements and layout optimizations:

* **WPM Widget Control:** Added a configuration option to show or hide the WPM (Words Per Minute) widget on the display.
* **Layer Name Support:** Added an option to display descriptive layer names instead of simple numerical indexes.
* **UI Responsiveness:** Implemented minor refinements and responsiveness fixes in the screen drawing routines.

### UI Configuration Example

To enable your preferred UI style (hiding the WPM widget, displaying descriptive layer names, and naming your BLE profiles), add the following settings to your `prospector_scanner.conf`:

```conf
# Disable the WPM widget on screen
CONFIG_PROSPECTOR_WPM_SUPPORT=n

# Enable descriptive layer names (centers active name, hides inactive numbers)
CONFIG_PROSPECTOR_LAYER_NAMES_SUPPORT=y

# Custom BLE connection profile names (default: slot numbers "0" to "4")
CONFIG_PROSPECTOR_PROFILE_0_NAME="pers"
CONFIG_PROSPECTOR_PROFILE_1_NAME="work"
CONFIG_PROSPECTOR_PROFILE_2_NAME="ipad"
CONFIG_PROSPECTOR_PROFILE_3_NAME="mac"
CONFIG_PROSPECTOR_PROFILE_4_NAME="phone"
```

## Submodule Overview

### ZMK Config / Firmware ([zmk-config-prospector](https://github.com/WillACosta/zmk-config-prospector))
The user configuration repository for the scanner hardware (Seeeduino XIAO BLE + Round LCD). It contains:
* Shield configuration (`prospector_scanner.conf`)
* Custom layout selections and runtime settings.

### ZMK Module ([prospector-zmk-module](https://github.com/WillACosta/prospector-zmk-module))
The core ZMK driver and shield implementation. It contains:
* BLE advertising parser (`status_scanner.c`)
* Screen drawing and layout style definitions (`custom_status_screen.c`, `operator_layout.c`, `radii_layout.c`).

## Multi-Keyboard Configuration

For details on multiple keyboard tracking, sticky selection rules, and timeout parameters for quick auto-switching, refer the documentation guide:
[docs/MULTI_KEYBOARD_RULES.md](./docs/MULTI_KEYBOARD_RULES.md).
