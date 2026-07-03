# Tempest CAX 2026 ROM set

This is the `cax2026` modified ROM set for Atari's arcade game TEMPEST.

- `tempest3.zip` — MAME-ready ROM archive (20 chip files, standard
  `tempest3` naming/layout).
- `136002-*.d1/.e1/.f1/...` — the same 20 chip images, unzipped, in case
  you need to inspect or re-pack individual files.

## What this build does

Video walkthrough: https://www.youtube.com/watch?v=DEtjrcK1U0o&t=11s

- Purple levels beyond green — level families extend past L96 into a
  new purple range. Purple levels are even harder than green.
- 7-digit scoring (stock Tempest is limited to 6 digits).
- Start on any level L1-L112, with the corresponding start-of-level
  bonus scaled up to 1,410,000 (1410K).
- Last-level-reached is displayed for each entry in the high score table.

### Self-test disabled

This build disables the cabinet/service-menu self-test mode. That code
was overwritten to reclaim ROM space needed for the new mods.

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

- This is a hacked/patched ROM set (not the stock Atari Tempest program
  ROMs) — expect gameplay behavior (scoring, levels, colors, etc.) to
  differ from the original arcade release.
- The individual chip files are provided only for inspection/repacking;
  MAME itself only needs `tempest3.zip`.