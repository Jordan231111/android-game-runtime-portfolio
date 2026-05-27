<div align="center">

# Android Game Runtime Systems Portfolio

**Below-the-editor Android engineering — ARM64 patching, LSPosed/Xposed modules, IL2CPP analysis, engine-neutral runtime instrumentation, and the verification scaffolding that keeps it honest.**

<p>
  <img src="https://img.shields.io/badge/Focus-Native_Android_Runtime-111827?style=flat-square" />
  <img src="https://img.shields.io/badge/Experience-5+_years-2EA043?style=flat-square" />
  <img src="https://img.shields.io/badge/Shipped_★-100+-FFD33D?style=flat-square" />
  <img src="https://img.shields.io/badge/Tier-Engine_Internals-6D28D9?style=flat-square" />
</p>

</div>

> Curated index of my native-Android, ARM64, and runtime-instrumentation work. The systems most
> relevant to game-engine, simulation, automation, and AI-research roles are listed here so a
> reviewer doesn't have to dig through the rest of my GitHub.

---

## At a Glance

| Domain | Concrete Work |
| --- | --- |
| **ARM64 patching** | `/proc/self/maps` parser · IDA-style signature scan · page-permission-aware writes · 16-byte `ldr x17, #8 / br x17 / <addr>` absolute-branch trampoline · ELF symbol-table fallback when `dlsym` returns null |
| **Houdini / native-bridge** | Alias-aware writes for `arm64-v8a` running on x86_64 emulators — every alias for the same file offset is patched, not just the one the verifier reads back from |
| **LSPosed modules** | libxposed API 101 · dual `onPackageLoaded`/`onPackageReady` · process-scope filtering · ShadowHook via `JNI_OnLoad` / `RegisterNatives` · runtime feature registry · overlay UI bound per-feature |
| **Engine detection** | Library-name classification across Unity, Unreal, Cocos2d-x, Godot, Flutter, React Native, and Xamarin via `nativeLibraryDir` + `/proc/self/maps` |
| **Unity IL2CPP** | Metadata-resolved methods and fields against `libil2cpp.so` · fail-closed resolution · device-verified status counters · side-aware managed-type logic |
| **Lua / Cocos2d-x runtime** | `luaL_loadbuffer` boundary inspection · script-load observation · coroutine-yield analysis · side-effect-preserving fast iteration |
| **Emulator tooling** | BlueStacks 5, BlueStacks Air (macOS, SIP-aware), MuMu Player 12 · JSON config patching · `.bak` rollback · registry-based install discovery · dry-run modes |
| **Rust systems** | Cross-platform CI matrix · `cargo audit` + `cargo deny` + SBOM · property tests · atomic DB ops · exit code `2` reserved for "found malware" |

---

## Flagship Work

### **[ae-pcd-stamp-tracer — Engine-Neutral Runtime Instrumentation](https://github.com/Jordan231111/ae-pcd-stamp-tracer-public)** ★ Flagship

`C++` · `Java` · `Lua` · `Python` · `NDK` · **~10K+ LOC · 5 years**

The **applied layer** of my native-Android research trajectory: an engine-neutral runtime
instrumentation system that proves the reusable foundations work at scale against a real
Cocos2d-x / Lua native-heavy Android runtime. Verification-first design — every feature ships
with a toggle, status counter, log line, and safe disabled state.

The full technical case study covers:
- **7 specific engineering problems solved** with their solutions (engine routing without source,
  stable observation points over fixed RVAs, Houdini alias-aware patching, JNI symbol-table
  stealth, fail-closed verification, side-effect-preserving fast iteration, process-scope
  filtering)
- Architecture lineage showing how `lsposed-universal-template` and
  `arm64-houdini-lsposed-framework` feed into the application layer
- Engineering principles ("boundaries over addresses," "verification is part of the feature")
- Mermaid system-architecture diagram + module breakdown

The private repository is target-specific; live walk-through available on request.

