---
name: game-godot
version: 1.3.1
description: "Develop, test, build, and deploy Godot 4.x games. Use when writing GdUnit4 GDScript unit tests, PlayGodot E2E automation, exporting web/desktop builds, or wiring CI/CD pipelines. Trigger keywords: Godot, GDScript, GdUnit4, PlayGodot, game export, web build, itch.io, Vercel deploy."
risk: safe
source: openrouter-deepsearch
date_added: 2026-06-16
---

# Godot 4.x Game Development, Testing, Build & Deploy

Develop, test, build, and deploy Godot 4.3+ (LTS) games with GdUnit4 for GDScript unit/component tests and PlayGodot v3+ for E2E game automation.

## When to Use

- Developing, testing, building, or deploying a Godot **4.3+** game (LTS release)
- Writing GDScript **unit/component tests** with GdUnit4 (runs headless inside Godot)
- Writing **E2E / integration / automated-gameplay tests** with PlayGodot v3+ (external control, like Playwright for games)
- Simulating mouse, keyboard, touch, or input-action events in scene tests
- Exporting **web or desktop** builds from the command line using Godot 4.3+ export templates
- Wiring Godot tests and exports into a **CI/CD** pipeline (GitHub Actions, GitLab CI)
- Deploying a web build to **Vercel, GitHub Pages, Netlify, or itch.io** with modern compression (Brotli)

### Do Not Use

- **Godot 3.x projects** — the GdUnit4 base classes, scene-runner API, and export flags differ; use the 3.x docs and `gut`/GdUnit3 instead
- **Non-Godot engines** (Unity, Unreal, web canvas frameworks) — none of these tools or CLI invocations apply
- **PlayGodot without v3+** — Earlier versions lack async/await support and modern Python type hints
- **Pure asset/art pipelines** with no code under test — there is nothing here to assert against
- **General (non-game) Python testing** — use plain `pytest`/`unittest`; PlayGodot only adds value when driving a live Godot game
- **Manual play-test or visual-only QA** — this skill is for *automated* tests, exports, and deploys
- **Unencrypted network deployments** — Web exports must use HTTPS and Content Security Policy (CSP) headers

## Prerequisites

- **Godot 4.3+** (LTS release) installed and on PATH. Verify: `godot --version`
- **Python 3.10+** for PlayGodot. Verify: `python --version`
- **Git** for cloning GdUnit4 addon. Verify: `git --version`
- **Brotli** compressor for web export optimization. Verify: `brotli --version`
- **Export templates** installed for Godot 4.3+ (via Godot Editor → Editor → Manage Export Templates, or CI setup action)
- **Windows host (PowerShell)** is the primary environment. Use PowerShell-compatible commands. On Linux/macOS, adapt path separators and shell commands accordingly.

### Reference Files

Load these reference files from the skill directory when you need deeper context:

- `references/gdunit4-api.md` — Load when writing GdUnit4 test suites, assertions, or scene runner input simulations
- `references/playgodot-api.md` — Load when writing PlayGodot E2E tests, mocking methods, or doing visual regression
- `references/export-presets.md` — Load when configuring `export_presets.cfg` for web/desktop exports
- `references/ci-templates.md` — Load when setting up GitHub Actions or GitLab CI pipelines

## Procedure

### Step 1: Pick the Test Layer

| | GdUnit4 v1.2+ | PlayGodot v3+ |
|---|---------|-----------|
| Type | Unit testing | Game automation |
| Language | GDScript | Python (async/await) |
| Runs | Inside Godot | External (like Playwright) |
| Requires | Godot 4.3+ | Godot 4.3+ RemoteDebugger |
| Best for | Unit/component tests | E2E/integration tests |

Use **GdUnit4** for fast GDScript unit/component/scene tests. Add **PlayGodot v3+** for full E2E automation through the native RemoteDebugger protocol.

### Step 2: Install GdUnit4

