# Mac setup

Status: pending. No Mac configuration or device testing has been performed.

## Information needed

- Mac model/chip and macOS version.
- Installed Xcode version, if any.
- iPhone iOS version.
- Apple Developer membership status.
- Available NTAG213/215 cards.

Check compatibility before installing or upgrading Xcode. References: [Xcode requirements](https://developer.apple.com/xcode/system-requirements/), [Family Controls distribution entitlement](https://developer.apple.com/documentation/familycontrols/requesting-the-family-controls-entitlement).

## Repository setup

Install or verify Git and VS Code. Authenticate to GitHub using a supported sign-in flow; do not paste credentials into chat or source files. Once the remote working branches exist, use Terminal:

```sh
mkdir -p "$HOME/Developer/bloom 101"
cd "$HOME/Developer/bloom 101"
git clone https://github.com/sfnkh/FocusDrop.git FocusDrop
cd FocusDrop
git switch --track origin/work/macos
```

If a FocusDrop folder already exists, inspect it first rather than clone over it. Open that folder in VS Code. Follow [branch handoffs](git-workflow.md) to import subsequent Windows edits.

## iOS setup gate

1. Verify compatible Xcode and required components.
2. Sign in to the appropriate Apple development team.
3. Create the SwiftUI app under `apps/ios`, using FocusDrop as the display name.
4. Configure app identifiers, signing, capabilities, and planned extensions.
5. Connect and trust the iPhone; complete Developer Mode prompts as needed.
6. Install and launch the minimal app.
7. Record versions, build steps, entitlement status, and test evidence.
8. Commit shared project configuration on `work/macos` and send it back to Windows.

Exact build commands will be added after the project and scheme exist. Development signing and distribution entitlement approval are separate checks. No account enrollment, purchase, or distribution approval is assumed.
