# Validation

Use this reference before claiming a StopWatch app is done.

## Static Checks

```bash
git status -sb
git submodule status --recursive
git diff --check
```

For app submodules, run `git diff --check` in both the app repo and the host repo when both are changed.

## HTML Checks

Open the prototype directly:

```bash
open web/index.html
```

Automated minimum:

- Load `web/index.html` in Playwright.
- Capture console/page errors.
- Verify canvas or primary UI is nonblank.
- Perform one pointer/key interaction and verify state changed.

## Firmware Build

Use the local ESP-IDF install:

```bash
source /Users/xurui/esp/esp-idf/export.sh
idf.py build
```

Record image size and free partition space from build output.

## Flash

Default device port:

```bash
idf.py -p /dev/cu.usbmodem21301 flash
```

If unavailable, list only USB serial/modem candidates:

```bash
ls /dev/cu.usb*
```

Do not use Bluetooth audio ports.

## Monitor

```bash
idf.py -p /dev/cu.usbmodem21301 monitor
```

Evidence to report:

- boot reaches Launcher;
- app `on create` appears;
- app `on open` appears if manually opened or automation triggers it;
- no panic/assert/LVGL error in observed log window.

Exit monitor with `Ctrl+]`.

## Push Order

For submodule app changes:

```bash
git -C thirdparty/<App>-StopWatch push origin main
git add thirdparty/<App>-StopWatch
git commit
git push origin HEAD:main
```
