# Windows and Mac handoffs

## Branches

- `work/windows`: main editing branch on Windows.
- `work/macos`: Mac project configuration, builds, tests, and fixes.
- `main`: reviewed, integrated work; no claim of a working app until device tests pass.

All three belong to one project. Pulling one branch does not bring in changes from another branch. Use explicit merges for handoffs. Never discard local changes or force-push to solve a synchronization problem.

## Before every handoff

Check `git status`. Commit coherent local changes first, or explicitly preserve unfinished work. Pause simultaneous edits to the same files. If a merge reports conflicts, stop, review both versions, and resolve them before building or continuing edits.

## Windows to Mac

On Windows, review changes, commit, and push `work/windows`. On the Mac, starting with a clean checkout:

```sh
git switch work/macos
git fetch origin
git pull --ff-only
git merge origin/work/windows
```

Build and test the merged result. Record the commit, device, OS, and result in device-tests.md. Commit fixes and progress notes, then:

```sh
git push
```

## Mac to Windows

On Windows, starting with a clean checkout:

```sh
git switch work/windows
git fetch origin
git pull --ff-only
git merge origin/work/macos
git push
```

If `pull --ff-only` fails, inspect the branch divergence before resolving it; do not reset away work. Merges may fast-forward or create a merge commit. Do not routinely cherry-pick the same changes between these branches.

## Promote stable work

Use a pull request into main after checks appropriate to the change pass. Documentation-only changes do not require an iPhone build. App changes require Mac/device evidence. If conflict resolution changes the tested code, test the result again. Bring the updated main branch into both working branches before the next integration cycle. Prefer preserving shared commit ancestry when merging long-lived work branches.

Update PROGRESS.md at each handoff with completed work, limitations, and the exact next action. No automatic bidirectional sync or automatic background commits are configured.
