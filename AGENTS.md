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

## Submodule pointer convention

Pointers in this repo always track the HEAD of the named branch.
To advance a pointer after pushing to a submodule:

```sh
git submodule update --remote <path>
git add <path>
git commit -m "Sync submodules: <summary>"
git push
```
