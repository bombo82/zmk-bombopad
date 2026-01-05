# BomboPad ZMK Firmware

BomboPad is an open-source, versatile 12-key macropad featuring dual rotary encoders and display support. Designed with
a focus on flexibility, it supports both wired and wireless configurations.

This directory contains the **ZMK firmware** implementation for the BomboPad, designed for wireless use with controllers
like the Nice!Nano.

## Key Features (v0.3)

The current firmware version supports the following hardware features:

- **12 keys**: Matrix layout 3x4.
- **Dual Rotary Encoders**: Support EC11 or EVQWGD001 encoders.
- **Display Support**: Nice!View.
- **Microcontroller**: Nice!Nano v2 or other compatible wireless controllers, e.g. SuperMini NRF52840.
  Note that the shield metadata specifies `requires: [pro_micro]`, which is the standard ZMK mapping for controllers
  with the Pro Micro footprint, including the Nice!Nano.
- **Wireless**: Bluetooth LE support via ZMK.

## Hardware Availability

For hardware design files, electrical schematics, and PCB layouts, please refer to the
**[main BomboPad repository](https://github.com/bombo82/bombopad)**.

## Build Variants

The firmware is built in two variants to accommodate different hardware configurations. The following table summarises
the differences:

| Feature           | `bombopad_niceview` (Standard) | `bombopad_nodisplay` (Optimised) |
|:------------------|:-------------------------------|:---------------------------------|
| **Display**       | Enabled (Custom Widget)        | Disabled                         |
| **BLE Profiles**  | 5                              | 2                                |
| **BT Navigation** | Cycle (`BT_PRV`/`BT_NXT`)      | Direct (`BT_SEL 0/1`)            |
| **Artifact Name** | `bombopad_niceview`            | `bombopad_nodisplay`             |

Both variants include `BT_CLR` for clearing the current Bluetooth bond.

## Keymap Information

The ZMK keymap is located at `boards/shields/bombopad/bombopad.keymap`.

### Shield-provided behaviours

The BomboPad shield exposes a small set of reusable behaviours that are available to:

- the default keymap included in this module; and
- any external ZMK configuration (e.g., a personal `zmk-config`) that builds with this shield.

They are defined at shield level in the DeviceTree overlay, so their node labels are always visible during the build,
regardless of which keymap file is used.

Supported behaviours:

1) `cycle_layer`

- Type: hold-tap combining `&mo` (momentary) and `&to` (toggle)
- Semantics:
    - Hold: momentarily switches to the specified layer (e.g., `MGMT`).
    - Tap: toggles to the specified layer (e.g., from `NUM` to `MGMT`).
- Default parameters: `tapping-term-ms = 200`.

Usage example:

```
&cycle_layer MGMT MGMT
```

2) `ht` (generic hold-tap)

- Type: hold-tap for dual-role keys
- Semantics: tap and hold actions are both `&kp` and are defined via binding cells per usage
- Default parameters: `tapping-term-ms = 200`, `quick-tap-ms = 200`, `flavor = tap-preferred`.

Usage example (modifier on hold, key on tap):

```
&ht LG(K_C) K_C
```

Notes:

- The parameters above are set globally by the shield. If you need different timings or flavour for a specific project,
  prefer introducing a separate behaviour with a different node label in your own configuration to avoid overriding
  shared defaults.
- Since these behaviours are provided by the shield, external `zmk-config` keymaps can reference `&cycle_layer` and
  `&ht` without redefining them.

### Layers

The default keymap defines the following layers:

- `NUM`: Numeric keypad layer (Default).
- `MGMT`: Management layer for Bluetooth profile selection, output toggling, and reset/bootloader functions.

