# PocketTracker BR v1 — Braids + Rings

Experimental RG28XX / Knulli / PortMaster build, prepared 20 October 2026.

This integrates the original Mutable Instruments Braids MacroOscillator and Rings Part DSP into PocketTracker's native audio mixer. The engine selector displays **BRAIDS** and **RINGS**. Each MODEL value displays its decimal number followed by its name. Both work without loading a sample.

## First sound

1. Create a new song. In SONG, double-tap A on an empty cell to create a chain; open it with A. Create/open a phrase in the same way.
2. Enter a note and instrument number on a phrase row. Use R + D-pad to navigate to INSTRUMENT. The existing screen navigation map is documented in `docs/quick-start-guide.md`.
3. On TYPE, hold A and use up/down to select BRAIDS or RINGS. On MODEL, hold A and use up/down for one-model steps. The name changes alongside the number. The decimal model numbering starts at zero; most other tracker values are hexadecimal.
4. Start with Braids `0 CSAW` or Rings `0 MODAL RESONATOR`, one track and a middle-register note (C-4 is a useful start). Press START to play/stop.
5. Braids: change TIMBRE and COLOR. Rings: change STRUCTURE, BRIGHTNESS, DAMPING and POSITION. These run 00–FF and update a sounding voice; retrigger a note after changing a Rings model to hear its attack.
6. Save the song from PROJECT. Instrument presets use the existing INST PRESET SAVE/LOAD buttons. Rings settings and model selection are stored in the project/preset.

Braids exposes its original 47 accessible oscillator models (0–46). The question-mark/random selector is excluded. Model names are listed below.

| Rings model | App label |
|---|---|
| 0 | MODAL RESONATOR |
| 1 | SYMPATHETIC STRING |
| 2 | INHARMONIC STRING |
| 3 | FM VOICE |
| 4 | QUANTIZED STRING |
| 5 | STRING + REVERB |

Rings uses its internal exciter, with a strum on every note. STRUCTURE, BRIGHTNESS, DAMPING and POSITION feed the original Patch parameters. The original OUT and AUX outputs feed the two channels before tracker pan/mix processing. Each tracker voice uses Rings' internal polyphony setting 1.

## Controls and files

D-pad moves; A edits/opens; A + D-pad changes values (up/down fine, left/right coarse); B returns; A+B deletes; R + D-pad changes screens; START plays/stops. Quit via PROJECT → EXIT.

The separate launcher is `PocketTracker BR.sh`, with program/data in `ports/pockettracker_br/`. It does not share the older port's `data` folder. The app creates `data/Projects`, `Samples`, `Soundfonts`, `Instruments`, `Themes` and `Renders` when launched. Existing songs can be copied into the new Projects folder; keep originals backed up. Older builds do not understand RINGS instruments.

## Scope and test status

- Original DSP sources and resources are included. No external hardware module, sample pack or proprietary M8 firmware is required. This is an unofficial PocketTracker modification, not an official Mutable Instruments product or a complete M8 firmware port.
- Full Linux desktop application compiled successfully. All 47 Braids and six Rings models produced finite, nonzero audio at 44.1 and 48 kHz. Tracker-path tests confirmed both sample-free instruments play and stop on note-off. The actual instrument editor was rendered to inspect model labels and parameter spacing.
- AArch64 cross-build and RG28XX/Knulli device operation have **not** been tested here. The supplied workflow performs the cross-build and checks its output. CPU load, latency and stability on your device remain to be tested. Start with one synth track, then add tracks gradually.
- The adapter uses 24-sample DSP blocks, Braids at 96 kHz and Rings at 48 kHz, with linear rate conversion. This is not bit-exact emulation of the hardware's analogue output and can alias at high frequencies.
- The shared synth pool has 16 slots including release tails. It is mono per tracker track with a short overlap for note transitions; the adapter supplies a 3 ms attack and 35 ms note-off fade. Rings DAMPING controls the resonator's decay within that envelope.
- This build exposes the oscillator/resonator controls listed above. It does not implement the modules' complete hardware UI, calibration, external audio excitation, Rings StringSynthPart Easter egg, or every tracker table/LFO/filter feature for these voices. Braids/Rings do not show the sampler filter/drive rows. Existing granular mode remains from the supplied base.

