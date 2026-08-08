# AGENTS.md — Cursor / AI entry point

This file is the short agent brief for **xp_wellys_vfr_atc**.
Deep reference (architecture, modules, settings, Windows pitfalls): **[CLAUDE.md](CLAUDE.md)**.
Focused, always-on / path-scoped rules live in **[.cursor/rules/](.cursor/rules/)**.

---

## What this project is

C++17 X-Plane 12 plugin (macOS 13.3+, Universal Binary `arm64+x86_64`, Windows `win_x64`) for **VFR radio** — German NfL Sprechfunk 2024 (DACH-VFR) plus English ICAO-VFR profile. **No IFR.**

Language is the single switch: `settings::atc_language()` → `"de"` | `"en"` → profile `"DE"` | `"EN"`. Bundles: `data/atc_profiles/{de,en}/`. BZF-strict / conformance hooks gate on `atc_profile()=="DE"`.

Triple backend (runtime setting `backend_mode`): **local** | **openai** | **mistral**. Same binary; mode chosen in Settings. `x86_64` has no local inference (`local` → `openai` at load).

License: **GPL-3.0-or-later**.

---

## Hard invariants (do not violate)

1. **Backend Adapter Rule** — Engine code talks only to `backends/i_{speech_to_text,language_model,text_to_speech}.hpp`. Mode selection lives **only** in `backends/loader.cpp::run_worker()`. Never branch on `settings::backend_mode()` from engine code. Enforced by `tests/test_audit_logging.cpp`.
2. **SDK-free engine** — `xp_atc_engine` OBJECT lib must stay free of `<XPLM*.h>`. SDK TUs belong in the plugin module.
3. **Main thread** — X-Plane API only on the main thread. Inference / network / heavy work on `std::thread` + atomics.
4. **No secrets in `settings.json`** — API keys only in Keychain (`com.xp_wellys_devfr_atc.{openai,mistral}`). Never log full keys (last 4 chars only).
5. **Do not “clean up” legacy runtime IDs** — Keychain services, plugin signature `ch.thWelly.wellys_devfr_atc`, commands `xp_wellys_devfr_atc/*`, prefs dirs `xp_wellys_devfr_atc` stay as-is (installations depend on them).

---

## Build & test

```bash
make setup      # SDK, vendor, spikes
make build      # Universal .xpl (arm64 local ON, x86_64 local OFF, lipo)
make test       # Catch2; --order rand --rng-seed 42
make all        # clean + format + build + lint + test
make repl       # headless engine REPL (no X-Plane / audio / models)
make sanitize   # ASan/UBSan on engine path only (not the .xpl)
```

New tests must reset via the Catch2 listener pattern; always set a realistic `frequency_type` (do not assume unloaded `flight_phase`).

---

## Layout (where to edit)

| Area | Path |
|------|------|
| ATC logic (SDK-free) | `src/atc/`, `src/atc/flows/` |
| Backends | `src/backends/` — extend `i_*.hpp` then all three families |
| Plugin / session / audio / UI | `src/main.cpp`, `src/atc/atc_session.*`, `src/audio/`, `src/ui/` |
| Profiles / phraseology | `data/atc_profiles/{de,en}/` |
| Models catalog | `data/models_catalog.json` |
| Tests | `tests/` |

Includes use subdir prefixes: `#include "backends/whisper_stt.hpp"`.

---

## When unsure

Read **CLAUDE.md** for the full module map, state machine, inference pipelines, Windows delay-load pitfalls, and coding conventions. Prefer `make` over raw CMake for local workflows.
