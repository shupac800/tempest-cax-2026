# Tempest CAX 2026 ROM set

This is the `cax2026` modified ROM set for Atari's arcade game TEMPEST.

- `tempest3.zip` — MAME-ready ROM archive (20 chip files, standard
  `tempest3` naming/layout).
- `136002-*.d1/.e1/.f1/...` — the same 20 chip images, unzipped. These
   can be burned onto a 2716 EPROM and installed into an original Tempest 
   main board.

## Compatibility

This patch set is designed for the Atari Tempest Rev 3 ROM set
(`tempest3`). The patched ROMs need to be used with a matched Rev 3 set of
EPROMs and PROMs. Mixing these patched ROMs with Rev 1 or Rev 2 ROMs,
PROMs, or other patched sets should be expected not to work.

It is fine to use these images on a board jumpered for 2716 EPROMs rather
than 2732 EPROMs, provided all installed EPROMs and PROMs are the Rev 3
parts.

## What this build does

Video walkthrough: https://www.youtube.com/watch?v=DEtjrcK1U0o

- Purple levels beyond green — level families extend past L96 into a
  new purple range. Purple levels are even harder than green.
  (Note: Tempest's color hardware (AVG `colorram` decode) drives each of
  R/G/B as an on/off bit, not a variable level — the 8 producible hues
  are black/blue/red/magenta/green/cyan/yellow/white. Orange isn't
  reachable this way, since it needs a partial-intensity green the
  hardware can't generate; that's a discrete design constraint, not
  something a future ROM patch could add.)
- 7-digit scoring (stock Tempest is limited to 6 digits).
- Start on any level L1-L112, with the corresponding start-of-level
  bonus scaled up to 1,410,000 (1410K).
- Last-level-reached is displayed for each entry in the high score table.
- Attract-mode no-death, selectable by DIP switch (see below).
- All DIP difficulty settings are safe on all levels (see below).

## Hard-difficulty fix (July 2026 revision)

Revisions of this ROM set prior to July 2026 could soft-lock on **Hard**
difficulty at level 96 and above (including every purple level): once the
last enemy died, the level never ended and the game sat waiting for
enemies that would never spawn, in both attract mode and real gameplay.

The cause is a latent bug in stock Tempest, not something this build
introduced: on Hard, the game boosts each wave's enemy count *after* the
per-level data is loaded, with no upper bound, and any count above 64
overflows the game's 64-slot enemy-spawn array — the wave's
enemies-remaining counter can then never reach zero. Stock Rev 3 has
enemy counts of 58-61 at L96-L99, so an unmodified board on Hard would
hang there too; this build's extended levels (and its attract mode, which
can demo any level) simply made the bug much easier to encounter.

The current revision clamps the Hard-difficulty boost to the 64-enemy
array limit. Counts of 64 and below are untouched (byte-identical
behavior to stock), and Easy/Medium settings were never affected. If your
chips were burned from a pre-July-2026 revision, reburn **D1**
(`136002-113.d1`) and **R1** (`136002-222.r1`) to pick up the fix.

## Hard-difficulty spiker-speed fix (July 2026 revision 2)

Revisions prior to this fix had near-stationary spikers on **Hard**
difficulty at L101 and above (all but the first four purple levels), in
attract mode and gameplay alike. The cause was an underflow in this
build's own purple-level speed hook: on purple waves it computes the
spiker speed as the enemy base speed minus 48, but Tempest stores speeds
as a single byte meaning (byte − 256), always negative. Hard difficulty's
stock 1/8 speed boost lowers the base-speed byte enough that subtracting
48 more wraps the byte, turning an intended speed of about −280 into −2
to −18 — a crawling spiker (Medium's spikers run −235 to −250 on the
same waves).

The fix clamps the computed spiker speed at −255, the fastest value a
single speed byte can express. Non-underflowing cases (all of
Easy/Medium, and Hard at L97–L100) are byte-identical to the previous
revision. Only **R1** (`136002-222.r1`) changed; reburn it to pick up
the fix.

## Attract-mode no-death (DIP switch L12:8)

This build adds an optional mode in which the demo/CPU player cannot die
**while the game is in attract (demo) mode** — the attract game keeps
running instead of ending on a hit. It has **no effect on real
gameplay**; a paying player dies exactly as in stock Tempest.

The option is controlled by **DIP switch L12 position 8** (the DSW2 bit 0
location on the Tempest main board):

| L12:8    | Attract-mode no-death |
|----------|-----------------------|
| **ON**   | Enabled — attract player cannot die |
| OFF      | Disabled — attract deaths behave like stock |

This DIP location is **active-low**, so "ON" corresponds to the switch's
raw-clear state (the same physical position that stock Tempest uses for
1-credit minimum). If the mode does not appear to engage, flip L12:8 to
its other position.

**Important — this switch no longer selects "2 credit minimum."** To make
room for the mode, this build repurposes L12:8 and removes the original
2-credit-minimum feature entirely: regardless of the switch position, the
game behaves as 1-credit minimum and the "2 CREDIT MINIMUM / INSERT COINS"
attract messaging is gone. The switch's *only* function on this ROM set is
the attract-mode no-death toggle above.

In MAME, set this from the in-game **Dip Switches** menu (Tab → Dip
Switches) rather than a physical switch.

## Burning EPROMs

All 10 program ROMs in a stock `tempest3` set are 2Kx8 **2716** EPROMs.
This build modifies 9 of those 10 — the following must be erased and
reprogrammed to install this hack on a real board:

| Chip           | Board socket |
|----------------|--------------|
| 136002-113.d1  | D1           |
| 136002-115.f1  | F1           |
| 136002-316.h1  | H1           |
| 136002-217.j1  | J1           |
| 136002-118.k1  | K1           |
| 136002-119.lm1 | LM1          |
| 136002-120.mn1 | MN1          |
| 136002-121.p1  | P1           |
| 136002-222.r1  | R1           |

`136002-114.e1` (socket E1) is unchanged by this build and does not need
to be reburned — the stock chip works as-is. The two vector ROMs
(`136002-123.np3`, `136002-124.r3`) and the eight shared PROMs
(`136002-125.d7`, `126.a1`, `127.e1`, `128.f1`, `129.h1`, `130.j1`,
`131.k1`, `132.l1`) are likewise untouched.

## Running in MAME

MAME identifies ROM sets by the **driver/shortname**, not the zip's file
name — `tempest3.zip` must be discoverable under a directory MAME searches
via `-rompath`, and you invoke the driver as `tempest3`.

1. Make sure you have a MAME build that includes the `tempest3` driver
   (standard MAME does).
2. From a command prompt, run:

   ```
   mame.exe tempest3 -rompath "C:\path\to\tempest-cax-2026"
   ```

   Replace `C:\path\to\tempest-cax-2026` with wherever this directory
   lives, and `mame.exe` with the full path to your MAME executable if
   it's not on your `PATH`.

### Alternative: drop into MAME's own rom folder

If you'd rather not pass `-rompath`, copy `tempest3.zip` into the `roms`
folder of your MAME install (e.g. `<mame_dir>\roms\tempest3.zip`) and just
run:

```
mame.exe tempest3
```

### Notes

- In the purple levels, the player receives an additional full-strength
  superzapper. So, three total: two full-strength zaps plus one "most
  dangerous enemy" zap.
- The individual chip files are provided for burning individual EPROMs.
  MAME itself only needs the ZIP archive `tempest3.zip`.
- This build disables the cabinet/service-menu self-test mode — that
  code was overwritten to reclaim ROM space needed for the mods.
