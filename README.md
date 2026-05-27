# Android Game Runtime Systems Portfolio

A curated index of my native-Android, ARM64, and runtime-instrumentation work. The systems most
relevant to game-engine, simulation, automation, and AI-research roles are listed here so a reviewer
does not need to dig through the rest of my GitHub.

## What This Work Covers

- **ARM64 patching primitives.** `/proc/self/maps` parsing, IDA-style signature scan, page-permission-aware
  read/write, 16-byte `ldr x17, #8 / br x17 / <addr>` absolute-branch trampoline, ELF symbol-table
  fallback for symbols not exposed through `dlsym`.
- **Houdini / native-bridge correctness.** On x86_64 Android emulators with Houdini, the `arm64-v8a`
  library loads as guest ARM64 with multiple aliased mappings for the same file offset. The framework
  treats writes as alias-aware — every alias is patched, not just the one the verifier reads from —
  because verifying at one address while gameplay continues to use another mapping is a real failure
  mode that quieter tooling will not catch.
- **LSPosed module scaffolding.** Modern `libxposed` API 101 entry points, `onPackageLoaded` +
  `onPackageReady`, process-level scope filtering, ShadowHook integration, runtime feature registry,
  movable overlay UI bound to feature toggles.
- **Engine classification.** Library-name detection across Unity, Unreal, Cocos2d-x, Godot, Flutter,
  React Native, and Xamarin targets — driven by `/proc/self/maps` and `nativeLibraryDir` scan, with
  evidence reported back to the overlay.
- **Unity IL2CPP analysis.** Metadata-resolved methods and fields against `libil2cpp.so`, fail-closed
  resolution (refuse to install hooks against unresolved symbols rather than blind-patch), and
  device-verified status counters.
- **Emulator and rooted-device tooling.** Reproducible BlueStacks 5, BlueStacks Air (macOS), and MuMu
  Player 12 root setup with Magisk, plus the operational glue (registry detection, JSON config
  patching, `.bak` rollback, dry-run modes) that makes the workflows safe to run on real machines.

## Public Repositories

### ARM64 Houdini LSPosed Framework

**[arm64-houdini-lsposed-framework](https://github.com/Jordan231111/arm64-houdini-lsposed-framework)** · C++ · Java · NDK

Self-contained ARM64 instrumentation framework. No third-party native inline-hook dependency, so it
keeps working in Houdini/native-bridge emulators where general inline-hook libraries refuse to bind
their ABI. Reusable parts:

- `/proc/self/maps` parser with module range and base helpers
- IDA-style ARM64 pattern parser and scanner
- File-backed code reading for verifying original ARM64 bytes from mapped APK/SO pages
- Houdini/native-bridge alias discovery and alias-aware writes
- `dlopen` / `dlsym` / `RTLD_DEFAULT` / manual ELF symbol-table fallback
- 16-byte ARM64 absolute branch patch primitive
- Patch records and framework status returned through JNI to Java overlay
- Bounded waiting for game libraries that load after `Application.attach`

The default framework installs no target-specific hooks — that is intentional. Target branches own
offsets, signatures, object layouts, and replacement functions. The reusable part is the loader,
scanner, verifier, and patcher.

### LSPosed Universal Template

**[lsposed-universal-template](https://github.com/Jordan231111/lsposed-universal-template)** · Java · NDK · ShadowHook

Quick-start LSPosed module scaffold for authorized testing and rapid prototyping. Modern
`libxposed` API 101, both `onPackageLoaded` and `onPackageReady` overridden for broad
LSPosed/Vector compatibility, process-level scope defaults that skip push / crash / sandbox /
anti-cheat-satellite processes unless opted in.

`EngineDetector` classifies Unity, Unreal, Cocos2d-x, Godot, Flutter, React Native, and Xamarin
targets at startup by matching well-known native libraries from `nativeLibraryDir` first
(cheap, no `/proc` IO) and then `/proc/self/maps`. `FeatureRegistry` provides bool/float runtime
flags with overlay-bound toggles and best-effort persistence. ShadowHook is registered via
`JNI_OnLoad` + `RegisterNatives` so the `.so` symbol table does not advertise package-derived JNI
export names. R8 obfuscates everything except the LSPosed entry point.

### ae-pcd-stamp-tracer — Public Case Study

**[ae-pcd-stamp-tracer-public](https://github.com/Jordan231111/ae-pcd-stamp-tracer-public)**

Architecture and engineering write-up for a private engine-neutral instrumentation project that
extended the universal template to a Cocos2d-x / Lua native-heavy Android runtime. Covers
library-load and script-load boundaries as stable observation points, runtime verification as a
first-class design constraint (every feature has a toggle, a status counter, logs, and a safe
disabled state), and the difference between "skip the wait" and "fast-iterate while preserving
every script side effect that mutates state."

The private repository itself is target-specific and is not published as raw code. Live
walk-throughs available on request.

### Emulator and Device Tooling

- **[BluestacksRoot](https://github.com/Jordan231111/BluestacksRoot)** ★64 — BlueStacks 5 root toolchain. Magisk integration, native C++ component, semantic + dynamic integrity-check bypass in Python, GitHub Actions release pipeline.
- **[mumu-magisk-1click](https://github.com/Jordan231111/mumu-magisk-1click)** ★39 — MuMu Player 12 root setup. 60KB PowerShell that locates installs via the Windows uninstall registry (not hard-coded paths), patches `customer_config.json` / `vm_config.json` / `shell_config.json` via real JSON parsing, writes `.bak` files before first mutation, supports `--dry-run` and `--edition global\|chinese\|all`. CI diffs the bundled installer against MuMu's official download API.
- **[bluestacks-air-oneclick-root](https://github.com/Jordan231111/bluestacks-air-oneclick-root)** — BlueStacks Air on macOS. SIP-aware: separate code paths for SIP-enabled (prints the patched file path for the user to copy) and SIP-disabled (fully automatic). Single curl-piped installer.

This tooling layer is the reason the engineering above can be developed quickly: repeatable,
reset-able test environments across BlueStacks (Windows + macOS) and MuMu, rooted, with Magisk
present, without manual steps that drift between runs.

## Why This Is Relevant For Game / AI-Game Research

AI work that uses real games as environments is usually bottlenecked below the editor layer:
extracting runtime state, driving controlled actions, accelerating simulation loops, and verifying
that side effects still fire in the intended order. That is exactly the surface my work has been
operating on for five years — native hooks, engine internals, ARM64 patching, runtime
instrumentation, and the verification scaffolding that keeps automated runs trustworthy.

## Professional Summary

Five years of independent Android runtime-systems work — Unity IL2CPP analysis, LSPosed/Xposed
module development, native C++ hooks, ARM64 pattern scanning and patching, engine detection,
runtime state inspection, and emulator/root tooling — alongside a clean systems-engineering
signal in Rust (MalwareMinimizer: cross-platform CI, property tests, supply-chain enforcement,
atomic database operations).

Currently: AI Model Evaluation Contractor at Handshake AI.

Computer Science + Mathematics, Rensselaer Polytechnic Institute.
