# FocusDrop decisions

| Decision | Reason |
| --- | --- |
| FocusDrop is the project and app name | User-selected name; Bloom is only the research reference |
| Native iOS first | Validate Apple's blocking and NFC APIs directly on the user's iPhone |
| Native Android later | Android enforcement requires separate platform work |
| Windows editing and Mac builds/tests | Matches the user's two-computer workflow |
| work/windows and work/macos branches | User-requested separate branches, synchronized by explicit merges |
| NTAG213 and NTAG215 | Both accommodate a compact identifier; no unclonability claim |
| Offline MVP, no backend | Keep the core card-unlock loop independent of a service |
| Short automatic breaks remain experimental | Background re-blocking must be proven on device |
| Research document stays local | Only the derived project plan is included in the repository |

See BUILD_PLAN.md for implementation details and source references.
