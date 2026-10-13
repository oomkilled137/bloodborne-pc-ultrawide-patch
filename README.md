# bloodborne-pc-ultrawide-patch

21:9 ultrawide output (**3440x1440** and **2560x1080**) for
[bbport](https://github.com/deadinside28/bloodborne_pc), the native Linux port of Bloodborne.
The 3D view gets wider instead of stretched. The HUD and menus keep their usual 16:9 layout
rather than moving out to the screen edges, and lock-on markers and enemy health bars line up
with the wider view.

This repository holds only a source patch for bbport. It contains no game files: you need your
own copy of the game and a bbport checkout, as described in bbport's README.

## What the patch does

- Adds **2560 x 1080 (21:9)** and **3440 x 1440 (21:9)** to the output resolution choices in
  the launcher, the in-game System menu and the overlay menu.
- `scripts/patches.py`: for a 21:9 output, builds the resolution patch from the community
  "Resolution Patch 2560x1080 (21:9)" (by Kyo, already in bbport's `patches/Bloodborne.xml`).
  That gives the wider camera and the 2560x1080 HUD canvas. The camera aspect ratio is set from
  the output size (2.389 at 3440x1440).
- `run.sh`: 21:9 always uses the startup resolution patch (the live resolution mode keeps the
  game at 16:9). It passes the UI canvas size to the renderer as `BB_UI_RES`.
- Renderer (`ui_composition.h`): the UI is recognised and scaled by that canvas size instead of
  a fixed 1920x1080. It fills the whole output: at 3440x1440 the HUD is 0.8% wider than its true
  proportions, the same as with the community patch alone. At 2560x1080 it is exact.
- Switching to or from 21:9 in the game's menu asks for a restart instead of switching live.
- Tests for all of the above.

16:9 outputs and the live resolution mode behave exactly as before.

## Applying

The patch is made against bbport commit
[`c7e3e55`](https://github.com/deadinside28/bloodborne_pc/commit/c7e3e55eff7c4a37fed314766d2180a9028c35db)
(0.5).

```sh
git clone https://github.com/deadinside28/bloodborne_pc.git
cd bloodborne_pc
git checkout c7e3e55
git apply /path/to/bloodborne-pc-ultrawide-patch/patches/0001-21-9-ultrawide-output.patch
./build.sh
```

Then pick **3440 x 1440 (21:9)** in the launcher, or set `output_res=3440x1440` in
`bbport.ini`, and start the game. The terminal should show:

```
Patches: scene 2294x960; UI 2560x1080, camera aspect 2.389
```

(the scene size depends on the upscaler preset; 2294x960 is FSR Quality).

## Known limits

- Pre-rendered movies and some menus stay 16:9 with side bars.
- Some in-engine cutscenes may show things at the screen edges that were not meant to be seen.
- TAA and upscaler-off at 21:9 have not been tested much.
- 32:9 is not supported.

## Credits and license

- [bbport](https://github.com/deadinside28/bloodborne_pc) and its authors: the port itself.
- Kyo: the community 21:9 resolution patch the game-side changes come from.

Like bbport, this patch is licensed under the GNU General Public License v2 (see `LICENSE`).
