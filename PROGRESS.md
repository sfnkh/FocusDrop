# FocusDrop Progress

## Current status

Phase 0 Windows checkout and repository documentation are prepared. App implementation has not started.

## Confirmed

- The project is named FocusDrop: an NFC physical-card app blocker. Use FocusDrop in the app, documentation, and future interface text; Bloom refers only to the supplied research and its source product.
- Initial test device is an iPhone 17 Pro Max.
- Hardware will use NTAG213 and/or NTAG215 cards.
- User prefers VS Code and has access to a Mac as well as Windows.
- User wants primary editing on Windows and iOS building/testing on the Mac, with separate working branches. Planned Windows checkout: `C:\Users\sufya\Desktop\bloom 101\FocusDrop`. Suggested Mac checkout: `~/Developer/bloom 101/FocusDrop`. Proposed branches: `work/windows` and `work/macos`, synchronized through explicit merges; the stable default branch receives tested work.
- Repository: https://github.com/sfnkh/FocusDrop. Cloned successfully on Windows, preserving the initial README and main history. Git and VS Code are installed; work/windows has been created.
- Android support is intended after the iOS core is validated.
- Research read and key platform assumptions checked against Apple, Google, Android, and NXP documentation.

## Recommended decisions

- Native Swift/SwiftUI iOS first, native Kotlin/Compose Android later.
- Offline operation and no backend for the MVP.
- Start focus in app; finish through a fresh paired-card scan.
- Individual self-control authorization; parental mode deferred.
- Scheduling follows the manual blocking/card-unlock proof.
- Five-minute breaks are an unproven feature pending device testing.

## Next work session

Verify publication of the setup contribution and work/macos branch. Then prepare the separate Mac checkout using docs/setup-macos.md. Confirm Mac/Xcode/iPhone compatibility and Apple account capabilities, initialize the iOS app, and install it on the phone. Demonstrate branch synchronization in both directions and record the tested commit.

## Validation so far

Repository inspection and planning/source review only. No app build, NFC scan, entitlement grant, or device blocking test has been performed. The Mac's compatibility and Apple account status remain unverified.
