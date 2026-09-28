# FocusDrop Build Plan

## Recommendation

Build a native iOS app first, validate it on your iPhone 17 Pro Max, and then implement Android against the same card format and product rules. You confirmed access to a Mac. Use VS Code on Windows for primary editing and a separate Mac checkout for Apple SDKs, signing, extensions, builds, installation, and device debugging. Synchronize through GitHub using the Windows and Mac branch workflow below.

FocusDrop is an independent NFC-card self-control app using your NTAG213 or NTAG215 cards. Use FocusDrop for the app name, project documentation, and future interface text. The supplied Bloom research is reference material, not a set of instructions to execute. Its descriptions of Bloom's private implementation are unverified. No app code has been built or tested yet.

Plan in focused work sessions rather than fixed calendar deadlines. A first working loop is a reasonable target for the first 3–5 sessions once signing and hardware are available; a dependable personal beta is a planning estimate of roughly 10–15 sessions. Apple approval and device failures can extend this. Android is a separate milestone with its own estimate after a feasibility test.

## Product behavior

The first version supports an adult managing their own device:

1. Grant Screen Time access.
2. Pair one NFC card and optionally a backup card.
3. Select a few distracting apps using Apple's picker.
4. Start focus without needing the card.
5. iOS shields the selected apps.
6. Open our app, choose Scan to unlock, and scan a paired card.
7. The app ends the session and removes its shields.

Everything in this loop should work offline after installation and pairing. The card is a passive identifier, not a processor that blocks apps or a storage location for your app list. The phone performs enforcement. Keep recovery available if the card is lost. Do not claim that self-control authorization is impossible to revoke.

Initial scope: one focus profile, manual start, physical-card end, guided pairing, local state, visible permission status, and emergency recovery. Add scheduling after the manual loop is reliable. Defer accounts, subscriptions, a backend, parental controls, full Screen Time analytics, premium card manufacturing, and advanced anti-copy hardware.

## Research corrections that affect the design