```powershell
# Clone GdUnit4 addon (PowerShell)
git clone --depth 1 -b v1.2 https://github.com/MikeSchulze/gdUnit4.git addons/gdUnit4
```

Then enable the plugin: **Project Settings → Plugins → GdUnit4 → Enable**

Expected project structure:

```
project/
├── addons/gdUnit4/          # GdUnit4 addon
├── test/                    # Test directory
│   ├── game_test.gd
│   └── player_test.gd
└── scripts/
    └── game.gd
```

### Step 3: Write GdUnit4 Unit Tests

Create test suites extending `GdUnitTestSuite`. Always free nodes with `auto_free()` to prevent memory leaks.

```gdscript
# test/game_test.gd
extends GdUnitTestSuite

@warning_ignore("unused_parameter")
var game: Node

func before_test() -> void:
    game = auto_free(load("res://scripts/game.gd").new())

func test_initial_state() -> void:
    assert_that(game.is_game_active()).is_true()
    assert_that(game.get_current_player()).is_equal("X")

@warning_ignore("return_value_discarded")
func test_make_move() -> void:
    var success := game.make_move(4)
    assert_that(success).is_true()
    assert_that(game.get_board_state()[4]).is_equal("X")
```

### Step 4: Write GdUnit4 Scene Tests with Input Simulation

Use `scene_runner(...)` to load scenes and simulate input. Always call `await_input_processed` after simulating input.

```gdscript
# test/game_scene_test.gd
extends GdUnitTestSuite

var runner: GdUnitSceneRunner

func before_test() -> void:
    runner = scene_runner("res://scenes/main.tscn")

func after_test() -> void:
    runner.queue_free()

func test_click_cell() -> void:
    await runner.await_idle_frame()

    var cell := runner.find_child("Cell4") as Control
    runner.set_mouse_position(cell.global_position + cell.size / 2)
    runner.simulate_mouse_button_pressed(MOUSE_BUTTON_LEFT)
    await runner.await_input_processed()

    var game := runner.scene()
    assert_that(game.get_board_state()[4]).is_equal("X")

func test_keyboard_restart() -> void:
    runner.simulate_key_pressed(KEY_R)
    await runner.await_input_processed()
    assert_that(runner.scene().is_game_active()).is_true()
```

#### GdUnit4 Assertion Quick Reference

```gdscript
# Null checks
assert_that(value).is_null()
assert_that(value).is_not_null()

# Type checks
assert_that(node).is_instanceof(CharacterBody2D)

# Signal verification
await assert_signal(player).is_emitted("died")
await assert_signal(ui).is_not_emitted("popup_closed", timeout_ms=1000)

# Collection assertions
assert_that([1, 2, 3]).contains_exactly([1, 2, 3]).in_order()
assert_that({"a": 1}).has_key("a")
```

### Step 5: Run GdUnit4 Tests Headless

```powershell
# All tests with JUnit reports (PowerShell)
godot --headless --path . -s res://addons/gdUnit4/bin/GdUnitCmdTool.gd --run-tests --report-format junit --report-directory ./reports

# Specific test suite
godot --headless --path . -s res://addons/gdUnit4/bin/GdUnitCmdTool.gd --run-tests --add res://test/player_test.gd
```

### Step 6: Set Up PlayGodot v3+

```powershell
# Install PlayGodot and pytest-asyncio (PowerShell)
python -m pip install --upgrade "playgodot>=3.0.0" pytest-asyncio

# Verify installation
python -c "import playgodot; print(playgodot.__version__)"  # Should show 3.x
```

Create a `conftest.py` fixture:

```python
import pytest_asyncio
from playgodot import Godot, GodotConfig

@pytest_asyncio.fixture
async def game():
    config = GodotConfig(
        project_path=".",
        headless=True,
        timeout=10.0,
        extra_args=["--disable-gpu"],  # Required for CI environments
    )
    async with Godot.launch(config) as g:
        await g.wait_for_node("/root/Main")
        yield g
```

### Step 7: Write PlayGodot E2E Tests

