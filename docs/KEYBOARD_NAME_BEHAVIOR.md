# Keyboard Naming and BLE Host Behavior

This document outlines how configuring the Prospector Scanner status advertisement name impacts the Bluetooth device name displayed to host computers and phones.

## BLE Device Name Override

By default, when you configure a name for the scanner using the following setting in your keyboard's `.conf` file:
```conf
CONFIG_ZMK_STATUS_ADV_KEYBOARD_NAME="Corne Kbd"
```

The Prospector ZMK module's status advertisement code ([status_advertisement.c](file:///Users/will/workspace/keyboards/zmk_prospector_scanner/prospector-zmk-module/src/status_advertisement.c#L1149)) automatically calls Zephyr's dynamic name setter:
```c
bt_set_name(CONFIG_ZMK_STATUS_ADV_KEYBOARD_NAME);
```

This behavior synchronizes the name that is broadcast to the Prospector Scanner screen with the name presented to host devices during Bluetooth pairing and connections. Consequently, your host devices (PC, Mac, or phone) will identify the keyboard by this overridden name (e.g. "Corne Kbd") instead of the keyboard's default name (such as "Eyelash Corne").

## Configuration Options

To change this behavior, choose one of the following approaches:

### Keep the Default Keyboard Name Everywhere

If you want both the Prospector Scanner screen and your host devices to display the default keyboard name (e.g., "Eyelash Corne"), leave the status advertisement name config empty or comment it out:

```conf
CONFIG_ZMK_STATUS_ADV_KEYBOARD_NAME=""
```

In this case, the module defaults to your keyboard's built-in name (configured via `CONFIG_BT_DEVICE_NAME` or the shield definition) for both status advertisements and host connections.

### Configure Different Names for the Scanner and Host

Currently, the module code enforces name synchronization. If you want to show a short name on the scanner display but keep your keyboard's full name on host devices, the driver code in `status_advertisement.c` must be modified to prevent calling `bt_set_name()`.

## Behavior when Scanner is Powered Off

Because the status advertisement protocol is entirely unidirectional, the broadcasting peripheral has no awareness of the scanner's power state.

* **Static Device Name:** The peripheral continues to advertise and pair with host devices under the configured status advertisement name even if the scanner is powered off or out of range. It does not revert dynamically to its default hardware name.
* **Prevention of Sync Issues:** This static naming behavior is intentional. If the peripheral dynamically toggled its displayed Bluetooth name based on the scanner's presence, host operating systems (which cache Bluetooth name-to-MAC address mappings) would experience pairing confusion, display incorrect names, or fail to reconnect.
* **Instant Re-synchronization:** When the scanner is powered back on, it automatically scans and captures active advertisement packets (typically within one second) to update its UI state immediately without requiring any handshake or state negotiation.

