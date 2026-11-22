# Prospector Scanner Monorepo

> [!NOTE]
> Work In Progress (WIP): This workspace is currently undergoing active setup, configuration, and testing.

![Prospector Scanner Showcase](./docs/assets/header.png)

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

## Documentation Reference

For detailed explanations of specific behaviors and configurations, refer to the local guides:

* [docs/MULTI_KEYBOARD_RULES.md](./docs/MULTI_KEYBOARD_RULES.md) – Covers multiple keyboard tracking, sticky selection, and timeout parameters.
* [docs/KEYBOARD_NAME_BEHAVIOR.md](./docs/KEYBOARD_NAME_BEHAVIOR.md) – Explains how configuring the status advertisement name affects BLE host device names.

## ThinkPad BLE bridge

Use the experimental Corne and Prospector firmware together. The Corne's left
half runs the keymap and keeps the right half connected. The Prospector forwards
the Corne's keyboard, media, and pointer reports to its USB host while continuing
to scan and display keyboard status.

The Prospector builds are `prospector_ble_bridge` (display only) and
`prospector_ble_bridge_touch` (touch enabled). Plug the Prospector into the
ThinkPad with a USB data cable, then select the ThinkPad binding on the Corne's
device layer. The binding uses ZMK BLE profile 2, the third slot. Start with that
slot free. An unbonded Prospector accepts the advertised `Bs Corne` bridge
profile during the first minute after boot. Keep other Corne bridge builds
powered off during this first pairing. The receiver then reconnects only to its
stored BLE bond. The ThinkPad sees a USB HID device.

Use the Corne's USB output binding to type through a cable directly from the
left half. For direct Bluetooth, choose another saved profile and use the BLE
output binding. If one side's bond was cleared, clear the Corne's bridge slot
with `BT_CLR` and flash `prospector_settings_reset` on the Prospector, then
reflash the bridge firmware. The Prospector reset clears its saved settings.

### Branch changes awaiting publication

The [module patch](patches/ble-hid-bridge/prospector-zmk-module.patch) and
[build configuration patch](patches/ble-hid-bridge/zmk-config-prospector.patch)
apply to this checkout's pinned submodule commits. The feature branches are
named `feat/ble-hid-bridge` in each repository. Apply and publish the module
patch first. Then apply and publish the build configuration patch and the
Corne branch. Both build configurations fetch the module's feature branch
through West. After the submodule commits are published, record their new
gitlinks here.

For hardware acceptance, try both halves, a multi-key chord, media keys,
pointer movement and scrolling, disconnecting with a key held, reconnecting
after reboot, and switching among the receiver, a direct Bluetooth host, and
wired USB. Check that the Prospector display continues to update while typing.
