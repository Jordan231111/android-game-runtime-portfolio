# Android Game Runtime Systems Portfolio

This repository is a public portfolio index for my Android game-runtime and engine-systems work.
It summarizes the parts that are useful for interviews and research roles without publishing
target-specific private code, proprietary assets, or exploit-oriented instructions.

## Focus

- Android game runtime instrumentation
- Unity IL2CPP metadata analysis and native method hooks
- LSPosed / Xposed module development
- ARM64 native-code scanning, patching, and verification
- Emulator and rooted-device automation workflows
- Engine detection across Unity, Unreal, Cocos2d-x, Godot, Flutter, React Native, and Xamarin
- Game-loop, movement, collision, dialogue, and simulation-speed research
- Tooling that can support AI agents, automated testing, and fast simulation iteration

## Public Work Samples

### Archero-LSPOSED-Mod

Repository: https://github.com/Jordan231111/Archero-LSPOSED-Mod

Unity IL2CPP Android runtime project using C++ LSPosed hooks. The implementation demonstrates:

- metadata-based method and field resolution
- runtime hook installation against `libil2cpp.so`
- movement, collision, projectile, and game-speed system analysis
- device-verified status counters and fail-closed hook behavior
- Android NDK, Gradle, CMake, Java, and native C++ integration

### arm64-houdini-lsposed-framework

Repository: https://github.com/Jordan231111/arm64-houdini-lsposed-framework

Reusable LSPosed framework for ARM64 guest-code instrumentation in native-bridge / Houdini
environments. The framework covers:

- `/proc/self/maps` parsing and target module discovery
- IDA-style ARM64 pattern scanning
- file-backed code reads for verifier workflows
- alias-aware writes for native-bridge mappings
- ARM64 absolute branch patch primitives
- feature registry, overlay controls, and native status reporting

### lsposed-universal-template

Repository: https://github.com/Jordan231111/lsposed-universal-template

Reusable Android runtime instrumentation template with:

- modern libxposed API entry points
- engine detection for Unity / Unreal / Cocos2d-x / Godot and other runtimes
- native utilities for module lookup, pattern scan, symbol resolution, memory reads, and guarded writes
- configurable overlay and feature toggles
- Frida-first reconnaissance workflow documentation

### Emulator / Device Tooling

- BlueStacks rooting and setup automation: https://github.com/Jordan231111/BluestacksRoot
- MuMu / Magisk automation: https://github.com/Jordan231111/mumu-magisk-1click
- BlueStacks Air macOS root automation: https://github.com/Jordan231111/bluestacks-air-oneclick-root

These projects show the device and emulator operations side of game-runtime research: setting up
repeatable test environments, rooted workflows, Android file-system access, and automation around
tooling that is otherwise manual and fragile.

## Private Case Study: ae-pcd-stamp-tracer

`ae-pcd-stamp-tracer` is my most advanced private game-runtime research repository. It is not
published as raw code because parts of it are target-specific. I can walk through it live or provide
selected excerpts when appropriate.

The project demonstrates:

- Cocos2d-x + Lua + native Android runtime analysis
- decoded Lua chunk inspection via `luaL_loadbuffer` interception
- engine-neutral feature registry and runtime overlay controls
- native `libapp.so` symbol and pattern research
- dialogue / timer fast-iteration planning that preserves coroutine side effects
- achievement/catalog tracing research
- game-state instrumentation patterns relevant to AI-agent evaluation workflows

The public case study is in [docs/ae-pcd-stamp-tracer-case-study.md](docs/ae-pcd-stamp-tracer-case-study.md).

## Why This Matters For AI Game Research

AI research projects that use games or digital environments often need more than editor-level game
development. They need infrastructure that can:

- read or infer runtime state
- automate controlled actions
- accelerate repetitive simulation loops
- verify behavior inside real runtime environments
- connect model output to game/system feedback
- run consistently across device and emulator setups

My work is strongest at that boundary: Android runtime systems, engine internals, native hooks,
instrumentation, and automation.

## Interview Summary

I have 5+ years of independent Android game modding and runtime systems experience. My work is
mostly below the editor layer: Unity IL2CPP, Cocos2d-x/Lua, LSPosed/Xposed modules, native C++ hooks,
engine detection, runtime state inspection, movement/collision logic, game-loop modification, and
emulator-based testing.

