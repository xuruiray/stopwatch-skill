# StopWatch Repo Structure

Use this reference when wiring an extracted app repo into the host StopWatch firmware.

## Host Firmware

Current host repo shape verified from `/Users/xurui/.codex/worktrees/f2f2/StopWatch`:

- `main/CMakeLists.txt` owns host source collection and app submodule source inclusion.
- `main/apps/apps.h` includes app headers.
- `main/main.cpp` installs apps into Mooncake.
- `main/assets/assets.h` declares LVGL image/font symbols.
- `thirdparty/Ratchet-StopWatch` and `thirdparty/Schulte-StopWatch` are app submodules.

## Extracted App Repo Shape

Use this shape for app-specific repos:

```text
<App>-StopWatch/
├── README.md
├── web/index.html
└── hardware/mooncake/
    ├── INTEGRATION.md
    └── main/
        ├── apps/app_<name>/
        │   ├── app_<name>.h
        │   ├── app_<name>.cpp
        │   └── view/
        │       ├── view.h
        │       └── view.cpp
        └── assets/images/icon_<name>.c
```

## Host Integration Pattern

In `main/CMakeLists.txt`, define the app module directory, check it exists, append its sources, and add its app include directory:

```cmake
set(APP_MODULE_DIR "${CMAKE_CURRENT_LIST_DIR}/../thirdparty/MyApp-StopWatch/hardware/mooncake/main")
if(NOT EXISTS "${APP_MODULE_DIR}/apps/app_my_app/app_my_app.cpp")
    message(FATAL_ERROR "MyApp-StopWatch submodule is missing. Run: git submodule update --init --recursive")
endif()
list(APPEND MY_SRCS
    "${APP_MODULE_DIR}/apps/app_my_app/app_my_app.cpp"
    "${APP_MODULE_DIR}/apps/app_my_app/view/view.cpp"
    "${APP_MODULE_DIR}/assets/images/icon_my_app.c"
)
list(APPEND MY_EXTRA_INCS
    "${APP_MODULE_DIR}/apps"
)
```

Then:

- Add `#include "app_my_app/app_my_app.h"` to `main/apps/apps.h`.
- Add `GetMooncake().installApp(std::make_unique<AppMyApp>());` to `main/main.cpp`.
- Add `LV_IMG_DECLARE(icon_my_app);` to `main/assets/assets.h`.

## Git Order

When the app repo is a submodule:

1. Commit and push the app repo.
2. `git add thirdparty/<App>-StopWatch` in host repo.
3. Commit and push the host repo submodule pointer.