```python
import pytest
from playgodot import Key

@pytest.mark.asyncio
async def test_complex_interaction(game):
    # Type into a LineEdit
    await game.focus("/root/Main/UI/NameInput")
    await game.type_text("Player1")
    
    # Verify UI state
    name = await game.get_property("/root/Main/UI/NameInput", "text")
    assert name == "Player1"
    
    # Press configured action
    await game.press_action("ui_accept")
    
    # Wait for scene change
    await game.wait_for_node("/root/Game", timeout=5.0)
```

#### Advanced PlayGodot Features

```python
# Mocking engine methods
await game.mock_method(
    "/root/Game/Network",
    "is_online",
    return_value=False
)

# Performance testing
with game.benchmark("load_scene"):
    await game.change_scene("res://levels/boss.tscn")

# Visual regression
await game.assert_screenshot(
    "title_screen.png",
    threshold=0.98,  # 98% similarity
    mask=["/root/Main/UI/VersionLabel"]  # Ignore dynamic elements
)
```

### Step 8: Run PlayGodot Tests

```powershell
# Run all PlayGodot tests (PowerShell)
pytest tests/ -v
```

### Step 9: Export Web Build with Brotli Compression

Configure `export_presets.cfg` with CSP enabled:

```ini
[preset.0]
name="Web"
platform="Web"
runnable=true
export_path="build/index.html"

[preset.0.options]
vram_texture_compression/for_mobile=false
html/export_icon="res://icon.png"
html/custom_html_shell="res://export-templates/web/shell.html"
html/security/csp_enabled=true
html/security/csp="default-src 'self' 'unsafe-eval' 'wasm-unsafe-eval'"
```

Export and compress:

```powershell
# Export web build (PowerShell)
godot --headless --export-release "Web" ./build/index.html

# Compress with Brotli (PowerShell — requires brotli on PATH)
# On Windows, use: Get-ChildItem -Path ./build -Recurse -Include *.wasm,*.js,*.packed | ForEach-Object { brotli -k $_.FullName }
# On Linux/macOS:
# find ./build -type f \( -name "*.wasm" -o -name "*.js" -o -name "*.packed" \) -exec brotli -k {} \;
```

### Step 10: Deploy

```powershell
# Deploy to Vercel with security headers (PowerShell)
vercel deploy ./build --prod --csp
```

For GitHub Pages, itch.io, or Netlify, upload the contents of `./build/` ensuring HTTPS and CSP headers are configured at the hosting layer.

### Step 11: Automate in CI/CD

```yaml
name: Godot CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up Godot 4.3
        uses: chickensoft-games/setup-godot@v3
        with:
          version: 4.3.0
          export-templates: true
          
      - name: Run GdUnit4 tests
        run: |
          godot --headless --path . \
            -s res://addons/gdUnit4/bin/GdUnitCmdTool.gd \
            --run-tests --report-format junit --report-directory ./reports
            
      - name: Run PlayGodot tests
        run: |
          python -m pip install playgodot pytest-asyncio
          pytest tests/ -v
          
      - name: Upload test results
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: |
            reports/
            test-results.xml
```

## Pitfalls