**→ Read the full case study: [ae-pcd-stamp-tracer-public](https://github.com/Jordan231111/ae-pcd-stamp-tracer-public)**

---

## Native Instrumentation Foundations

<table>
<tr><td width="100%">

#### **[arm64-houdini-lsposed-framework](https://github.com/Jordan231111/arm64-houdini-lsposed-framework)**

`C++` · `Java` · `Android NDK` · `ARM64`

Self-contained ARM64 patching framework with **no third-party inline-hook dependency** — keeps
working on Houdini/native-bridge emulators where general libraries refuse to bind their ABI.

- `/proc/self/maps` parser with module range / base helpers
- IDA-style ARM64 pattern parser and scanner
- File-backed code reading to verify original ARM64 bytes from mapped APK/SO pages
- Houdini alias discovery and alias-aware writes
- `dlopen` / `dlsym` / `RTLD_DEFAULT` / manual ELF symbol-table fallback
- 16-byte ARM64 absolute branch patch primitive
- Patch records and framework status returned through JNI to a Java overlay
- Bounded waiting for game libraries that load after `Application.attach`

The default framework installs no target-specific hooks — that's intentional. The reusable part is
the loader, scanner, verifier, and patcher.

</td></tr>
<tr><td>

#### **[lsposed-universal-template](https://github.com/Jordan231111/lsposed-universal-template)**

`Java` · `Kotlin DSL` · `NDK` · `ShadowHook`

Quick-start LSPosed module scaffold on the modern **libxposed API 101**. Process scope defaults
skip push, crash, sandbox, and anti-cheat-satellite processes unless explicitly opted in.

- `EngineDetector` classifies Unity / Unreal / Cocos2d-x / Godot / Flutter / React Native / Xamarin
  from `nativeLibraryDir` first (no `/proc` IO) with `/proc/self/maps` fallback
- `FeatureRegistry` bool/float runtime flags with overlay-bound toggles and persistence
- ShadowHook registered via `JNI_OnLoad` + `RegisterNatives` so the `.so` symbol table doesn't
  advertise package-derived JNI export names
- R8 obfuscates everything except the LSPosed entry point
- `configure-template.py` renames package, scope, metadata, and packaged `.so` name in one command

</td></tr>
<tr><td>

#### **[Archero-LSPOSED-Mod](https://github.com/Jordan231111/Archero-LSPOSED-Mod)**

`C++` · `Java` · `Android NDK` · `Unity IL2CPP`

Applied Unity IL2CPP runtime-hook study against a live `arm64-v8a` target build (Archero v7.9.1).
Demonstrates the IL2CPP side of the same engineering trajectory the Cocos2d-x case study
demonstrates for the script-engine side.

- Metadata-driven method and field resolution against `libil2cpp.so` (no fixed-RVA fallback at
  install time — fail-closed if a symbol can't be resolved)
- Side-aware logic in the IL2CPP managed-type system (`EntityBase.AddSkill` / `ContainsSkill`
  resolution by metadata)
- Per-feature toggle architecture with named status counters
- Native trampoline placement; through-wall field discovery on hero bullet collision handlers
- Authorized testing on owned devices only

</td></tr>
</table>

---

## Rust Systems

<table>
<tr><td>

#### **[MalwareMinimizer ★5](https://github.com/Jordan231111/MalwareMinimizer)**

`Rust 2024` · `clap` · `proptest` · `criterion` · `wiremock`

Cross-platform malware-scanning CLI. **321 commits, 139 PRs, 110 issues** — review-driven
development, not a one-off demo. Strongest pure-engineering signal in the portfolio outside the
native-Android stack.

- **CI matrix:** Linux / macOS / Windows × x86_64 / ARM64 with pinned action SHAs
- **Supply chain:** `cargo audit`, `cargo deny`, `cargo supply-chain`, SBOM generation,
  `unknown-registry = "deny"`, `unknown-git = "deny"`
- **Correctness:** atomic database writes with rollback, quarantine-path validation with
  restrictive permissions, weekly signature-publish workflow with checksummed update artifacts
- **Testing:** property tests via proptest, criterion benchmarks, wiremock-backed HTTP tests,
  autograder-style CLI tests for new contributors
- **CLI contract:** exit code `2` reserved for "found malware" so shells can distinguish detection
  from runtime failure

</td></tr>
</table>

---

## Emulator and Device Tooling

<table>
<tr><td>

#### **[BlueStacksRoot ★64](https://github.com/Jordan231111/BluestacksRoot)**

`Batch` · `C++` · `Python` · `GitHub Actions`

One of the more widely used BlueStacks 5 rooting toolchains. Native Magisk component, semantic +
dynamic integrity-check bypass in Python, full CI release pipeline.

</td></tr>
<tr><td>

#### **[mumu-magisk-1click ★39](https://github.com/Jordan231111/mumu-magisk-1click)**

`PowerShell` · `Batch`

MuMu Player 12 root setup. 60KB PowerShell helper that locates installs via the Windows uninstall
registry (not hard-coded paths), patches `customer_config.json` / `vm_config.json` /
`shell_config.json` via real JSON parsing, writes `.bak` files before first mutation, supports
`--dry-run` and `--edition global|chinese|all`. CI diffs the bundled installer against MuMu's
official download API and opens an update PR when it changes.

</td></tr>
<tr><td>

#### **[bluestacks-air-oneclick-root](https://github.com/Jordan231111/bluestacks-air-oneclick-root)**

`Bash` · `macOS`

BlueStacks Air on macOS. **SIP-aware:** separate code paths for SIP-enabled (prints the patched
file path for the user to copy) and SIP-disabled (fully automatic). Single curl-piped installer.

</td></tr>
</table>

This tooling layer is the reason the engineering above can be developed quickly: repeatable,
reset-able test environments across BlueStacks (Windows + macOS) and MuMu, rooted, with Magisk
present, without manual steps that drift between runs.

---

## Why This Is Relevant for Game and AI-Game Research

AI work that uses real games as environments is usually bottlenecked **below the editor layer**:

- **Extracting runtime state** from a process that doesn't expose an API
- **Driving controlled actions** against a game loop that wasn't designed for automation
- **Accelerating simulation loops** without invalidating the state they produce
- **Verifying** that side effects still fire in the intended order after every change

That's the surface my work has been operating on for five years — native hooks, engine internals,
ARM64 patching, runtime instrumentation, and the verification scaffolding (status counters, logs,
fail-closed defaults) that keeps automated runs trustworthy enough to ship.

---

## About

| | |
| --- | --- |
| **Current role** | AI Model Evaluation Contractor — Handshake AI |
| **Education** | Computer Science + Mathematics, Rensselaer Polytechnic Institute |
| **LinkedIn** | [jordan-ye-100b86237](https://www.linkedin.com/in/jordan-ye-100b86237/) |
| **GitHub** | [Jordan231111](https://github.com/Jordan231111) |
| **Contact** | yejordan8888@gmail.com |
