---
name: stopwatch-development
description: Specialized workflow for developing M5Stack StopWatch Mooncake apps and matching HTML prototypes. Use when Codex needs to design, implement, review, extract, or maintain StopWatch app modules, LVGL UI layers, hardware feedback behavior, app submodules under thirdparty, browser HTML prototypes, or ESP-IDF build/flash validation for this firmware.
---

# StopWatch Development

Use this skill to keep StopWatch app work consistent across the browser prototype,
the extracted app repository, and the host Mooncake firmware.

## Workflow

1. Inspect the actual repo state before judging anything.
   - Use `git status -sb`, `git submodule status --recursive`, and `rg`.
   - Do not infer app registration or asset wiring without checking source files.
2. Start with an HTML prototype for new UI/interaction work when the user wants visual iteration.
   - Copy `assets/html-framework/` as the first version.
   - Keep prototype logic close to hardware semantics: same button states, timing, density/gear constants, feedback gates, and result formatting.
3. Move confirmed behavior into the hardware app.
   - Put reusable app code in its own repo under `hardware/mooncake/main/apps/app_<name>/`.
   - Put launcher assets under `hardware/mooncake/main/assets/images/`.
   - Keep public hardware integration points documented in `hardware/mooncake/INTEGRATION.md`.
4. Integrate into the host StopWatch firmware through submodules.
   - Prefer `thirdparty/<AppName>-StopWatch`.
   - Update `main/CMakeLists.txt`, `main/apps/apps.h`, `main/main.cpp`, and `main/assets/assets.h`.
5. Validate with real commands.
   - For HTML: open with Playwright or browser and check console errors plus nonblank render.
   - For firmware: run `source /Users/xurui/esp/esp-idf/export.sh && idf.py build`.
   - If requested or needed, flash with `idf.py -p /dev/cu.usbmodem21301 flash` and monitor boot logs.
6. Commit in the right repositories.
   - Commit app repo changes first.
   - Push the app repo.
   - Commit the host repo submodule pointer second.
   - Push the host repo.

## Reference Files

Read these only when needed:

- `references/repo-structure.md`: known StopWatch host/submodule layout and integration points.
- `references/mooncake-app-pattern.md`: app lifecycle, key handling, LVGL locking, feedback cleanup.
- `references/html-prototype-parity.md`: how to keep browser prototypes aligned with hardware logic.
- `references/validation.md`: build, flash, monitor, and push checklist.

## Implementation Rules

- Preserve unrelated dirty files and submodule state.
- Prefer existing Mooncake, HAL, LVGL, and `uitk` patterns over new framework code.
- Keep UI constants named and mirrored between HTML and hardware when the prototype is the spec.
- Use runtime allocation checks on ESP32 paths; C++ exceptions are disabled in the current firmware.
- Stop audio and vibration on app close when an app owns feedback behavior.
- Use `LvglLockGuard` around LVGL object creation, update, and destruction in app lifecycle methods.
- Treat logs, build output, and source lines as evidence; do not state hardware behavior as confirmed without monitor or device observation.

## HTML Framework

Use `assets/html-framework/index.html` for a new prototype. The template includes:

- 466x466 circular device viewport.
- Left/right hardware key simulation with keyboard support.
- Pointer input surface.
- State constants block intended to match C++ constants.
- Basic audio and vibration feedback helpers.
- Console-free browser validation surface.

Copy it into an app repo as `web/index.html`, then specialize visuals and logic.
