# Welly's VFR ATC — AI Voice ATC for X-Plane 12

![Welly's VFR ATC panel with ATIS broadcast at LSZB Bern-Belp](images/atc-atis-example.jpg)

> **Talk to the tower via push-to-talk — AI-powered VFR radio in German or
> English for X-Plane 12, running locally on your machine or through the
> cloud.**

Welly's VFR ATC turns your VFR flights in X-Plane 12 into a real radio
conversation: you press the push-to-talk key, speak your call into the
microphone, and the tower answers you back by voice — in either **German
(NfL/BZF)** or **English (ICAO)** VFR phraseology, with realistic reactions
even to pilot mistakes.

> ### This is the **VFR** plugin
>
> Pattern work, cross-country, UNICOM/CTAF, AFIS, VFR reporting points,
> traffic advisories — **VFR radio is what this plugin does.** It is the home
> of the German NfL/BZF profile and the BZF strict mode.
>
> **Looking for IFR?** That lives in a separate product:
> **[Welly's IFR ATC](https://github.com/rwellinger/xp_welly_llm_atc)**
> (English/ICAO only). See [Related plugins](#related-plugins).

### Get started

1. Download the latest release from
   **[GitHub Releases](https://github.com/rwellinger/xp_wellys_vfr_atc/releases)**
2. Follow the
   **[Quick start](docs/README.md#quick-start-prebuilt-release)**
   (unzip into `X-Plane 12/Resources/plugins/`, bind PTT, pick a backend)

Full install, backends, models, build and configuration:
**[Technical documentation](docs/README.md)**

## Table of contents

- [What Welly's ATC is for](#what-wellys-atc-is-for)
- [A typical VFR flight, end to end](#a-typical-vfr-flight-end-to-end)
- [What the plugin covers](#what-the-plugin-covers)
- [Platforms & backends](#platforms--backends)
- [What it can't do (yet)](#what-it-cant-do-yet)
- [Related plugins](#related-plugins)
- [Technical documentation](#technical-documentation)
- [License](#license)

---

## What Welly's ATC is for

> **This plugin is first and foremost oriented toward REALITY.** The goal is
> to reproduce VFR radio procedures as authentically as possible — in German
> per NfL/BZF, in English per ICAO — so that you can **train and practice for
> future exams and tests, such as the BZF**. We work continuously toward
> bringing the phraseology and the ATC flows even closer to real practice.

**Disclaimer.** Welly's ATC is a practice and training tool for flight
simulation. It is **not an official certification, not an educational
resource in the sense of accredited training, and not a substitute for real
exam preparation**. We accept **no responsibility and give no guarantee
whatsoever regarding passing any test or exam**. Use is at your own risk; no
warranty is given for the correctness of the phraseology shown. Corrections
from BZF holders are expressly welcome.

## A typical VFR flight, end to end

Every one of these calls is spoken — press PTT, talk, listen. The
phraseology hints panel tells you what to say if you are unsure, and the
tower reacts realistically when you get it wrong.

| Phase | What you say | What the tower does |
|---|---|---|
| **Before start-up** | Listen to ATIS | Automatic ATIS broadcast from live sim weather, with information letter |
| **Taxi** | "Ground, D-EABC, at the apron, request taxi for local VFR flight" | Taxi clearance to the holding point, runway and QNH |
| **Departure** | "Ready for departure" | Takeoff clearance with wind — or line-up-and-wait if traffic is in the way |
| **Pattern** | "Downwind runway 14" · "Final" | Pattern acknowledgement, landing sequencing ("you are number two, follow the traffic on left base") |
| **Landing** | "Request touch and go" | Clearance for touch-and-go, full stop, or an unsolicited go-around when the runway is blocked |
| **Cross country** | "Request frequency change" · "Leaving your frequency" | Frequency change approved, en-route traffic advisories, then handoff |
| **Inbound** | "Inbound November, 2000 feet, request joining" | Approach hands you to the tower with the destination frequency; pattern entry instructions |
| **Uncontrolled field** | "Kirchdorf traffic, D-EABC, joining downwind 25" | UNICOM/CTAF self-announcement acknowledged — no clearances, as in reality |

## What the plugin covers

- **Two language profiles — DE (NfL/BZF) & EN (ICAO)** — switchable at
  runtime in the settings; German is the default. German follows **NfL
  Sprechfunk 2024** (DACH) with optional **BZF strict mode** for readback
  checks. English is a self-contained **ICAO VFR** profile (Annex 10 /
  Doc 4444 / SERA), not a translation of the German one. The UI language
  is independent of the spoken phraseology — e.g. English menus while
  training German radio.
- **Traffic pattern** — entry, downwind, base, final, landing,
  touch-and-go, go-around, including landing sequencing ("you are number
  two, follow the traffic").
- **Cross country** — departure clearance, en-route frequency changes,
  approach handoff to the destination tower.
- **Airfield types** — adapts automatically:
  - **Uncontrolled** — UNICOM/CTAF self-announcements (no clearances)
  - **Tower** — clearances on the tower frequency
  - **Tower + ground** — separate ground for taxi
  - **AFIS** — information service without binding clearances
- **Voice radio** — push-to-talk (keyboard or joystick), context-aware
  **phraseology hints**, and coaching on poor radio discipline.
- **AI support** — speech recognition → intent understanding → speech
  synthesis, plus automatic **ATIS** from live weather and **traffic
  advisories** about surrounding aircraft.

## Platforms & backends

Choose the mode at runtime in Settings — same plugin binary:

| Mode | Where | Notes |
|---|---|---|
| **Local** | Apple Silicon (Metal); Windows x64 from **v0.8** (Vulkan) | 100% offline after a one-time model download. Native German voice via Piper `de_DE-thorsten`. |
| **OpenAI Cloud** | Any Mac, any Windows PC | Own API key. English TTS voices — German is spoken with a US accent. |
| **Mistral Cloud** | Any Mac, any Windows PC | Own API key. Multilingual, but **no German preset voice** — default voices still carry an English accent (issue #63). |

> **For accurate German, use Local mode.** Both cloud providers speak German
> with an accent.

**Intel Macs** can run the plugin in cloud mode only (no local inference).
**Windows** supports all three backends; the artifact ships `piper.dll` +
`onnxruntime.dll` beside the `.xpl`. Platform prerequisites, Vulkan vs CUDA,
and the VC++ redistributable troubleshooting (`0xC06D007E` on first TTS)
are in the
[technical documentation](docs/README.md#hardware-requirements).

## What it can't do (yet)

- **No IFR here** — instrument flight is
  **[Welly's IFR ATC](https://github.com/rwellinger/xp_welly_llm_atc)**
  (separate plugin). This one stays VFR-only.
- **German & English only** — no FR/IT phraseology planned.
- **No local mode on Intel Macs** — cloud only (OpenAI or Mistral).
- **Windows local mode is younger** than the macOS one; Vulkan compute is
  not guaranteed on every virtualised GPU (CPU fallback then).
- **Not modeled yet** — wake-turbulence separation, freely selectable taxi
  routes (currently always "via Alpha"), large hubs with a delivery
  workflow (LSZH, LSGG, …), virtual co-pilot / checklist reader.

> Effort estimates and detail:
> [Known limitations](docs/README.md#known-limitations).

## Related plugins

### Welly's IFR ATC — the instrument-flight sibling

**[Welly's IFR ATC](https://github.com/rwellinger/xp_welly_llm_atc)** shares
the same voice pipeline and backend choice (Local / OpenAI / Mistral), but a
different ATC flow: IFR clearance with squawk and SID, en-route sector
handoffs, STAR descent, approach and landing — driven by **SimBrief** and
X-Plane **CIFP** data.

**VFR → this plugin. IFR → that one.** They install side by side. The IFR
plugin is **English/ICAO only** and needs extra data (SimBrief OFP, OpenAir
airspace file).

### Welly's VFR Trainer — optional gamification

**[Welly's VFR Trainer](https://github.com/rwellinger/xp_wellys_vfr_trainer)**
suggests VFR flights in the DACH region and rates your radio after landing.
Welly's ATC works fully on its own — the trainer is optional.

## Technical documentation

Installation, quick start, backend modes, building from source, local
inference models, configuration, architecture and development workflow:

**→ [docs/README.md](docs/README.md)**

## License

[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.html)
— details and third-party dependencies:
[technical documentation](docs/README.md#license) and
[`THIRD_PARTY.md`](THIRD_PARTY.md).
