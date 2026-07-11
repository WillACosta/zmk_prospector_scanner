# ZMK Prospector Scanner: Multi-Keyboard Rules & Behaviors

This document tracks the rules and expected behaviors for multi-keyboard support on the Prospector Scanner firmware and module.

## 🔍 Core Behaviors & Limitations

### 1. In-Memory Tracking vs. Active Display
* **Background Tracking:** The scanner module can track status advertisements for up to `CONFIG_PROSPECTOR_MAX_KEYBOARDS` (default: 3) simultaneously. All detected keyboards are stored in the `keyboards` array in [scanner_stub.c](file:///Users/will/workspace/keyboards/zmk_prospector_scanner/prospector-zmk-module/boards/shields/prospector_scanner/src/scanner_stub.c#L44).
* **Single Keyboard Focus:** The main status screens (YADS, Field, Operator, and Radii layouts) only display detailed telemetry (keymap, layer, modifiers, WPM, and batteries) for **one selected keyboard at a time** (the `selected_keyboard` index).

### 2. Sticky Selection Strategy
* To prevent the UI from flickering or constantly jumping between active keyboards (which broadcast advertisements continuously, especially during typing), the active keyboard selection is **sticky**.
* The scanner remains locked to the selected keyboard until:
  1. The user manually selects another keyboard (only possible in Touch Mode).
  2. The selected keyboard stops advertising and times out.

### 3. Screen Navigation (Touch Mode vs. Non-Touch Mode)
* **Touch Mode (`CONFIG_PROSPECTOR_TOUCH_ENABLED=y`):**
  * Swiping **UP** on the main screen opens the Keyboard List screen (`SCREEN_KEYBOARD_SELECT`), which displays all active keyboards as cards.
  * Tapping a card manually changes the selected keyboard.
  * Swiping **DOWN** returns to the main screen.
* **Non-Touch Mode (`CONFIG_PROSPECTOR_TOUCH_ENABLED=n`):**
  * Touch input and gestures are disabled. The Keyboard List screen is inaccessible.
  * Switching between keyboards is handled **entirely by timeout detection**.

## ⚙️ How to Configure Keyboard Switching

### Adjusting the Timeout (Recommended for Non-Touch setups)
By default, the selected keyboard will only time out after **8 minutes** of inactivity (`480000` ms). To make the scanner switch quickly when you transition between keyboards, you can reduce this value in [prospector_scanner.conf](file:///Users/will/workspace/keyboards/zmk_prospector_scanner/zmk-config-prospector/config/prospector_scanner.conf#L100):

```conf
# Change timeout from 480000 ms (8 mins) to 5000 ms (5 secs) or 10000 ms (10 secs)
CONFIG_PROSPECTOR_SCANNER_TIMEOUT_MS=10000
```

Once updated and flashed:
1. When you stop typing on Keyboard A, it will time out after 10 seconds of no advertisements.
2. The scanner will automatically switch its active display to Keyboard B if Keyboard B is active and advertising.

### Channel Filtering (Optional)
If you are in an environment with other ZMK keyboards broadcasting advertisements, you can filter advertisements by channel to prevent them from filling slots on your scanner:
* On the keyboard side: Set `CONFIG_PROSPECTOR_CHANNEL=N` (1–9).
* On the scanner side: Set `CONFIG_PROSPECTOR_SCANNER_CHANNEL=N` to filter, or set it to `0` (or `10` via the Touch UI) to show all keyboards.