## Source provenance and local changes

Base: the supplied `PocketTracker_M8_RG28XX_PortMaster_Build_Kit (1).zip`, SHA-256 `cb7a2827a878b47d2c2a8a51cf88699a0d8224c871c54b950abb4c827c5ea3b7`.

Mutable source: https://github.com/pichenettes/eurorack/tree/f4cfa7697095a28d292bff758beb51deb49b5a86

stmlib: https://github.com/pichenettes/stmlib/tree/e3bd7c9cc00e4364166f9905c0509b6ffd0535ec

Braids DSP files in the supplied base match that upstream snapshot, apart from three explicit initial values in `MacroOscillator::Init`. The adapter resets voice memory before Init. Rings and stmlib were taken from the commits above. `stmlib/dsp/dsp.h` enables the existing portable math path on non-32-bit-ARM hosts, including AArch64. Two colliding Rings resource macros have a `RINGS_` prefix. Hardware-only stmlib third-party sources are omitted from the source archive because the desktop DSP does not use them.

PocketTracker's GPL-3.0 licence and existing third-party notices are retained. Mutable DSP/stmlib use their included MIT notices. The built BR package adds both notices. Keep the corresponding source ZIP available if you redistribute the executable.

## Verification

`bash tests/run_mutable_smoke.sh` compiles and checks all models. For a host build, `bash tests/run_tracker_mutable_smoke.sh /path/to/cmake-build` checks the tracker audio route. `python3 tools/verify_mutable.py /path/to/binary --allow-host` checks a host ELF; omit `--allow-host` to require AArch64.

The release check searches the executable for original Braids wavetable/sine bytes and Rings stiffness/decade table bytes, plus model labels and the BR build marker. It also checks the ELF architecture. A binary with only renamed UI labels fails. `BUILD_INFO.json` records the binary hash, source archive hash and GitHub run. Launch logs contain `PocketTracker BR v1 | ORIGINAL BRAIDS + ORIGINAL RINGS`.

The pinned workflow expects the supplied source archive unchanged. A developer editing the source should rebuild the archive and update the expected SHA-256 deliberately.

## Braids model names

| Model | App label |
|---|---|
| 0 | CSAW |
| 1 | MORPH |
| 2 | SAW SQUARE |
| 3 | SINE TRIANGLE |
| 4 | BUZZ |
| 5 | SQUARE SUB |
| 6 | SAW SUB |
| 7 | SQUARE SYNC |
| 8 | SAW SYNC |
| 9 | TRIPLE SAW |
| 10 | TRIPLE SQUARE |
| 11 | TRIPLE TRI |
| 12 | TRIPLE SINE |
| 13 | RING |
| 14 | SAW SWARM |
| 15 | SAW COMB |
| 16 | TOY |
| 17 | DIGITAL LP |
| 18 | DIGITAL PK |
| 19 | DIGITAL BP |
| 20 | DIGITAL HP |
| 21 | VOSIM |
| 22 | VOWEL |
| 23 | VOWEL FOF |
| 24 | HARMONICS |
| 25 | FM |
| 26 | FEEDBACK FM |
| 27 | CHAOTIC FM |
| 28 | PLUCK |
| 29 | BOW |
| 30 | BLOW |
| 31 | FLUTE |
| 32 | BELL |
| 33 | DRUM |
| 34 | KICK |
| 35 | CYMBAL |
| 36 | SNARE |
| 37 | WAVETABLE |
| 38 | WAVEMAP |
| 39 | WAVE LINE |
| 40 | WAVE PARAPHONIC |
| 41 | FILTER NOISE |
| 42 | TWIN PEAKS |
| 43 | CLOCK NOISE |
| 44 | CLOUD |
| 45 | PARTICLES |
| 46 | DIGITAL MOD |
