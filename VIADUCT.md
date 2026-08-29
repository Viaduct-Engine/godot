# Viaduct Engine — fork notes

This repository is a **thin downstream fork** of [godotengine/godot](https://github.com/godotengine/godot).
It carries Viaduct's branding, version identifiers, and the small set of core patches
that genuinely cannot live outside the engine tree. Everything else — gameplay and
editor features — lives in the separate `viaduct-modules` repository and is compiled in
via `scons custom_modules=...`.

Keeping the delta against upstream small is the whole point: the smaller the diff, the
cheaper every version bump.

## Branches

| Branch    | Purpose |
|-----------|---------|
| `master`  | Untouched mirror of `upstream/master`. **Never commit here.** Fast-forward only. |
| `viaduct` | Integration branch and repo default. Based on a Godot **stable tag**, carries only the Viaduct delta. All Viaduct work branches from here and merges back here. |

Remotes:

```sh
git remote add upstream https://github.com/godotengine/godot.git   # once
git remote -v
# origin    https://github.com/Viaduct-Engine/godot   (the fork)
# upstream  https://github.com/godotengine/godot      (canonical Godot)
```

`viaduct` currently tracks **`4.7.2-stable`**.

## Keeping current with upstream Godot

Track Godot **stable tags** (`4.7.2-stable`, `4.8-stable`, …), never `master`.
When a new stable tag lands:

```sh
git fetch upstream --tags --prune

# refresh the mirror (fast-forward only)
git checkout master
git merge --ff-only upstream/master

# replay the Viaduct delta onto the new tag
git checkout viaduct
git rebase --onto <new-stable-tag> <old-stable-tag> viaduct
#   e.g. git rebase --onto 4.8-stable 4.7.2-stable viaduct

# resolve any conflicts one patch at a time, build, then:
git push --force-with-lease origin viaduct
```

Use `rebase` while the branch is essentially single-maintainer; switch to `merge` if a
team works `viaduct` concurrently. After the rebase, bump the pinned `godot/` submodule
SHA in the `viaduct-engine` superproject deliberately and let CI build it.

CI runs this rebase-onto-latest-stable plus a build as an early-warning signal only — it
never auto-merges the result.

## Working on a ticket

Issues are tracked in Nixli (project `VIA`). Branch name = the exact ticket key
(`VIA-42`, no prefix), branched from `viaduct`. See the repo-root `CLAUDE.md` in the
`viaduct-engine` superproject for the full status/PR conventions.