| Research claim or implication | Engineering decision |
| --- | --- |
| Family Controls approval is required before any compilation | Separate development capability/signing from distribution approval. Apple requires a distribution entitlement request for the app and applicable Screen Time extensions. Start the requests early. [Apple entitlement guidance](https://developer.apple.com/documentation/familycontrols/requesting-the-family-controls-entitlement). |
| A custom `bloom://unlock` tag silently wakes the app on iOS | Start with an explicit in-app NFC scan. Apple's documented background flow uses supported URLs or Universal Links and requires the user to tap a notification. Custom schemes are not supported by that documented flow. [Apple background NFC](https://developer.apple.com/documentation/CoreNFC/adding-support-for-background-tag-reading). |
| Individual authorization makes the app undeletable | Apple distinguishes individual authorization from parental controls and permits deauthorization. Treat removal restrictions as a separately tested option, never a guarantee of tamper-proof blocking. [Apple Screen Time overview](https://developer.apple.com/videos/play/wwdc2022/110336/). |
| Five-minute automatic breaks are an ordinary schedule | The documented minimum Device Activity monitoring interval is 15 minutes. A foreground timer or notification is not reliable background enforcement. Short breaks require a separate device-tested approach. [Apple interval constraint](https://developer.apple.com/documentation/deviceactivity/deviceactivitycenter/monitoringerror/intervaltooshort). |
| A static UID, password, or signed payload proves an unclonable physical card | NTAG213/215 provide static identifiers and modest memory protection. A readable static payload remains copyable; signatures can authenticate content without proving a fresh physical scan. [NXP specifications](https://www.nxp.com/products/NTAG213_215_216). |
| Android must intercept settings and uninstall screens | Do not adopt that approach for this adult self-control app. Google's policy restricts preventing disable/uninstall, with specific parental/enterprise exceptions. Use transparent permissions and recovery. [Google policy](https://support.google.com/googleplay/android-developer/answer/16313518?hl=en-GB). |
| Android requires access to all screen text and a separate draw-over-apps permission | Scope the experiment to package/window events. Evaluate accessibility overlays before adding general overlay permission; avoid retrieving screen contents where unnecessary. [Android accessibility service](https://developer.android.com/reference/kotlin/android/accessibilityservice/AccessibilityService), [overlay reference](https://developer.android.com/reference/android/view/WindowManager.LayoutParams). |
| A running extension can be the ultimate state authority | Extensions are intermittent processes. Persist desired session state, revision, and schedule identifiers; reconcile enforcement from both the app and extension. Do not assume either process is always alive. |
| Usage thresholds substitute for a complete analytics API | Begin with our own focus-session history. Research Device Activity Reports separately for actual usage reports; do not present focus duration as measured screen-time reduction. |

## Stack and tools

| Component | Choice | Purpose |
| --- | --- | --- |
| Editor and history | VS Code and Git | Edit files and save reviewable increments across days. A private remote repository is optional. |
| iOS build environment | Compatible stable Xcode on a Mac | Build, sign, install, inspect logs, and debug on the iPhone. Check the Mac/phone OS combination against [Apple's requirements](https://developer.apple.com/xcode/system-requirements/). |
| iOS language and interface | Swift and SwiftUI | Direct access to Apple's native controls and NFC APIs. |
| Screen Time permission and selection | FamilyControls | Individual authorization and FamilyActivityPicker. Keep selection tokens opaque. |
| Enforcement | ManagedSettings | Apply and remove shields owned by our app. |
| Schedules | DeviceActivity and a monitor extension | Apply schedule decisions when the main app is inactive. |
| Shield appearance | ManagedSettingsUI shield configuration extension | Explain that focus is active and how to open our app to scan. Add a shield action extension only if needed. |
| Card interaction | Core NFC | Read, initialize, verify, and pair NDEF tags. |
| Shared state | Codable records in an App Group container | Share versioned session state and selections between the app and extension. Use coordinated atomic writes for critical records; small UI preferences may use shared UserDefaults. |
| Device credentials | Keychain where needed | Device-local secrets, if later features need them. It does not turn a readable tag identifier into a secret. |
| Verification | Swift Testing/XCTest and real-device scenarios | State transitions, payload parsing, scheduling races, and actual shields/NFC. |
| Android later | Kotlin, Jetpack Compose, Android NFC, carefully scoped AccessibilityService | Separate native implementation with the same card protocol and session behavior. Android Studio/SDK and Gradle provide builds. |
| Backend | None initially | Pairing, blocking, and recovery remain local. |

Why native first: the hardest work lives in platform-specific NFC, Screen Time extensions, and Android services. React Native or Flutter would still need native implementations of these components. A shared interface can be reconsidered later, but it does not remove this work. Share specifications, test vectors, and design decisions first, rather than introduce a cross-language bridge before validating the core.

Budget for Apple Developer Program membership; Apple currently lists USD 99 per membership year or local currency where available. Confirm local price and account capabilities during setup. Xcode is available without a paid subscription, but this project must validate its required signing capabilities. [Membership details](https://developer.apple.com/support/compare-memberships/).

## Cards and pairing

NTAG213 has 144 bytes of user memory; NTAG215 has 504. Both are adequate for a small versioned identifier. Extra capacity does not improve blocking or authentication. Start with a few inexpensive writable plastic cards, including a spare. Avoid metal enclosures until the working prototype can be tested with purpose-built on-metal NFC construction. [NXP data sheet](https://www.nxp.com/docs/en/data-sheet/NTAG213_215_216.pdf).

Proposed first protocol: one NDEF record containing a short app-specific marker, format version, and randomly generated 128-bit card ID. Keep the complete encoded message under the NTAG213 capacity, including NDEF/container overhead. Publish test vectors in the repository so the Android implementation can read identical cards.

Pairing reads existing content first, identifies blank/compatible/unrelated/locked tags, requests confirmation before replacing unrelated data, writes when appropriate, then reads back before recording success. Accept only supported versions and bounded payload lengths. No permanent tag locking during development; some locking operations are irreversible. A card can be paired separately to more than one phone, but it only changes the state of the phone scanning it.

An unlock requires a fresh successful reader-session result matching a locally paired ID. A typed ID, pasted URL, or ordinary deep-link navigation must not unlock. Require a paired-card scan or explicit recovery to remove/replace a paired card during an active session; otherwise re-pairing becomes a trivial bypass. Duplicated static tags remain outside the protection offered by this hardware.

Later, a short HTTPS NDEF Universal Link can improve app discovery. That adds domain hosting and association files. Opening the link should route to scanning, not count as physical-card proof. Evaluate richer NFC-delivered metadata separately before changing that rule.

## Architecture and state rules

Keep platform-neutral concepts independent from Apple's selection tokens:

- `CardRecord`: protocol version, card ID, display name, pairing date, backup status.
- `FocusProfile`: profile ID and product settings; an iOS adapter owns the encoded FamilyActivitySelection.
- `FocusSession`: ID, profile ID, start time, end policy, optional deadline, state, and end reason.
- `ScheduleRule`: ID, selected weekdays, local start/end times, time-zone policy, and enabled status.
- `EnforcementSnapshot`: revision, desired restrictions, last reconciliation result, and authorization status.
- `SessionEvent`: local events such as started, card unlock, emergency exit, or detected authorization loss. These are not measurements of every app launch.

Proposed states: setup required, ready, focused, break active (later), and recovery required. Scanning is a temporary operation that does not remove shields until verification succeeds. Repeated scans and callbacks must be idempotent. Persist state transitions and reconcile shields on foreground entry and supported extension callbacks. Coordinate writes across processes; an in-process Swift actor alone does not serialize an extension's writes.

MVP supports one active session, reducing overlap problems. When schedules arrive, define behavior explicitly: a manual session ends by card/recovery; a scheduled window can end at its configured time; a card exit suppresses only that schedule occurrence, not future days. Stale callbacks cannot re-block a session already ended by card. If overlapping schedules are later enabled, compute all active restrictions so one ending schedule cannot clear another's shield.

Authorization loss must be visible as inactive/degraded protection. A UI boolean saying focused is insufficient evidence that blocking works. No promise of a continuously running watchdog or guaranteed exact-time callback delivery.

## Phases and completion gates

### Phase 0  Windows and Mac setup with GitHub  One or more setup sessions

The chosen project repository is [sfnkh/FocusDrop](https://github.com/sfnkh/FocusDrop). Use FocusDrop as the project name. The Windows checkout is established and an initial setup contribution has been pushed successfully. The repository started with a README on main; its history is preserved. See PROGRESS.md for the current handoff status.

Phase 0 sequence:

1. Confirm Mac model/chip, macOS version, iPhone iOS version, Xcode version, developer membership, and available cards. Windows is the primary editing computer; the Mac builds and tests iOS.
2. Verify Git and VS Code on Windows. Install or verify compatible Xcode, VS Code, and Git on the Mac.
3. Sign in to GitHub through the normal authentication flow and verify access to FocusDrop. Never put tokens or passwords in chat or tracked files.
4. Inspect the repository and local folder before changing anything. Clone FocusDrop into `C:\Users\sufya\Desktop\bloom 101\FocusDrop` on Windows. If that destination already exists, inspect and reuse a suitable checkout rather than overwrite it. Preserve the repository's existing history, README, license, app files, and other work. Do not create an unrelated history or force-push. Clone the same repository on the Mac into a user-owned folder, suggested as `~/Developer/bloom 101/FocusDrop`.
5. Bring BUILD_PLAN.md and PROGRESS.md into that checkout. Add or update a README, an appropriate .gitignore, and Mac setup instructions. Exclude build output, local Xcode user settings, credentials, and provisioning profiles. Keep shared project configuration tracked. Keep the supplied research document local unless separately selected for publication.
6. Establish two working branches, proposed as `work/windows` and `work/macos`, plus the repository's stable default branch (assumed `main` only after checking). Create both from the same existing base, or from the first setup commit if the repository is empty. Review staged files, commit the setup work on `work/windows`, push, and verify GitHub. Follow existing protections and PR requirements. Resolve access failures without overwriting history.
7. Create the minimal iOS app, configure development signing and required capabilities, connect the iPhone, and install the first build. Prepare distribution entitlement requests for the app and relevant Screen Time extensions.
8. Commit Mac-created project configuration and fixes on `work/macos`, push, and merge them back into `work/windows` before continuing Windows edits. Update setup/progress notes and demonstrate a full Windows-to-Mac-to-Windows handoff.

#### Two computer branch workflow

These are two checkouts of one app, not separate Windows and Mac app versions. Separate branches do not synchronize automatically. Pulling `work/macos` only updates that branch; it does not import changes from `work/windows`.

- Windows: fetch/pull current branch, merge any pending `origin/work/macos` changes, edit in VS Code, commit, and push `work/windows`.
- Mac: save/commit current work, fetch/pull current branch, then merge `origin/work/windows` into `work/macos`. Resolve conflicts before building. Build and test the resulting commit on the iPhone. Commit any Mac-side fixes and push `work/macos`.
- Windows: fetch, merge `origin/work/macos` into `work/windows`, then resume work. Prefer merge commits or fast-forwards as appropriate; avoid cherry-picking routine synchronization because it duplicates commits across the branches.
- Stable branch: promote tested work through a PR according to repository rules. Record the tested commit and device/OS in `docs/device-tests.md`; test again if later conflict resolution or new code changes the tested result. Bring the updated stable branch back into both work branches.

Avoid simultaneous edits to the same files during a handoff. Never force-push or discard uncommitted changes to synchronize. Git merges will occasionally need review; two machine branches add that overhead compared with sharing one feature branch.

Keep the repository checkout as the canonical home for BUILD_PLAN.md and PROGRESS.md after importing them. The planning copies currently in the parent `bloom 101` folder should be marked as superseded or replaced with a pointer after successful import, rather than maintained as separate plans. Open the inner FocusDrop folder in VS Code on both machines.

Track shared Xcode project files, entitlements, extension configuration, and shared schemes. Ignore DerivedData, build output, per-user Xcode settings, local machine paths, signing secrets, and provisioning profiles. Add `.gitattributes` with a reviewed cross-platform text/line-ending policy to reduce Windows/Mac formatting churn.

Record the Mac model/macOS, iPhone iOS version, Xcode compatibility, Apple developer membership status, and actual card types. Pick temporary identifiers for the app, extensions, and App Group. Create the repository structure and local build instructions. Configure development signing/capabilities and begin distribution entitlement requests for intended targets. No purchase or account enrollment is performed automatically.

Deliverables: both FocusDrop checkouts and working branches exist; GitHub contains reviewed planning files and the initial scaffold; a minimal app is installed on the iPhone; tool versions, signing steps, and entitlement status are documented. Gate: a Windows edit reaches the Mac for a device test, a Mac change returns to Windows, and the real phone can run our signed build. Resolve capability failures before investing in screens.

### Phase 1  Prove blocking  Sessions 2–3

Implement individual authorization, permission status, Apple's picker, selection persistence, and apply/clear actions for a named ManagedSettingsStore. Start with two distracting apps and default shield appearance. Keep an explicit development recovery control.

Deliverables: a small real-device demo and observations. Gate: selected apps become shielded; unselected apps remain usable; clearing our restrictions restores access. Test foreground/background, force-quit, authorization denial/revocation, and restart. Record failures rather than assuming persistence from the research.

### Phase 2  Prove card pairing and unlock  Sessions 3–5

Implement the versioned NDEF format, tag inspection, pairing, write/read-back verification, and a scan-to-end action. Handle an unknown card, malformed content, unsupported version, canceled scan, timeout, read-only tag, and repeated scan. Add backup-card pairing and explicit replacement rules.

Deliverables: card protocol with test vectors and an end-to-end demo. Gate: start focus without a card; a valid paired card ends focus offline; a wrong/canceled scan leaves restrictions intact. Repeat the loop at least 20 times on the iPhone, testing both NTAG213 and NTAG215 when available. This is an acceptance exercise, not proof of long-term reliability.

### Phase 3  Make the personal MVP usable  Sessions 5–7

Build onboarding, one focus profile, the focus screen, scan guidance, card management, and recovery. Add persisted state, coordinated writes, and reconciliation. Replace development-only unblock controls with the documented recovery path. Keep essential apps out of the initial test selection and explain the selection before starting.

Deliverables: a usable offline app with clear permission and session status. Gate: ordinary day-to-day use works without attaching a debugger; relaunch does not lose the session or pairings. Test crash recovery and interrupted transitions. Use on your own phone for at least a full day before adding more scope.

### Phase 4  Add scheduled focus  Sessions 7–9

Add the monitor extension and one recurring schedule. Share the profile/session snapshot through App Groups. Define weekday, midnight, time-zone/DST, card-exit, and schedule-edit behavior. Register schedules with error handling and handle stale/duplicate callbacks. Add custom shield appearance if useful.

Deliverables: a schedule that starts and ends according to the chosen rules. Gate: scheduled changes work in device tests while the main app is inactive. Test overnight windows, reboot, altered time zone, and manual sessions. Do not proceed with more schedules while this one is unreliable.

### Phase 5  Validate breaks and refine recovery  Sessions 9–11

First test a supported longer break and automatic re-shielding. Investigate five-minute breaks as a separate experiment, accounting for Apple's 15-minute monitoring minimum. Test any candidate implementation with the app backgrounded, force-quit, device locked, and device restarted. A usage threshold represents usage, not necessarily five elapsed minutes.

Deliverables: a written supported-break policy and device evidence. Gate: ship a break option only when the tested behavior matches the promise. If five-minute re-locking cannot be made dependable, defer it or offer a clearly described longer break. Notifications may inform the user but are not enforcement. Keep a deliberate emergency exit with a confirmation and local record; avoid copying the research's contradictory lifetime/monthly quota.

### Phase 6  Reliability and personal beta  Sessions 11–15

Exercise lost-card recovery, duplicate scans, denied/revoked permissions, state migrations, extension failures, time changes, restart, and conflicting restrictions from other Screen Time apps. Check text size, VoiceOver, dark mode, scanning instructions, and empty/error states. Add small local history: sessions, focus duration, card exits, emergency exits. Keep actual Screen Time reports separate.

Deliverables: reproducible build instructions, a recorded test matrix, known limitations, and a device build suitable for a longer personal trial. Gate: several days of routine use with no unexplained unlocks or unrecoverable lockouts. TestFlight is contingent on membership, distribution entitlements for every relevant target, signing, and Apple's processing/review; it is not a fixed-day promise.

### Phase 7  Android feasibility then Android MVP  Estimate after spike

First implement a narrow Kotlin proof of concept on an NFC-capable Android phone: read the same card, detect a selected app, and present the chosen blocking interface. Evaluate minimal AccessibilityService permissions and overlay options. Validate required Play disclosure/declaration and current platform restrictions. Do not assume a foreground service or boot receiver guarantees uninterrupted enforcement.

If feasible, implement pairing, session rules, recovery, scheduling, and a native Compose UI. Reuse the card protocol, behavior specifications, and test vectors; select apps independently on Android because iOS opaque tokens do not transfer. Test at least a Pixel and Samsung before broader compatibility claims, including permission loss, reboot, battery management, multi-window behavior, and safe mode.

Deliverables: an Android beta and an explicit parity/limitations table. Gate: the card workflow works on both platforms, and Android limitations are described honestly. Website blocking and usage reporting get separate feasibility work; they do not automatically match iOS. Google Play approval remains an external dependency. [Google AccessibilityService disclosure requirements](https://support.google.com/googleplay/android-developer/answer/10964491?hl=en).

## Repository and daily contributions

Proposed structure once implementation starts:

```text
apps/ios/                 iOS app, extensions, and Xcode project
apps/android/             Added when Android implementation begins
packages/FocusCore/       Swift session rules and focused unit tests
specs/card-protocol.md    Cross-platform NFC format and test vectors
specs/session-rules.md    Shared behavioral contract
docs/setup-macos.md       Repeatable Mac setup and device installation
docs/device-tests.md      Device/OS observations and acceptance checks
docs/decisions.md         Decisions and reasons
BUILD_PLAN.md             This roadmap
PROGRESS.md               Completed work and exact next step
```

Each session should finish with one coherent change, relevant checks, a short device test for you, and an updated progress note. Use Git commits once the repository is initialized; push only to a chosen authorized remote. Keep signing secrets and provisioning credentials out of source control. The progress file makes resuming in VS Code or another chat straightforward without depending on remembered conversation context.

Our next implementation target is Phase 0 followed by the Phase 1 blocking demo. The first major milestone is observable: two apps shielded on your iPhone and released by your paired physical card. Subsequent features must preserve that behavior.