1. **Godot 3.x incompatibility** — GdUnit4 base classes, scene-runner API, and export flags differ from 3.x. Do not attempt to use this skill with Godot 3.x projects. Use `gut`/GdUnit3 for 3.x.
2. **PlayGodot version < 3** — Earlier versions lack async/await support and modern Python type hints. Always pin `playgodot>=3.0.0`.
3. **Memory leaks from unfreed nodes** — Always use `auto_free()` for nodes created in GdUnit4 tests and `queue_free()` for scene runners in `after_test()`.
4. **Missing `await_input_processed`** — Input simulations without awaiting processing will produce flaky tests. Always call `await runner.await_input_processed()` after `simulate_mouse_button_pressed`, `simulate_key_pressed`, or similar.
5. **Missing export templates** — `--export-release` will fail silently or produce broken builds if export templates are not installed. Verify templates before exporting.
6. **No CSP on web exports** — Web exports without CSP headers are vulnerable to injection attacks. Always set `html/security/csp_enabled=true` in `export_presets.cfg`.
7. **No HTTPS on deployment** — Web exports must use HTTPS. Brotli-compressed `.wasm` files require proper MIME types and HTTPS to load in browsers.
8. **`--disable-gpu` missing in CI** — PlayGodot tests in headless CI environments require `extra_args=["--disable-gpu"]` to avoid GPU initialization failures.
9. **Brotli not on PATH (Windows)** — On Windows, `brotli` may not be available. Install it via `choco install brotli` or use `Get-ChildItem | ForEach-Object { brotli -k $_.FullName }` pattern.
10. **Deprecated APIs** — Do not mix GdUnit3 or PlayGodot <3 APIs. They will fail at runtime with import or method errors.
11. **PowerShell path separators** — Godot uses `res://` paths internally regardless of OS. For filesystem paths in PowerShell, use `./` (forward slashes work in PowerShell).

## Verification

Confirm each item before merging or shipping. Run these checks:

```powershell
# 1. Verify Godot version is 4.3+
godot --version
# Expected output: 4.3.x or higher

# 2. Run GdUnit4 tests headless
godot --headless --path . -s res://addons/gdUnit4/bin/GdUnitCmdTool.gd --run-tests --report-format junit --report-directory ./reports
# Expected: All tests pass, JUnit XML generated in ./reports/

# 3. Run PlayGodot tests
pytest tests/ -v
# Expected: All tests pass with exit code 0

# 4. Verify PlayGodot version
python -c "import playgodot; print(playgodot.__version__)"
# Expected: 3.x

# 5. Verify web export exists and is Brotli-compressed
# PowerShell:
Get-ChildItem -Path ./build -Recurse | Select-Object Name, Length
# Expected: index.html, index.wasm, index.js, index.wasm.br (if Brotli applied)

# 6. Verify CSP is enabled in export_presets.cfg
Select-String -Path ./export_presets.cfg -Pattern "csp_enabled=true"
# Expected: Match found
```

Checklist:

- [ ] GdUnit4 tests pass in headless mode (`--headless --run-tests`)
- [ ] PlayGodot tests pass with `pytest tests/ -v`
- [ ] All test resources are freed (`auto_free()`/`queue_free()`)
- [ ] Input simulations await processing (`await_input_processed`)
- [ ] Web export includes Brotli-compressed assets
- [ ] CSP headers are enabled in `export_presets.cfg`
- [ ] CI pipeline runs both test suites on all platforms
- [ ] Deployment includes HTTPS and security headers
- [ ] No deprecated APIs (GdUnit3, PlayGodot<3) are used

## Examples

### Quick Reference Commands

```powershell
# GdUnit4 - Unit testing framework (GDScript, runs inside Godot)
godot --headless --path . -s res://addons/gdUnit4/bin/GdUnitCmdTool.gd --run-tests

# PlayGodot v3+ - Game automation framework (Python, like Playwright for games)
pip install "playgodot>=3.0.0"
pytest tests/ -v

# Export web build with Brotli compression
godot --headless --export-release "Web" ./build/index.html
brotli -k ./build/index.wasm

# Deploy to Vercel with security headers
vercel deploy ./build --prod --csp
```

## Related Skills

- `game-unity` — Unity game development, testing, and deployment
- `game-unreal` — Unreal Engine development and automation
- `ci-github-actions` — General GitHub Actions workflow patterns
- `deploy-vercel` — Vercel deployment patterns and configuration

## References

- [GdUnit4 Documentation](https://mikeschulze.github.io/gdUnit4/)
- [PlayGodot v3 API Reference](https://randroids-dojo.github.io/PlayGodot/)
- [Godot 4.3 Export Docs](https://docs.godotengine.org/en/4.3/tutorials/export/)
- [Web Security Best Practices](https://owasp.org/www-project-secure-headers/)