For further details on layer configuration and encoder behaviours, refer to the [Encoders](#encoders)
and [Keymap & Bluetooth](#keymap--bluetooth) sections below.

## Display & Nice!View Widget

The BomboPad includes a customised status widget for the Nice!View display, which provides at-a-glance information about
the device state.

### Customised Widget Layout

The customised widget is enabled by default (`CONFIG_NICE_VIEW_WIDGET_STATUS=n`) and replaces the standard ZMK status
screen. It is organised into three main sections:

| Section                | Position    | Content                                                   |
|:-----------------------|:------------|:----------------------------------------------------------|
| **Battery & Output**   | Left Top    | Battery icon/percentage and active output (USB/BLE).      |
| **Layer Status**       | Left Bottom | Name of the currently active layer (e.g., `NUM`, `MGMT`). |
| **Bluetooth Profiles** | Right       | Status of the 5 Bluetooth profiles (numbered circles).    |

### Status Indicators

- **Output**:
    - `USB`: Connected via USB.
    - `BLE`: Connected to a Bluetooth host.
    - `CLOSE`: Bonded but disconnected.
    - `SETTINGS`: Discoverable/pairing mode.
- **Bluetooth Profiles**:
    - Numbered circles represent the available BLE profiles.
    - A solid circle indicates the currently selected profile.
    - A dashed circle indicates a bonded profile.
- **Layers**:
    - Displays the `display-name` defined in the keymap.
    - Default layers: `NUM` (numeric keypad) and `MGMT` (management).

### Customisation

You can revert to the standard ZMK status widget or customise the behaviour via `bombopad.conf`:

- **To use the standard ZMK widget**:
  ```kconfig
  CONFIG_NICE_VIEW_WIDGET_STATUS=y
  ```
- **Fonts**: The widget uses Montserrat fonts (14, 16, 18) which are enabled in `Kconfig.defconfig`. Disabling these
  fonts will cause the widget to render incorrectly or fail to compile.

## Encoders

The BomboPad features two rotary encoders that can be customised for various tasks like volume control, scrolling, or
switching layers.

### Calibration

The encoder behaviour is defined by two main parameters in `boards/shields/bombopad/bombopad.overlay`:

- **`steps`**: The number of pulses the encoder hardware sends per full rotation.
- **`triggers-per-rotation`**: The number of times ZMK should trigger a keypress/action per full rotation.

By default, the BomboPad is configured for encoders with a 4:1 ratio (e.g., 80 steps and 20 triggers):

```devicetree
encoder0: encoder0 {
    steps = <80>;
};

sensors: sensors {
    triggers-per-rotation = <20>;
};
```

#### Common Encoder Settings

If your encoder feels too sensitive (multiple actions per click) or not sensitive enough (skips clicks), adjust these
values:

| Encoder Model          | Recommended `steps` | Recommended `triggers-per-rotation` |
|:-----------------------|:--------------------|:------------------------------------|
| **EC11** (Standard)    | 80                  | 20                                  |
| **EVQWGD001** (Roller) | 24                  | 24                                  |

### Customising Actions

Encoder actions are defined in the `sensor-bindings` section of each layer in `bombopad.keymap`:

```devicetree
sensor-bindings = <&inc_dec_kp K_VOL_UP K_VOL_DN &inc_dec_kp LG(K_VOL_UP) LG(K_VOL_DN)>;
```

The first entry corresponds to the left encoder (`encoder0`), and the second to the right encoder (`encoder1`).

## Keymap & Bluetooth

The BomboPad keymap is designed to be intuitive yet powerful, with dedicated layers for numbers and system management.

### Customised Behaviours

- **`cycle_layer`**:
    - **Hold**: Momentarily switches to the `MGMT` layer.
    - **Tap**: Toggles between layers (e.g., `NUM` to `MGMT` and vice-versa).
    - Defined with a `tapping-term-ms` of 200ms.
- **`ht` (Hold-Tap)**:
    - Standard hold-tap behaviour used for keys that perform different actions when held versus tapped.
    - `tapping-term-ms`: 200ms, `quick-tap-ms`: 200ms.

### Layers

#### 1. NUM (Default)

The main layer for numeric entry.

- **Left Encoder**: Volume Up/Down.
- **Right Encoder**: Command + Volume Up/Down (useful for specific OS shortcuts).

#### 2. MGMT (Management)

Used for system tasks and Bluetooth management.

- **Top Row**: System Reset, Bootloader.
- **Middle Row**: Output selection (USB, BLE, Toggle).
- **Bottom Row**: Profile cycling (Next/Prev) or Selection, and Bluetooth Clear.

### Bluetooth Management

The BomboPad's Bluetooth management varies depending on the [Build Variant](#build-variants) used:

#### With Display (`bombopad_niceview`)

- **Profiles**: Supports up to **5 Bluetooth profiles**.
- **Switching Profiles**: Use `BT_NXT` (Next) and `BT_PRV` (Previous) in the `MGMT` layer to cycle through profiles. The
  active profile is highlighted on the custom status widget.
- **Clearing Bonds**: `BT_CLR` removes the bonding information for the *currently selected* profile.

#### Without Display (`bombopad_nodisplay`)

- **Profiles**: Supports up to **2 Bluetooth profiles**.
- **Switching Profiles**: Use `BT_SEL 0` or `BT_SEL 1` in the `MGMT` layer for direct profile selection.
- **Clearing Bonds**: `BT_CLR` removes the bonding information for the *currently selected* profile.

> [!WARNING]
> **Using BT_CLR**: If you use `BT_CLR`, you must also "Forget" or "Remove" the BomboPad from your computer/device's
> Bluetooth settings before attempting to re-pair.

### Output Selection

You can force the macropad to use a specific connection method:

- `OUT_USB`: Force USB output.
- `OUT_BLE`: Force Bluetooth output.
- `OUT_TOG`: Toggle between USB and BLE.

## Future Improvements (To-Do)

The following features and improvements are planned for future updates:

- **BLE Hardening & Security**:
    - Evaluate the need for `CONFIG_ZMK_BLE_EXPERIMENTAL_CONN=y`.
    - Document the use of bonding/fixed passkey and its implications.

## Help & Contributions

Bug reports, suggestions, and contributions are welcome! Please
use [GitHub Issues](https://github.com/bombo82/bombopad/issues)
and [Discussions](https://github.com/bombo82/bombopad/discussions) for any feedback.

## Authors

- **Gianni Bombelli (bombo82)** - [GitHub Profile](https://github.com/bombo82)

## Licenses

Documentation is licensed under the GNU Free Documentation License as published by the Free Software Foundation, either
version 1.3 of the License, or (at your option) any later version.

Source code is licensed under the GNU General Public License as published by the Free Software Foundation, either
version 3 of the License, or (at your option) any later version.

Hardware design and all related items are licensed under the CERN Open Hardware Licence as published by CERN, either
version 2 of the Licence, or (at your option) any later version.

## License Disclaimer

Copyright (C) 2024-2025 Gianni Bombelli <bombo82@giannibombelli.it>

Permission is granted to copy, distribute and/or modify this document
under the terms of the GNU Free Documentation License, Version 1.3
or any later version published by the Free Software Foundation;
with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.

You should have received a copy of the GNU Free Documentation License
along with this program. If not, see <https://www.gnu.org/licenses/fdl-1.3.html>.

## Acknowledgements

Special thanks to [Arialdo](https://github.com/arialdomartini) for introducing me to the world of custom mechanical
keyboards.
