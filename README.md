# TOTEM ZMK Configuration

Custom ZMK firmware configuration for the [GEIGEIGEIST TOTEM](https://github.com/GEIGEIGEIST/TOTEM) split keyboard using Seeed Studio XIAO nRF52840 controllers.

This configuration uses a **QWERTY base layout** while retaining the home-row mods, layer-tap keys, mod-morphs, sticky modifiers, and other Miryoku-inspired behaviors.

## Features

* QWERTY base layout
* Split wireless operation
* Seeed Studio XIAO nRF52840
* Optional dedicated XIAO BLE dongle
* ZMK Studio support
* Home-row modifiers
* Layer-tap thumb keys
* Sticky modifiers
* Mod-morph punctuation
* Navigation, number, function, and utility layers
* GitHub Actions firmware builds
* Prebuilt firmware available from GitHub Releases

## Hardware

This configuration targets:

* **Keyboard:** GEIGEIGEIST TOTEM
* **Controller:** Seeed Studio XIAO nRF52840
* **ZMK board:** `xiao_ble//zmk`
* **Keyboard halves:** `totem_left` and `totem_right`
* **Optional receiver:** `totem_dongle`

## Base Layout

The alpha layout is standard QWERTY:

```text
Q W E R T | Y U I O P
A S D F G | H J K L ;
Z X C V B | N M , . /
```

The key positions remain QWERTY while retaining the custom behaviors used by the Miryoku-inspired keymap.

### Home-Row Mods

The home-row keys act as normal letters when tapped and modifiers when held.

```text
Tap:   A      S      D      F          J      K      L      ;
Hold: Ctrl   Alt    GUI   Shift      Shift   GUI    Alt    Ctrl
```

Additional keys also use dual-role behaviors such as `MEH`, `HYPER`, layer switching, sticky modifiers, and mod-morphs.

## Layers

The keymap includes:

* Base / QWERTY
* Navigation
* Numbers
* Functions
* Utilities

See [`config/totem.keymap`](config/totem.keymap) for the complete layout and behavior definitions.

# Firmware Modes

The keyboard can be used in two different configurations.

## Without Dongle

Standard ZMK split operation:

```text
Right Half ──BLE──> Left Half ──BLE or USB──> Host
                   Central
```

The left half acts as the split central.

Use the firmware from the **main branch / non-dongle release**:

```text
Left half  → totem_left.uf2
Right half → totem_right.uf2
```

ZMK Studio is accessed through the left half over USB.

## With Dongle

The dedicated XIAO BLE dongle becomes the split central:

```text
Left Half  ──BLE──┐
                  ├──> XIAO Dongle ──USB──> Host
Right Half ──BLE──┘
```

In this mode:

* Left half = BLE peripheral
* Right half = BLE peripheral
* Dongle = BLE central
* Dongle = USB HID device
* Dongle = ZMK Studio interface

Use the firmware from the **dongle branch / dongle release**:

```text
Left half  → totem_left.uf2
Right half → totem_right.uf2
Dongle     → totem_dongle.uf2
```

The dongle build supports two split BLE peripherals and uses a mock key scanner because the dongle itself has no physical keys.

# Prebuilt Firmware

Prebuilt `.uf2` firmware is available from the **Releases** section of this repository.

Make sure you use firmware from the configuration you intend to run.

### Direct / No-Dongle Setup

```text
totem_left.uf2
totem_right.uf2
```

### Dongle Setup

```text
totem_left.uf2
totem_right.uf2
totem_dongle.uf2
settings_reset.uf2
```

Do not mix left/right firmware from the direct configuration with firmware from the dongle configuration. The split roles are different.

# Entering the XIAO Bootloader

For any XIAO nRF52840:

1. Connect the controller over USB.
2. Quickly press RESET twice.
3. The XIAO should appear as a UF2 USB storage device.
4. Copy the required `.uf2` file onto it.
5. The controller will reboot automatically after flashing.

# Flashing Without a Dongle

For a fresh direct split setup:

1. Turn both keyboard halves off.
2. Connect the left controller over USB.
3. Double-tap RESET to enter the UF2 bootloader.
4. Copy `totem_left.uf2`.
5. Disconnect the left half.
6. Connect the right controller.
7. Double-tap RESET.
8. Copy `totem_right.uf2`.
9. Turn both halves on.
10. Pair the keyboard to the host from the left/central half.

The architecture is:

```text
Left  = central
Right = peripheral
```

ZMK Studio is accessed by plugging the **left half** into the computer over USB.

# Flashing With a Dongle

When moving to the dongle architecture, reset the stored ZMK settings on **all three controllers** first.

This is important because ZMK stores split BLE bonding information in persistent flash. Normal firmware flashing does not necessarily erase those bonds.

## Initial Dongle Setup

Turn all controllers off before starting.

### 1. Reset and Flash the Dongle

Connect the XIAO that will be used as the dongle.

Flash:

```text
settings_reset.uf2
```

After it reboots, enter the bootloader again and flash:

```text
totem_dongle.uf2
```

### 2. Reset and Flash the Left Half

Connect the left keyboard controller.

Flash:

```text
settings_reset.uf2
```

Then enter the bootloader again and flash:

```text
totem_left.uf2
```

### 3. Reset and Flash the Right Half

Connect the right keyboard controller.

Flash:

```text
settings_reset.uf2
```

Then enter the bootloader again and flash:

```text
totem_right.uf2
```

### 4. Start the Keyboard

After all three controllers have been flashed:

1. Connect the dongle to the computer.
2. Turn on the left half.
3. Turn on the right half.
4. Allow the halves to establish their split BLE connections to the dongle.

The final architecture is:

```text
Left   = peripheral
Right  = peripheral
Dongle = central
```

The host sees the dongle as the keyboard over USB.

ZMK Studio is accessed through the **dongle**, not through either keyboard half.

# Switching Between Dongle and Non-Dongle Modes

When changing between these architectures:

```text
Direct:
Left = central
Right = peripheral

Dongle:
Dongle = central
Left   = peripheral
Right  = peripheral
```

flash `settings_reset.uf2` before flashing the firmware for the new configuration.

For the cleanest transition, reset every controller participating in the new split arrangement.

For example, switching from direct mode to dongle mode:

```text
Dongle:
settings_reset → totem_dongle

Left:
settings_reset → totem_left

Right:
settings_reset → totem_right
```

If switching back to direct mode, reset the keyboard halves before flashing the direct-mode left/right firmware again.

# ZMK Studio

## Without Dongle

Connect the left half over USB.

```text
PC ──USB──> Left Half
```

The left half is both the split central and Studio endpoint.

## With Dongle

Connect the dongle over USB.

```text
PC ──USB──> Dongle
```

The dongle is the split central and Studio endpoint.

The dongle firmware is built with:

```yaml
snippet: studio-rpc-usb-uart
cmake-args: -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n
```

# Building

Firmware is built automatically using GitHub Actions.

After making changes:

```bash
git add .
git commit -m "Update ZMK configuration"
git push
```

Build targets are defined in [`build.yaml`](build.yaml).

## Main Branch

The `main` branch contains the standard direct split configuration.

```text
Left  = central
Right = peripheral
```

## Dongle Branch

The `dongle` branch contains the dedicated receiver configuration.

```text
Dongle = central
Left   = peripheral
Right  = peripheral
```

Its build outputs include:

```text
totem_left
totem_right
totem_dongle
settings_reset
```

# Repository Structure

```text
.
├── .github/
│   └── workflows/
├── boards/
│   └── shields/
│       └── totem/
│           ├── Kconfig.defconfig
│           ├── Kconfig.shield
│           ├── totem.dtsi
│           ├── totem-layouts.dtsi
│           ├── totem_left.overlay
│           ├── totem_right.overlay
│           ├── totem_dongle.overlay
│           └── totem_dongle.conf
├── config/
│   ├── combos.dtsi
│   └── totem.keymap
├── build.yaml
└── README.md
```

### `boards/shields/totem/`

Contains the TOTEM hardware definition, split configuration, matrix transform, physical layout, controller-specific overlays, and dongle configuration.

### `config/totem.keymap`

Contains the keyboard layers, bindings, custom behaviors, combos, and QWERTY layout.

### `build.yaml`

Defines the firmware targets generated by GitHub Actions.

# Credits

* [ZMK Firmware](https://zmk.dev/)
* [GEIGEIGEIST TOTEM](https://github.com/GEIGEIGEIST/TOTEM)
* Miryoku and community ZMK configurations that inspired the layer and home-row-mod setup
* Community ZMK dongle configurations that helped inform the dedicated receiver setup

# Disclaimer

This repository contains a personal keyboard configuration and may change as the layout and behaviors are refined.

Before flashing prebuilt firmware, verify that your hardware uses the Seeed Studio XIAO nRF52840 and that you are using firmware from the correct configuration: **direct** or **dongle**.
