# KBH Totem — ZMK keymap (tomzbench layout)

Design context for **modifying the keymap going forward**. The original port is
done; this file is the reasoning + the user's preferences so future changes stay
consistent. (If you're just here to edit keys: read "Design rules" and "Layers".)

## What this is
ZMK config for the KeyboardHoarders **Totem** — a 38-key split: 3×5 alphas + 3
thumbs per hand, plus **one outer pinky per hand on row 3**. Board `xiao_ble`,
shields `totem_left` / `totem_right` (+ `settings_reset`). The layout is ported
from the user's Kinesis Advantage 360 Pro config ("tomzbench" keymap).

Key budget: 30 alphas + 6 thumbs + 2 outer pinkies = 38. The Totem has ~28
*fewer* keys than the Adv360 used (no number row, no outer symbol columns, no
bottom finger row), so everything that lived there now lives on a layer.

## Design rules (honor unless the user explicitly overrides)
1. **Thumb mods, NOT home-row mods.** Mods live on thumbs + pinkies; the home
   row stays pure letters. Reason: the user is a fast typist and home-row mods
   misfire on rolls. *Do not reintroduce home-row mods without asking.*
2. **No timing misfires; explicit beats clever.** Alpha keys that carry a mod
   use `tap-preferred` so normal typing never triggers the hold.
3. **Alt lives near the pinky.** The user uses Alt-combos a lot and is used to
   Alt on the outer-pinky area. It's a hold on `Z`/`/`, not a layer-only mod.
4. **Preserve Adv360 muscle memory** when relocating anything.
5. **Brackets sit on the opposite-hand thumbs** from the layer hold.

## User preferences (noted)
- Fast typist — **misfire-free typing is the #1 priority**.
- Uses Alt-combos often; wants Alt as a pinky **hold**.
- Comfortable with **arrows below the home row**.
- Doesn't lean on F-keys / nav — fine for them to be a layer-hold away.
- **US layout** (German/locale keys from the source were dropped).
- Prefers explicit, well-reasoned behaviors over dense tricks.

## Behaviors
**config/tomzbench.dtsi** (the hold-tap "flavors" + shift morphs):
- `kp_fast_hold` — hold-tap, hold-preferred, 200ms. Decisive mod thumbs (GUI/CTRL/ENTER/DEL/ESC).
- `kp_slow_hold` — hold-tap, tap-preferred, 250ms. SPACE (rolls a lot → tap-biased).
- `mo_fast_hold` — hold-tap (`&mo` + `&kp`), hold-preferred, 200ms. Layer thumbs (SYM/TAB, NUM/BSPC).
- `lsft_sqt` / `rsft_dqt` — shift mod-morphs: hold = Shift, tap = `'` / `"` (morph yields the opposite quote when the other Shift is held).
- `mo_slow_hold`, `grave_tilde` — defined, currently unused.

**config/totem.keymap** also defines:
- `pmt` (pinky_mod_tap) — hold-tap, tap-preferred, 200ms → Alt on `Z` and `/`.
- tri-layer `conditional_layers`: **NUM + SYM held → SYS**.

**config/macros.dtsi** — Win/Mac shortcuts + bracket/quote macros. **Defined but
not bound to any key.** Available to map if the user wants them.

## Thumb cluster (shared by all layers)
L→R: `LGUI/esc · LCTRL/spc · SYM/tab  ||  NUM/bspc · RCTRL/ent · RGUI/del`
- Inner thumbs are the layer keys (SYM on left, NUM on right).
- On **NUM**, the LEFT thumbs become `[` `]`.
- On **SYM**, the RIGHT thumbs become `{` `}`.

## Layers
### BASE (0) — QWERTY
Outer pinkies = shift morphs (tap `'`/`"`). `Z` and `/` = hold Alt / tap letter.

### NUM (1) — hold right inner thumb
Numbers on the LEFT (type with the free hand). RIGHT: mods on the home row so
`Ctrl+digit` etc. work, arrows on the row beneath them. `[ ]` on left thumbs.
```
+ 7 8 9 0      ·  ·   ·   ·   ·
- 1 2 3 =      :  GUI CTL ALT  ·
* 4 5 6 \      ·  ←   ↓   ↑    →
```

### SYM (2) — hold left inner thumb
Symbols on the RIGHT (1:1 from Adv360). Left hand transparent. `{ }` on right thumbs.
```
· · · · ·      ~ & * ( )
· · · · ·      _ ! @ # +
· · · · ·      ` $ % ^ |
```

### SYS (3) — hold NUM + SYM together
F-keys on the LEFT, **mirroring the NUM digit positions** (F1 where 1 is, … F11/F12
in the `+`/`-` corners). BT + bootloader on the RIGHT.
```
F11 F7 F8 F9 F10    BOOT  ·    ·    ·    ·
F12 F1 F2 F3  ·     BTclr BT0  BT1  BT2  BT3
 ·  F4 F5 F6  ·     BT4 BTnxt  ·    ·    ·
```

## Build
GitHub Actions on the **`tomzbench` fork** (TomzBench/zmk-totem-config). Push the
branch, download firmware from the Actions run. KBH guide:
https://keyboard-hoarders.com/pages/guides-1
`totem.conf`: 15-min sleep, pointing enabled, BT TX +8dB, ZMK Studio off.

## Intentionally excluded (don't "add back" without asking)
Physical number row, number-layer toggle/lock (`tog 1`), arrows on base,
page-nav / home / end, output-toggle / soft-reset, German locale keys.
`£` (POUND) from the source SYM was changed to `#` (HASH) for US.

## Editing tips
- Each layer's `bindings` must be **exactly 38** entries in this order:
  row1 (10) · row2 (10) · row3 (1 outer + 5 + 5 + 1 outer = 12) · thumbs (6).
- Trust the `bindings = < … >`, then update the ASCII diagram to match.
- Behaviors/macros are copied **in-tree** — the repo is self-contained (no
  dependency on the external Adv360 repo anymore).
