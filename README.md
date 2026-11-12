zmk-config for CCK_BALL (4x6)
![白色成品+手托](https://github.com/user-attachments/assets/7b2834f0-ca09-4f8c-9fc1-12455f67ae44)
![白色成品2](https://github.com/user-attachments/assets/7fe073d5-bb1c-4e42-b150-3521b4418ae0)
![黑色成品1](https://github.com/user-attachments/assets/4a7023ca-07ca-4a63-9d32-9af96ff9c73f)
![黑色成品2](https://github.com/user-attachments/assets/34d4351d-cbd9-4c82-a2e0-c7c9d28c5942)

## Overview

Split wireless 4x6 trackball keyboard firmware (ZMK) for nice!nano v2 controllers.

- **Right half:** central role (plug USB into this half), PMW3610 trackball, right encoder, ZMK Studio over USB
- **Left half:** peripheral, left encoder
- **Software stack:** ZMK `main` (Zephyr 4.1) plus [badjeff/zmk-pmw3610-driver](https://github.com/badjeff/zmk-pmw3610-driver) (`main`)

## Building

Push to GitHub. The Actions workflow (`.github/workflows/build.yml`, uses ZMK's `@main` build workflow) reads the **root** `build.yaml` and builds:

| Target | Board | Notes |
|---|---|---|
| `cck_ball_left` | `nice_nano//zmk` | peripheral |
| `cck_ball_right` | `nice_nano//zmk` | central, ZMK Studio enabled (`studio-rpc-usb-uart`, locking off) |
| `settings_reset` | `nice_nano//zmk` | clears pairing and settings |

Note: on ZMK `main` the old board name `nice_nano_v2` no longer exists; it is `nice_nano//zmk`.
`config/build.yaml.unused` is an old, ignored copy; only the root `build.yaml` counts.

## Flashing

1. Double-tap the reset button on a half to enter the bootloader (a USB drive appears).
2. Copy the matching `.uf2` onto the drive.
3. Do the other half. If the halves will not pair, flash `settings_reset` on both first, then flash the normal firmware on both again.

## Features and layers

| Layer | Name | How to reach it |
|---|---|---|
| 0 | QWERTY | default |
| 1 | F_layers (F1-F12, mouse buttons) | hold the inner left thumb key (`&mo 1`, next to the left space bar) |
| 2 | BT_layers (Bluetooth profiles) | hold the inner right thumb key (`&mo 2`, next to the right space bar) |
| 3 | scroll-layers | hold `F` (`&lt 3 F`): trackball becomes a scroll wheel |
| 4 | snipe-layers | not wired to any key yet |

**Encoders:** both encoders act as a normal mouse wheel on every layer: one click is one wheel notch
(clockwise = down, counter-clockwise = up), which makes them suitable for zooming in AutoCAD.
Smooth/hi-res scrolling is deliberately off (`CONFIG_ZMK_POINTING_SMOOTH_SCROLLING=n`).

**ZMK Studio:** open https://zmk.studio and connect over USB to the right half. Studio locking is
disabled in `build.yaml`, so the keymap is editable immediately.

## Tuning

- **Trackball direction wrong:** in `cck_ball_right.overlay` toggle `swap-xy;`, `invert-x;`, `invert-y;`.
- **Trackball speed:** `cpi = <400>;` in the same node (200 to 3200, steps of 200).
- **Trackball scroll speed (layer 3):** the `1 16` values in the `scroll` block of `trackball_listener`.
- **Encoder gives 0 or 2 notches per click:** adjust `tap-ms` (24) or `ZMK_POINTING_DEFAULT_SCRL_VAL` (64) in `cck_ball.keymap`.
- **Encoder direction:** swap the two bindings of `scroll_encoder` in `cck_ball.keymap`.
- **Encoder triggers twice per click or every other click:** `steps` in `cck_ball.dtsi` is pulses per full rotation (24 detents x 2 pulses = 48).

## Files

- `config/cck_ball.keymap`: layers, encoder behavior, mouse tuning
- `config/cck_ball.conf`: settings for both halves; `config/boards/shields/cck_ball/*.conf`: per-half settings
- `config/boards/shields/cck_ball/`: shield definition (matrix, encoders, trackball)
- `config/west.yml`: ZMK and driver versions
- `config/cck_ball_old.keymap`, `Weekrhino.keymap`: reference copies of earlier keymaps, not used by the build
