# Objective: Interaction System — Done Condition

## Goal

Get the `multiplayer-fabric-interaction-system` addon fully working inside
`multiplayer-fabric-interaction-system-project` running on the Meta XR Simulator.

When this is done, the segment is complete.

## What "fully working" means

A VR controller raycast (or desktop mouse ray) enters the scene, hits a
`CanvasPlane` in 3D space, passes through the Lasso database (`lassodb.gd`)
to find the closest point of interest, and fires `call_gui_input` on the
correct `Control` node — so that a `Button` press, `HSlider` drag, or other
Godot UI interaction responds as if the user clicked it with a mouse.

### Pass condition (all rows GREEN in `test_interaction_ui.gd`)

| Test row | What it checks |
|---|---|
| `lasso_query_returns_poi` | `LassoDB.query()` returns the nearest registered POI given a ray transform |
| `call_gui_input_fires` | `InputEventMouseButton` is synthesised and delivered to the target `Control` |
| `btn_pressed_fires` | The `Button.pressed` signal emits when the interaction hits it |
| `virtual_input_fires` | Virtual input path (XR controller pose → raycast) triggers `gui_input` |
| `slider_value_changes` | `HSlider` value changes when the interaction drags across it |

Running the project in the Meta XR Simulator produces no `SCRIPT ERROR` lines
and the test panel shows all PASS rows.

## Current blockers

1. **`.godot/` cache missing** — `Canvas3D` class name is not in the global
   class database so `canvas_plane.gd` fails to resolve when Godot starts
   without an editor import pass. Fix: run `godot --import` or open the project
   in the editor once to generate `.godot/global_script_class_cache.cfg`.

2. **`canvas_plane` parse cascade** — `interaction_manager.gd` preloads
   `canvas_plane.gd` which extends `Canvas3D`. Until blocker 1 is resolved
   every script that touches `canvas_plane_class` fails to parse, so no scene
   runs.

3. **`direct_interaction_action.gd` stub** — `controller_actions/direct_interaction_action.gd`
   exists but the `call_gui_input` routing to the winning POI's `Control` is
   not yet wired end-to-end.

## Stack

| Layer | Component |
|---|---|
| Godot engine | `opentelemetry-godot` — `feat/module-sqlite` branch |
| XR runtime | Meta XR Simulator v85 (activated at `/usr/local/share/openxr/1/active_runtime.json`) |
| Addon under test | `multiplayer-fabric-interaction-system` → `addons/interaction_system` |
| Canvas UI bridge | `canvas_plane` addon → `addons/canvas_plane` |
| Spatial query | `lassodb.gd` — angular + distance scoring, snap hysteresis |
| Test scene | `multiplayer-fabric-interaction-system-project/test_main.tscn` |

## Definition of done

```
pgrep MetaXRSimulator                        # simulator running
godot --path multiplayer-fabric-interaction-system-project
# → no SCRIPT ERROR in stderr
# → test panel: all rows show PASS
# → Meta XR Simulator viewport renders the scene
```

No canvas_plane parse errors. No unresolved class names. Button, slider, and
label all respond to the simulated VR controller ray.
