---
name: godot-gdscript-patterns
description: "Master Godot 4 GDScript patterns including signals, scenes, state machines, and optimization. Use when building Godot games, implementing game systems, writing GDScript, or refactoring Godot 4.x projects."
version: 1.0.1
risk: safe
source: community
date_added: "2026-02-27"
---

# Godot GDScript Patterns

Production patterns for Godot 4.x game development with GDScript, covering architecture, signals, scenes, state machines, and performance optimization.

## When to Use

- Building or refactoring games with Godot 4.x
- Implementing game systems in GDScript (inventory, combat, dialogue, AI)
- Designing scene architecture and node composition
- Managing game state (player progress, UI flow, level transitions)
- Optimizing GDScript performance (hot loops, allocation, signal traffic)
- Learning or enforcing Godot 4 best practices

## Do Not Use When

- The task targets Godot 3.x (API differs significantly: `yield`, `connect` signatures, `export` vs `@export`)
- The task is C# / GDExtension / C++ in Godot
- The task is unrelated to Godot or GDScript

## Prerequisites

- Godot 4.x installed and runnable on Windows host
- PowerShell as primary shell (Windows paths below use backslash form)
- Project root contains `project.godot`
- GDScript files use `.gd` extension

## Procedure

1. Clarify the goal, constraints, and required inputs before writing code.
2. Identify the pattern category: signal wiring, scene composition, state machine, singleton/autoload, resource management, or optimization.
3. Apply the matching pattern below; load `resources/implementation-playbook.md` when you need full copy-pasteable examples or multi-file scaffolds.
4. Validate the result by running the project or the relevant scene.

### Core Patterns

#### Signals (decoupled communication)
- Declare with `signal name_changed(new_name: String)`.
- Connect in `_ready()` using `node.signal_name.connect(callable)` (Godot 4 syntax).
- Prefer `@onready var` references over `get_node()` strings where possible.
- Emit with `name_changed.emit(value)`.

#### Scene composition
- One responsibility per scene; compose via instancing.
- Use `@export` for configurable dependencies exposed in the inspector.
- Prefer typed nodes: `@onready var player: CharacterBody2D = $Player`.

#### State machines
- Use a `State` base class with `enter()`, `exit()`, `process(delta)`, `physics_process(delta)`.
- Keep a `StateMachine` node that owns the current `State` and forwards callbacks.
- Avoid hard transitions; let states request transitions via the owner.

#### Autoloads / singletons
- Register in Project Settings > Autoload; access globally by class name.
- Keep autoloads small and signal-driven; do not store per-instance data in autoloads.

#### Resources for data
- Use custom `Resource` subclasses for shared data (item definitions, stats).
- `.tres` files are data; `.gd` is logic.

### Optimization Checklist

- Avoid `get_node()` in hot loops; cache node references.
- Prefer `@onready` and typed variables.
- Minimize per-frame `print()` and string formatting.
- Use `call_deferred()` when modifying the tree during physics callbacks.
- Profile with the built-in Debugger > Profiler before optimizing.

## Examples

### Signal declaration and connection (Godot 4)

```gdscript
# player.gd
class_name Player
extends CharacterBody2D

signal health_changed(new_health: int, max_health: int)

@export var max_health: int = 100
var health: int

func _ready() -> void:
    health = max_health

func take_damage(amount: int) -> void:
    health = max(0, health - amount)
    health_changed.emit(health, max_health)
```

```gdscript
# hud.gd
extends CanvasLayer

@onready var bar: ProgressBar = $HealthBar

func _ready() -> void:
    var player: Player = get_tree().get_first_node_in_group("player")
    player.health_changed.connect(_on_health_changed)

func _on_health_changed(new_health: int, max_health: int) -> void:
    bar.max_value = max_health
    bar.value = new_health
```

### Minimal state machine

```gdscript
# state_machine.gd
class_name StateMachine
extends Node

@export var initial_state: State
var current_state: State

func _ready() -> void:
    current_state = initial_state
    current_state.enter()

func _process(delta: float) -> void:
    current_state.process(delta)

func transition_to(new_state: State) -> void:
    current_state.exit()
    current_state = new_state
    current_state.enter()
```

```gdscript
# state.gd
class_name State
extends Node

func enter() -> void:
    pass

func exit() -> void:
    pass

func process(delta: float) -> void:
    pass
```

## Pitfalls

- **Godot 3 vs 4 syntax**: `connect("sig", self, "method")` is Godot 3; Godot 4 uses `sig.connect(callable)` and `sig.emit(value)`. Do not mix.
- **String `get_node` paths break silently** when nodes are renamed or reparented; use `@onready` typed references.
- **Modifying the scene tree during physics** causes errors; use `call_deferred()`.
- **Autoloads holding per-instance state** cause cross-level leaks; reset on scene change.
- **`@onready` runs after `_init`** but before `_ready`; do not depend on it in `_init`.
- **Circular references between autoloads** can deadlock initialization order; reorder in Project Settings > Autoload.
- **Typed arrays (`Array[Node]`) have runtime cost** in very hot loops; benchmark before assuming they are free.
- **`print()` in `_process`** tanks frame rate; guard with a flag or remove before shipping.

## Verification

1. Open the project in Godot 4.x on Windows:
   ```powershell
   & "C:\Program Files\Godot\Godot.exe" --path . --editor
   ```
2. Run the main scene and confirm no errors in the Output dock:
   ```powershell
   & "C:\Program Files\Godot\Godot.exe" --path .
   ```
3. For a specific scene:
   ```powershell
   & "C:\Program Files\Godot\Godot.exe" --path . res://scenes/player.tscn
   ```
4. Check signal connections: in the editor, select the emitting node and inspect Node > Signals; connected targets should appear.
5. Profile hot paths: Debugger > Profiler > Start; confirm no single function dominates frame time unexpectedly.
6. Run GDScript lint via editor: Project > Tools > GDScript Lint (if enabled); fix reported warnings.

## Related Skills

- `godot-scene-architecture` (if present) for deeper node composition guidance.
- `godot-performance` for profiling and rendering optimization beyond GDScript.

## Resources

- `resources/implementation-playbook.md` — load this when you need full multi-file scaffolds, advanced patterns (object pooling, event buses, save systems), or step-by-step refactor recipes. Do not load for quick single-pattern answers; the sections above suffice.
