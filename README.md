# stopwatch-skill

Reusable StopWatch development skill and HTML prototype framework.

## Contents

- `skill/stopwatch-development/`: Codex skill for M5Stack StopWatch app development.
- `html-framework/`: standalone browser prototype starter.

## Use The Skill

Copy or symlink `skill/stopwatch-development` into a Codex skills directory, or reference it directly in a prompt:

```text
Use $stopwatch-development at /path/to/stopwatch-skill/skill/stopwatch-development to build a new StopWatch app prototype.
```

## Use The HTML Framework

Copy `html-framework/index.html` into a new app repository as `web/index.html`, then edit the constants, drawing, and state machine for the app.

```bash
open html-framework/index.html
```

The framework is intentionally vanilla HTML/CSS/JS and works through `file://`.
