# BomboPad ZMK Firmware

BomboPad is an open-source, versatile 12-key macropad featuring dual rotary encoders and display support. Designed with
a focus on flexibility, it supports both wired and wireless configurations.

This directory contains the **ZMK firmware** implementation for the BomboPad, designed for wireless use with controllers
like the Nice!Nano.

## Key Features (v0.3)

The current firmware version supports the following hardware features:

- **12 keys**: Matrix layout 3x4.
- **Dual Rotary Encoders**: Supports EC11 or EVQWGD001 encoders.
- **Display Support**: Nice!View.
- **Microcontroller**: Nice!Nano v2 or other compatible wireless controllers e.g. SuperMini NRF52840.
- **Wireless**: Bluetooth LE support via ZMK.

## Hardware Availability

For hardware design files, electrical schematics, and PCB layouts, please refer to the 
**[main BomboPad repository](https://github.com/bombo82/bombopad)**.

## Build Instructions

To build the ZMK firmware, run the following command from your ZMK `app` directory:

```bash
west build -p -b nice_nano -- -DSHIELD="nice_view_adapter nice_view bombopad" -DZMK_EXTRA_MODULES="/path/to/zmk-bombopad"
```

Replace `/path/to/zmk-bombopad` with the absolute path to this repository.

## Keymap Information

The ZMK keymap is located at `boards/shields/bombopad/bombopad.keymap`.

### Custom Behaviors

The keymap includes custom behaviors to enhance the macropad's functionality:

1. **`cycle_layer`**:
    - A hold-tap behavior that combines `mo` (momentary) and `to` (toggle).
    - **Hold**: Momentarily activates a layer (usually the management layer).
    - **Tap**: Toggles to a specific layer.
    - Used to navigate through the different functional layers of the macropad.

2. **`ht` (Hold-Tap)**:
    - A standard hold-tap behavior used for multi-function keys (e.g., Command+Key on hold, simple Key on tap).

### Layers

The default keymap defines the following layers:

- `NUM`: Numeric keypad layer (Default).
- `MGMT`: Management layer for Bluetooth profile selection, output toggling, and reset/bootloader functions.

## Help & Contributions

Bug reports, suggestions, and contributions are welcome! Please
use [GitHub Issues](https://github.com/bombo82/bombopad/issues)
and [Discussions](https://github.com/bombo82/bombopad/discussions) for any feedback.

## Authors

- **Gianni Bombelli (bombo82)** - [GitHub Profile](https://github.com/bombo82)

## Licenses

Documentation are licensed under GNU Free Documentation License as published by the Free Software Foundation, either
version 1.3 of the License, or (at your option) any later version.

Source code are licensed under GNU General Public License as published by the Free Software Foundation, either version 3
of the License, or (at your option) any later version.

Hardware design and all related things are licensed under CERN Open Hardware Licence as published by the CERN, either
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

Special thanks to [Arialdo](https://github.com/arialdomartini) for introducing me to the world of custom mechanical keyboards.
