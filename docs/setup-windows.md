# Windows setup

## Prepared

- Git and VS Code are installed.
- Repository: https://github.com/sfnkh/FocusDrop
- Local checkout: `C:\Users\sufya\Desktop\bloom 101\FocusDrop`
- Windows working branch: `work/windows`
- Existing `main` history is preserved.

Open the inner FocusDrop folder using VS Code's File > Open Folder. Alternatively, from PowerShell:

```powershell
code 'C:\Users\sufya\Desktop\bloom 101\FocusDrop'
```

The repository copies of BUILD_PLAN.md and PROGRESS.md are authoritative. Parent-folder copies are pointers only after migration.

## Daily work

Read PROGRESS.md and follow [the handoff workflow](git-workflow.md) before editing. Windows can edit source and documentation. iOS compilation, signing, and real-device checks take place on the Mac. Do not report a Windows-only source change as iPhone-tested.

Review staged changes before each commit. Never commit authentication tokens, private signing keys, provisioning profiles, or machine-specific paths. The original research document is not part of this repository.
