
# TOTEM ZMK Configuration

Custom ZMK firmware configuration for the [GEIGEIGEIST TOTEM](https://github.com/GEIGEIGEIST/TOTEM) split keyboard using Seeed Studio XIAO nRF52840 controllers.

This configuration uses a **QWERTY base layout** while retaining the home-row mods, layer-tap keys, mod-morphs, and other behaviors from the Miryoku-inspired keymap.

## Features

* QWERTY base layout
* Split wireless operation
* Seeed Studio XIAO nRF52840
* ZMK Studio support
* Home-row modifiers
* Layer-tap thumb keys
* Sticky modifiers
* Mod-morph punctuation
* Multiple navigation, number, function, and utility layers
* GitHub Actions firmware builds
* Prebuilt firmware available from GitHub Releases

## Hardware

This configuration targets:

* **Keyboard:** GEIGEIGEIST TOTEM
* **Controller:** Seeed Studio XIAO nRF52840
* **ZMK board:** `xiao_ble//zmk`
* **Halves:** `totem_left` and `totem_right`

## Base Layout

The alpha layout is standard QWERTY:

```text
Q W E R T | Y U I O P
A S D F G | H J K L ;
Z X C V B | N M , . /
```

The key positions remain QWERTY while retaining the custom behaviors used by the original keymap.

### Home-Row Mods

The home-row keys act as normal letters when tapped and modifiers when held.

```text
Tap:   A      S      D      F          J      K      L      ;
Hold: Ctrl   Alt    GUI   Shift      Shift   GUI    Alt    Ctrl
```

This provides access to the main modifiers without requiring dedicated modifier keys.

Additional keys may also use dual-role behaviors such as `MEH`, `HYPER`, layer switching, sticky modifiers, and mod-morphs.

## Layers

The configuration uses multiple layers for commonly used functions while keeping the physical layout compact.

The keymap includes layers for:

* Base / QWERTY
* Navigation
* Numbers
* Functions
* Utilities

See [`config/totem.keymap`](config/totem.keymap) for the complete layout and behavior definitions.

## ZMK Studio

The left half is configured as the central half and includes ZMK Studio support over USB.

The build configuration uses:

```yaml
board: xiao_ble//zmk
shield: totem_left
snippet: studio-rpc-usb-uart
cmake-args: -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n
```

The right half is built as the split peripheral.

ZMK Studio can be used to modify supported keymap settings at runtime without rebuilding the firmware.

## Prebuilt Firmware

Prebuilt firmware is available from the **Releases** section of this repository.

Download the appropriate firmware for each half:

```text
totem_left  → Left / central controller
totem_right → Right / peripheral controller
```

Use the newest release unless you specifically need an older firmware version.

## Flashing

1. Connect the XIAO nRF52840 to your computer over USB.
2. Enter the UF2 bootloader by quickly pressing the reset button twice.
3. A USB storage device should appear.
4. Copy the appropriate `.uf2` firmware file onto the drive.
5. The controller will automatically reboot into ZMK.

Flash each half with its corresponding firmware.

```text
Left half  → totem_left firmware
Right half → totem_right firmware
```

## Building

Firmware is built automatically using GitHub Actions.

After modifying the configuration:

```bash
git add .
git commit -m "Update ZMK configuration"
git push
```

GitHub Actions will build the configured firmware targets.

Build targets are defined in [`build.yaml`](build.yaml).

## Repository Structure

```text
.
├── .github/
│   └── workflows/
├── boards/
│   └── shields/
│       └── totem/
├── config/
│   └── totem.keymap
├── build.yaml
└── README.md
```

### `boards/shields/totem/`

Contains the TOTEM hardware definition, including the matrix, physical layout, split configuration, and ZMK Studio layout information.

### `config/totem.keymap`

Contains the keyboard layers, bindings, behaviors, combos, and QWERTY layout.

### `build.yaml`

Defines the firmware targets for both TOTEM halves and enables ZMK Studio on the central/left half.

## Credits

* [ZMK Firmware](https://zmk.dev/)
* [GEIGEIGEIST TOTEM](https://github.com/GEIGEIGEIST/TOTEM)
* Miryoku and community ZMK configurations that inspired the layer and home-row-mod setup

## Disclaimer

This repository contains a personal keyboard configuration and may change as the layout and behaviors are refined.

If you are flashing the prebuilt firmware onto your own TOTEM, verify that your hardware and controller configuration match the targets used by this repository.
