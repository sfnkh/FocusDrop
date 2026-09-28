# FocusDrop

FocusDrop is an NFC-card focus app: start a focus session on your phone, then scan a paired physical card to end it. The first implementation targets iOS, with Android planned after the core workflow is validated.

## Current status

Phase 0: repository and development setup. No app code, working blocking engine, or NFC integration exists yet.

## Start here

- [Build plan](BUILD_PLAN.md): phases, architecture, and completion gates.
- [Progress](PROGRESS.md): completed work and the next action.
- [Windows setup](docs/setup-windows.md): local checkout and editing.
- [Mac setup](docs/setup-macos.md): prerequisites for building and iPhone testing.
- [Branch handoffs](docs/git-workflow.md): moving work between computers.
- [Decisions](docs/decisions.md): agreed project choices.
- [Device tests](docs/device-tests.md): results tied to a specific commit and device.

## Planned stack

Swift and SwiftUI, FamilyControls, ManagedSettings, DeviceActivity, and Core NFC. NTAG213 and NTAG215 cards are the initial hardware targets. The MVP is local and offline, without a backend.

## Working on two computers

Windows uses `work/windows` for editing. The Mac uses `work/macos` for Xcode configuration, builds, and device tests. These are branches of the same app; changes move between them through explicit merges. `main` receives reviewed, tested work.

Open this repository's `FocusDrop` folder in VS Code, not its parent folder. Keep shared Xcode configuration in Git, and keep credentials and local build output out of it.
