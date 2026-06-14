# Mooncake App Pattern

Use this reference when implementing the hardware side of a StopWatch app.

## App Class

The app class should:

- Derive from `mooncake::AppAbility`.
- Set `setAppInfo().name` and `setAppInfo().icon` in the constructor.
- Create `input::KeyManager` in `onOpen()`.
- Create the view under `LvglLockGuard`.
- Call `GetHAL().updateButtonStates()` in `onRunning()`.
- Close on `input::KeyEvent::GoHome`.
- Reset owned resources under `LvglLockGuard` in `onClose()`.

Example skeleton:

```cpp
class AppMyApp : public mooncake::AppAbility {
public:
    AppMyApp();
    void onCreate() override;
    void onOpen() override;
    void onRunning() override;
    void onClose() override;

private:
    std::unique_ptr<view::MyAppView> _view;
    std::unique_ptr<input::KeyManager> _key_manager;
};
```

## View Class

Keep app-specific UI and state in `view::MyAppView`.

Common methods:

```cpp
void init(lv_obj_t* parent);
void update();
void handleLeftKey();
void handleRightKey();
```

For pointer-heavy interactions, poll `GetHAL().lvTouchpad` and compute hit areas from logical 466x466 coordinates.

## Input Mapping

Use `KeyManager` events for menu-like apps:

- `GoHome`: A+B long press, return to Launcher.
- `GoPrevious`: left key.
- `GoNext`: right key.

Use raw button state for continuous or press-edge interactions:

```cpp
const bool both = GetHAL().btnA.isPressed() && GetHAL().btnB.isPressed();
const bool left = !both && GetHAL().btnA.wasPressed();
const bool right = !both && GetHAL().btnB.wasPressed();
```

## Feedback Cleanup

If the app starts vibration or audio, stop it on close:

```cpp
GetHAL().stopVibrate();
std::vector<int16_t> empty_audio;
GetHAL().audioPlay(empty_audio, true);
```

## LVGL Performance

- Avoid redrawing complex vector geometry every frame on ESP32.
- Prefer runtime image/canvas caches, small invalidation areas, or static labels.
- Guard large allocations; C++ exceptions are disabled in the current firmware.
- Delete LVGL image objects before freeing runtime image buffers they reference.
- Keep background FreeRTOS tasks synchronized with atomic flags or stop them before freeing shared memory.
