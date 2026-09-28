# Device test log

No iOS build, blocking test, or NFC scan has been performed yet.

For each test batch, record:

- Date and tested Git commit.
- Device model, OS version, Xcode version, and build configuration.
- Card type, where relevant; avoid recording full card credentials.
- Scenario and expected behavior.
- Observed result: pass, fail, or not tested.
- Reproduction steps and follow-up work for failures.

Phase 0 acceptance: the minimal app launches on the iPhone and a Windows-originated edit can be built on the Mac. Later phases add the blocking and card-unlock matrix from BUILD_PLAN.md.
