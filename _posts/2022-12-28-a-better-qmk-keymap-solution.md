---
layout: post
title: A Better QMK Keymap Solution
category: Keyboard
tags: [keyboard]
---

Starting with the [Ergodox EZ](https://ergodox-ez.com/), my keyboards have used QMK firmware to define the keyboard's keys and functionality. This is accomplished via a handful of configuration files getting compiled into the `.hex` file that ultimately gets flashed to the keyboard.

Currently, these two pieces - the actual QMK firmware and the individual keyboard configurations (called keymaps) are stored within the same repository, though there are [discussions to change this](https://github.com/qmk/qmk_firmware/issues/1120).

# Previous Implementation

The approach I took when starting to use QMK and the Ergodox EZ was the simplest - I forked the [QMK repository](https://github.com/qmk/qmk_firmware) and created my keyboard configurations on my personal fork.

This worked well for a while, but it was somewhat painful having to continually merge the upstream QMK firmware changes with the personal keymap changes. It is possible (and encouraged) to merge keymap changes to the upstream repository, but this can be tedious as a keyboard undergoes many tests/changes. Additionally, the personal keymap changes can become hard to find in between the countless upstream changes (including changes to other unrelated keymaps).

**Advantages**:

- Easy to get started
- Can easily merge keymap changes into official repository

**Disadvantages**:

- Hard to keep QMK source in parity with official repository
- Personal keyboard configurations are stored alongside the hundreds of others in the repository

# New Implementation

Recently, I decided to look for an easier solution when re-creating my Corne keyboard keymap. I found [this alternative approach](https://github.com/henrebotha/qmk_keymap) that makes use of a [git submodule](https://git-scm.com/docs/git-submodule) to track the QMK repository changes, isolating them from the personal keymap changes.

The repository structure is thus:

- `qmk_firmware` - directory/submodule containing upstream QMK firmware
- `keymaps` - directory storing personal keymaps
- `build.sh` - shell file to handle moving the keymaps to the appropriate directory in the submodule's source code to allow compiling the firmware.

This provides a clear separation between the keymaps and the firmware source code. This also makes it clear where the focus of my changes will be, instead of burying them five levels deep in QMK keymap directories. Further, the git submodule provides a clear indication of what upstream change the firmware is currently pointing at, and provides a simple git command to update the submodule to the latest version.

Currently, the only noticed downside is that as it is currently written, the build script only works with a single keymap. This is not currently a problem as I am only working with a single keyboard at this time. If multiple keymap/keyboard support is needed, then some complexity could be added to the build script to support this.

**Advantages**:

- Easier to update QMK source with upstream changes
- Clear separation of keyboard configurations and QMK source
- Easier to identify personal keyboard configurations from upstream/other user configurations

**Disadvantages**:

- Harder to set up initially
- Harder to support multiple keyboard configurations simultaneously
