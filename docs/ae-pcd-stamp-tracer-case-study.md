# ae-pcd-stamp-tracer Case Study

`ae-pcd-stamp-tracer` is a private Android game-runtime research repository focused on instrumenting
a native-heavy mobile game runtime. The public version of this case study intentionally excludes
target-specific instructions and raw implementation details, but captures the engineering surface
that is relevant to game systems and AI research roles.

## Technical Scope

- Android LSPosed module built with Java, C++, CMake, Gradle, and the Android NDK
- Modern libxposed API entry path with scoped process filtering
- Native instrumentation scaffold using ARM64 runtime address resolution
- Engine detection for Unity, Unreal, Cocos2d-x, Godot, Flutter, React Native, and Xamarin
- JNI bridge for native status reporting and runtime feature toggles
- Overlay UI for enabling/disabling instrumentation features during live testing
- Lua source-buffer inspection at load time for Cocos2d-x/Lua style scripting flows
- Native symbol, string, and pattern research against a stripped Android library

## Relevant Engineering Problems

### Runtime State Discovery

The project traces runtime objects and script labels that are not available from a clean public API.
That requires process-map inspection, target-library discovery, conservative memory reads, and careful
logging so observations can be reproduced instead of guessed.

### Script and Coroutine Flow Analysis

The most interesting work is around dialogue and script execution. The project investigates how
native dialogue windows, Lua coroutine yields, text timers, choices, reward dispatch, save calls, and
scene transitions fit together.

The key design constraint is not "skip to the end." It is fast iteration through the same script flow
so state-changing side effects still happen in order.

### Engine-Agnostic Framework Work

The reusable portion is not tied to one engine. It includes engine detection, runtime feature state,
native utilities, Frida-first reconnaissance docs, overlay controls, and Android module packaging.
That makes the work transferable to Unity IL2CPP, Cocos2d-x, Godot, Unreal, and native-heavy Android
apps.

## Relevance To AI Game Research

This kind of work maps directly to AI/game evaluation infrastructure:

- model agents need reliable environment feedback
- automated testing needs reproducible state observation
- simulation loops need controlled speedups
- runtime tools need to preserve game-side side effects
- benchmarks need instrumentation that works inside real app builds, not just toy demos

## What I Can Walk Through

In an interview I can discuss:

- how I identify the active runtime and target process
- how engine detection changes the hook strategy
- how IL2CPP and Cocos2d-x/Lua workflows differ
- how to distinguish safe timer acceleration from unsafe skip-to-end logic
- how to build a feature toggle system for live runtime testing
- how to design fail-closed native hooks and status counters
- how emulator/root setup supports repeatable Android runtime research

