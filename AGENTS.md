# multiplayer-fabric-xr-dev

Focused monorepo for the XR interaction system and its Godot engine dependency.

## Submodules

| Path | Branch | Purpose |
|---|---|---|
| `multiplayer-fabric-godot` | `multiplayer-fabric` | Godot engine with OTel, SQLite, lasso, WebTransport modules |
| `multiplayer-fabric-interaction-system` | `main` | XR interaction plugin (lasso, canvas_3d_anchor, action_host) |
| `multiplayer-fabric-interaction-system-project` | `main` | Test/demo Godot project — runs against the engine |

## Setup

```sh
git clone https://github.com/V-Sekai-fire/multiplayer-fabric-xr-dev.git
cd multiplayer-fabric-xr-dev
git submodule update --init --recursive
```

## Build

```sh
# Engine (macOS, dev build with tests)
cd multiplayer-fabric-godot
scons precision=double tests=yes dev_build=yes platform=macos -j$(nproc)
```

## Run unit tests

```sh
cd multiplayer-fabric-godot
bin/godot.macos.editor.dev.double.arm64 --test --test-case="*OTel*"
```

## Run project

```sh
cd multiplayer-fabric-godot
bin/godot.macos.editor.dev.double.arm64 \
    --path ../multiplayer-fabric-interaction-system-project
```

## OpenXR simulator (macOS)

**Install**: `~/Applications/MetaXRSimulator.app` — installed via Meta Quest Developer Hub.

**OpenXR runtime**: `/usr/local/share/openxr/1/active_runtime.json` is a symlink to
`MetaXRSimulator.app/Contents/Resources/MetaXRSimulator/meta_openxr_simulator.json`,
which resolves `./SIMULATOR.so` relative to its own directory. No manual path edits needed.

**First-time import** (populates `.godot/` cache, required for canvas_plane global class):

```sh
bin/godot.macos.editor.dev.double.arm64 \
    --path ../multiplayer-fabric-interaction-system-project \
    --headless --import --quit-after 10
```

**Launch simulator before running the project**:

```sh
open ~/Applications/MetaXRSimulator.app
# wait ~3 s for it to start, then run the project
```

**Known warning**: `MVKBlockObserver` duplicate symbol between the Godot binary and
`SIMULATOR.so` — MoltenVK symbol collision, non-fatal, safe to ignore.

## Submodule pointer convention

Pointers in this repo always track the HEAD of the named branch.
To advance a pointer after pushing to a submodule:

```sh
git submodule update --remote <path>
git add <path>
git commit -m "Sync submodules: <summary>"
git push
```
