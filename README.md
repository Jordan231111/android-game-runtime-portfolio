<div align="center">

# Android Game Runtime Systems Portfolio

**Below-the-editor Android engineering — ARM64 patching, LSPosed/Xposed modules, IL2CPP analysis, engine-neutral runtime instrumentation, and the verification scaffolding that keeps it honest.**

<p>
  <img src="https://img.shields.io/badge/Focus-Native_Android_Runtime-111827?style=flat-square" />
  <img src="https://img.shields.io/badge/Experience-5+_years-2EA043?style=flat-square" />
</p>

</div>

> Curated index of my native-Android, ARM64, and runtime-instrumentation work. The systems most
> relevant to game-engine, simulation, automation, and AI-research roles are listed here so a
> reviewer doesn't have to dig through the rest of my GitHub.

---

## Flagship Work

### **[ae-pcd-stamp-tracer — Engine-Neutral Runtime Instrumentation](https://github.com/Jordan231111/ae-pcd-stamp-tracer-public)** ★ Flagship

`C++` · `Java` · `Lua` · `Python` · `NDK` · **~10K+ LOC · 5 years**

The **applied layer** of my native-Android research trajectory: an engine-neutral runtime
instrumentation system that proves the reusable foundations work at scale against a real
Cocos2d-x / Lua native-heavy Android runtime. Verification-first design — every feature ships
with a toggle, status counter, log line, and safe disabled state.

The full technical case study covers:
- **7 specific engineering problems solved** with their solutions — engine routing without source,
  stable observation points over fixed RVAs, Houdini alias-aware patching, JNI symbol-table
  stealth, fail-closed verification, side-effect-preserving fast iteration, process-scope filtering
- Architecture lineage showing how `lsposed-universal-template` and `arm64-houdini-lsposed-framework`
  feed into the application layer

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

- Houdini/native-bridge alias-aware writes — every alias for the same file offset gets patched, not just the verified one
- 16-byte ARM64 absolute-branch primitive: `ldr x17, #8 / br x17 / <addr>`
- IDA-style ARM64 pattern scanner + manual ELF symbol-table fallback when `dlsym` returns null

The default framework installs no target-specific hooks — that's intentional. The reusable part is
the loader, scanner, verifier, and patcher.

</td></tr>
<tr><td>

#### **[lsposed-universal-template](https://github.com/Jordan231111/lsposed-universal-template)**

`Java` · `Kotlin DSL` · `NDK` · `ShadowHook`

Quick-start LSPosed module scaffold on the modern **libxposed API 101**. Process scope defaults
skip push, crash, sandbox, and anti-cheat-satellite processes unless explicitly opted in.

- `EngineDetector` classifies Unity / Unreal / Cocos2d-x / Godot / Flutter / React Native / Xamarin via `nativeLibraryDir` + `/proc/self/maps`
- ShadowHook registered via `JNI_OnLoad` + `RegisterNatives` so the `.so` symbol table doesn't advertise package-derived JNI export names
- `configure-template.py` renames package, scope, metadata, and packaged `.so` name in one command

</td></tr>
<tr><td>

#### **[Archero-LSPOSED-Mod](https://github.com/Jordan231111/Archero-LSPOSED-Mod)**

`C++` · `Java` · `Android NDK` · `Unity IL2CPP`

Applied Unity IL2CPP runtime-hook study against a live `arm64-v8a` target build (Archero v7.9.1).
Demonstrates the IL2CPP side of the same engineering trajectory the Cocos2d-x case study
demonstrates for the script-engine side.

- Metadata-driven method/field resolution against `libil2cpp.so` (no fixed-RVA fallback at install time — fail-closed if a symbol can't be resolved)
- Side-aware logic in the IL2CPP managed-type system (`EntityBase.AddSkill` / `ContainsSkill` resolved by metadata)
- Authorized testing on owned devices only

</td></tr>
</table>

---

## Rust Systems

<table>
<tr><td>

#### **[MalwareMinimizer ★5](https://github.com/Jordan231111/MalwareMinimizer)**

`Rust 2024` · `clap` · `proptest` · `criterion` · `wiremock`

Cross-platform malware-scanning CLI. **RCOS Project Lead of a 5-person team** at Rensselaer Center
for Open Source — Spring 2026 MVP, presented at the RCOS Spring 2026 showcase. 321 commits, 139
PRs, 110 issues — issue-driven team ownership, not a one-off demo.

- **Team:** Jordan Ye (architecture / CI / db), Riley Horling (scanner / quarantine), Isaac Child (CLI), Michael Wang (utilities), Alexander Santos (setup / docs) — plus 3 external contributors
- **Cross-platform CI:** Linux / macOS / Windows × x86_64 / ARM64 with pinned action SHAs; `cargo audit` + `cargo deny` + `cargo supply-chain` + SBOM; crates.io-only dependency policy
- **Correctness + testing:** 174 automated tests (proptest + criterion + wiremock); atomic database writes with rollback; exit code `2` reserved for "found malware"
- **Onboarding:** auto-graded starter-task track for new Rust contributors; `validate_strict.sh` + CODEOWNERS keep the review bar high

</td></tr>
</table>

---

## Emulator and Device Tooling

<table>
<tr><td>

#### **[BlueStacksRoot ★64](https://github.com/Jordan231111/BluestacksRoot)** — `Batch` · `C++` · `Python` · `GitHub Actions`

Widely used BlueStacks 5 rooting toolchain. Native Magisk component, semantic + dynamic
integrity-check bypass in Python, full CI release pipeline.

</td></tr>
<tr><td>

#### **[mumu-magisk-1click ★39](https://github.com/Jordan231111/mumu-magisk-1click)** — `PowerShell` · `Batch`

MuMu Player 12 root setup. Locates installs via the Windows uninstall registry, patches per-instance
`customer_config.json` / `vm_config.json` / `shell_config.json` via real JSON parsing, writes
`.bak` files before first mutation, supports `--dry-run` and `--edition global|chinese|all`. CI
diffs the bundled installer against MuMu's official download API.

</td></tr>
<tr><td>

#### **[bluestacks-air-oneclick-root](https://github.com/Jordan231111/bluestacks-air-oneclick-root)** — `Bash` · `macOS`

BlueStacks Air on macOS. **SIP-aware** — separate code paths for SIP-enabled (prints the patched
file path for the user to copy) and SIP-disabled (fully automatic). Single curl-piped installer.

</td></tr>
</table>

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
| **Email** | yejordan8888@gmail.com |
