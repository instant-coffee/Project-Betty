# Where should the photo metadata check run so every writer passes it?

Research for [#14](https://github.com/instant-coffee/Project-Betty/issues/14) (map: [#1](https://github.com/instant-coffee/Project-Betty/issues/1)). Researched 2026-10-01 against Claude Code's, GitHub's and libgit2's documentation, plus tests on this session's cloud VM. Platform behaviour changes, so check the linked page before relying on a detail.

**Evidence note.** The network policy for this session blocked `docs.github.com`, `github.blog`, `workingcopyapp.com`, `git-scm.com` and `apps.apple.com`. Claims that rest on those sites come from search-index excerpts (one step removed) and are marked *excerpt*. Anything marked *tested* was run on the VM. The Claude Code hooks and cloud-session pages were read directly. The cloud-environments page came back too large to read.

## The question

[#4](https://github.com/instant-coffee/Project-Betty/issues/4) settled that resized, metadata-free JPEGs go in git and that a GPS/size check must run before they land. Writers reach the repo from the phone (Working Copy), Claude Code cloud sessions (branch + PR), the laptop, and anywhere else that can push to GitHub. Where does the check live so none of them skips it?

## Answer in brief

**One script, three callers, and the GitHub Action is the authority.** Put the check in a small stdlib-only Python script in the repo. Run it from a GitHub Actions workflow on every push and pull request, because that is the only place Working Copy, github.com uploads, cloud sessions and the laptop all pass. Add a Claude Code hook as an early, preventive layer for agent writes. Treat a local git hook as optional.

**The Action cannot prevent exposure.** It runs after the commit is already on GitHub, and on a public repo that means published. GitHub offers no server-side content check before the push is accepted on this repo: push rulesets only match file path, extension and size, and only exist for private and internal repos. So the phone's real prevention is the capture path (the Shortcut from #4). The Action is the backstop that catches a slip within a minute or so.

**Working Copy almost certainly runs no hooks**, so no client-side check can reach the phone. That's the reason the Action is the authority.

**The ticket's premise about the cloud VM is half wrong.** This session's VM has ImageMagick 6.9.12 (`identify`, `convert`, HEIC readable). It has no exiftool. A stdlib Python script needs neither.

## 1. Who passes through what

| Writer | Local git hook | Claude Code hook | GitHub Action |
|---|---|---|---|
| Working Copy (phone) | Not documented, almost certainly no (§2) | No | Yes, on push |
| github.com web upload | No | No | Yes |
| Claude Code cloud session | None, because a fresh clone has no hooks installed | **Yes**, from the repo's committed `.claude/settings.json` | Yes, on the branch push and the PR |
| Claude Code on the laptop | Only if installed in that clone | Yes | Yes |
| Owner's own `git` on the laptop | Only if installed in that clone | No | Yes |
| Anything else that can push (Copilot agent, a future collaborator) | No | No | Yes |

Only the Action column is complete.

## 2. The options

### GitHub Action (plus an optional ruleset)

- **Reach:** every writer, since the check runs on GitHub's side of the push.
- **Limit:** it is detective, not preventive. The objects are already on GitHub when the job starts. GitHub's own sensitive-data guidance says that rewriting history leaves commits reachable through pull-request refs (`refs/pull/*` are read-only), and that purging cached views and PR references means contacting GitHub Support (*excerpt*, [Removing sensitive data][gh-sensitive]). A branch pushed to a public repo is public the moment it lands, so even a PR gated before merge has already exposed the photo on its branch.
- **What it buys:** the failure is loud and fast, merging a red PR is a deliberate act, and the same job can hold the Showcase update (§5).
- **Tooling:** none beyond what a runner has. The `ubuntu-24.04` runner inventory lists Python 3.12.3, pip 24.0 and jq 1.7. It does not list exiftool or ImageMagick (read via a fetch summariser, so treat the absence as likely, not certain). `apt-get install libimage-exiftool-perl` would work but is unnecessary (§4).

### Claude Code hook

- **Reach:** only Claude Code's own tool calls, in cloud sessions and on the laptop. The hooks reference lists `PreToolUse` as able to block a tool call, exit code 2 as the blocking mechanism, and an `if: "Bash(git commit *)"` filter that matches inside compound commands ([Hooks reference][cc-hooks]).
- **Cloud sessions:** they don't read the owner's `~/.claude/settings.json`, but they do read the repo's committed `.claude/settings.json` ([Hooks reference][cc-hooks], [Claude Code on the web][cc-web]). The hook therefore travels with the repo.
- **What it buys:** this is the only option that is *preventive* for an agent write. A `PreToolUse` hook on `git commit` and `git push` can stop a GPS-tagged JPEG before it reaches a branch.
- **Limit:** it doesn't touch the phone, github.com uploads or hand-typed `git`. It is a guard rail, not a gate.
- **Tooling:** whatever the script needs, so Python 3 (present on this VM).

### Local git pre-commit hook

- **Reach:** one clone on one machine. Hooks live in `.git/hooks` or a per-clone `core.hooksPath`, not in the repository contents, so cloning never installs them, and `--no-verify` skips them. This is standard git behaviour. I couldn't re-read the githooks page (blocked).
- **Working Copy:** its manual wasn't reachable. Working Copy is built on libgit2, per its App Store listing (*excerpt*), and libgit2's tracker has an issue asking for hook support whose opening line reads "It seems that libgit2 does not allow the processing of hooks, that git would perform" ([libgit2 #964][libgit2-964]). The issue is closed, but the page I fetched didn't show how. An iOS app also can't shell out to git or run a repo's scripts. So treat "no hooks" as the working assumption and confirm it in Working Copy's manual. Nothing in the recommendation depends on it being "no".
- **Verdict:** a three-line `.githooks/pre-commit` calling the same script is cheap for the laptop, but it can't be a pillar.

## 3. Can a server-side check stop a direct push to `main` on a free public repo?

- **Rulesets exist for this repo.** They are available in public repos on GitHub Free (*excerpt*, [About rulesets][gh-rulesets]). Repo `instant-coffee/Project-Betty` is public and owned by a personal account.
- **A required status check blocks unchecked pushes, not bad ones.** A commit that has just been created has no passing check, so a direct push to a branch that requires one is rejected. The commit must first be pushed to another branch, pass there, and then be merged or pushed. In effect the branch becomes pull-request-only (*excerpt*, [About protected branches][gh-protected]). The gate can't tell a clean push from a GPS-tagged one. It refuses both until a check exists for that exact commit.
- **Bypass defeats it for the person it matters most for.** A ruleset's bypass list can exempt actors. If the owner is exempt, Working Copy can push straight to `main` and the gate protects nothing. If the owner is not, every phone commit has to go through a branch and a PR (Working Copy Pro can create PRs, per [#5](https://github.com/instant-coffee/Project-Betty/issues/5)).
- **It collides with the Showcase Action.** [#10](https://github.com/instant-coffee/Project-Betty/issues/10) has an Action committing to `main` on push, and that commit would be rejected the same way. I couldn't confirm that `github-actions` can be a bypass actor in a personal-account ruleset. One excerpt suggested bypass actors may be limited to organisation repos, so don't plan on it.
- **No pre-receive content check on github.com.** Push rulesets restrict file paths, extensions and sizes, and apply to private and internal repositories (*excerpt*, [push rules changelog][gh-push-rules]). They don't read EXIF, and this repo is public. So "reject GPS-tagged JPEGs at push time" isn't available on GitHub at any tier I found.

**Answer:** a server-side check can gate PRs and, as a side effect of "required checks", force direct pushes onto a branch. It can't look inside the photo before accepting a push, and it can't stop exposure.

## 4. Tooling, tested

Checked on this session's cloud VM (Ubuntu 24.04.4) on 2026-10-01:

| Tool | Cloud VM | `ubuntu-24.04` runner |
|---|---|---|
| exiftool | absent | not in inventory |
| ImageMagick | **6.9.12-98 present**; lists HEIC and AVIF as readable, JPEG read/write (a real HEIC file wasn't tried) | not in inventory |
| Python 3 | 3.11.15, no Pillow | 3.12.3 + pip |
| `sips`, `jpegtran`, `exiv2` | absent | not in inventory |

An environment's image can change, so don't rely on ImageMagick being there. Python is the one common denominator.

**A stdlib-only checker is feasible.** I built a synthetic iPhone-style JPEG (EXIF orientation 6, GPS latitude and longitude) and tested:

- `identify -format '%[EXIF:GPSLatitude]'` reads the GPS tag. On a file with no EXIF it prints an "unknown image property" warning, which makes it awkward to script against.
- A throwaway ~50-line stdlib script walks the JPEG segments, flags the GPS IFD pointer in APP1/Exif (and `GPS` in an XMP segment), and reads width and height from the SOF marker. It flagged the GPS file, passed the clean and stripped ones, and scanned 1,000 copies of a 70 KB file in about 0.06 s.
- Caveat: the test file was synthetic, not a real iPhone photo. Real files can carry MakerNote, thumbnails and XMP, so the build ticket should test a few real ones.

**The strip step has a trap.** Orientation lives in EXIF, so stripping without applying it sideways-rotates portrait photos. On the test file, `convert in.jpg -strip -resize '1600x1600>'` gave 1600×1067 pixels, while `convert in.jpg -auto-orient -strip -resize '1600x1600>'` gave the upright 1067×1600. Always `-auto-orient` first. From memory and untested here: `-strip` also drops the ICC profile, and iPhone photos are usually Display P3, so check colours on a real photo.

## 5. One workflow for both checks?

Yes (this answers the comment on #14 from #10). One workflow file, triggered on `push` (all branches) and `pull_request`, with two jobs:

- `check-media`: read-only, always runs.
- `showcase`: `needs: check-media`, runs only for pushes to `main`, and is the only job with `contents: write`.

A commit pushed with the workflow's `GITHUB_TOKEN` doesn't start a new workflow run, so the Showcase commit can't loop or recurse into the check (*excerpt*, [Triggering a workflow][gh-trigger]). It only rewrites marker blocks in Markdown, so skipping the check on it is fine. Two details for the build: scan every commit in the pushed range, not just the tip, because a photo added and deleted within one push is still in history; and use a `concurrency` group so the Showcase commit can't race a phone push.

## Recommendation

1. **Write the check once.** A stdlib-only script (for example `scripts/check-media.py`) that fails on: not a real JPEG; any Exif or XMP GPS, or more strictly any Exif/XMP at all, since display copies should carry no metadata; long edge or file size over the limit. Thresholds are for the build ticket. #4's numbers (about 1600 px, a few hundred KB) suggest candidates.
2. **Run it from a GitHub Actions workflow as the authority**, as in §5. It's the only layer that sees every writer.
3. **Add a Claude Code hook** in `.claude/settings.json` that runs the script on `git commit` and `git push` and exits 2 on failure. This is the preventive layer for cloud sessions and laptop agents. It's a sketch, untested: `PreToolUse`, matcher `Bash`, `if: "Bash(git commit *)"` and `"Bash(git push *)"`.
4. **Local pre-commit is optional**: `.githooks/pre-commit` calling the same script, enabled per clone with `git config core.hooksPath .githooks`. Don't count it.
5. **Prevent on the phone at capture, not at commit.** The #4 Shortcut (resize, JPEG, no location) is the only thing that can stop a GPS photo before Working Copy pushes it. Importing straight from the Photos app into Working Copy bypasses it, and the Action then only catches it afterwards.
6. **Don't add a blocking ruleset on `main` yet.** It would force phone pushes through PRs or be bypassed by the owner, it would reject the Showcase Action's commits, and it can't undo exposure. Revisit it if there's ever a second writer, or if the owner chooses PR-only pushes from the phone. That is the owner's call.
7. **Write down the leak playbook now.** If the check fails on a pushed photo: assume the location is known; strip and rewrite the history; delete the branch or PR refs; and ask GitHub Support to purge cached views and PR references ([Removing sensitive data][gh-sensitive]). Prevention matters more than the cleanup, which is why steps 3 and 5 exist.

## What I couldn't verify

- Whether Working Copy runs hooks (its manual was blocked).
- Whether `github-actions` can be a ruleset bypass actor on a personal repo.
- That a required status check rejects a direct push, as described. This rests on excerpts of GitHub's docs, so confirm it on a throwaway branch before relying on it.
- Whether cloud sessions can push straight to `main` (also open in #6), and what the Trusted network level allows for `apt`/`pip`.
- Behaviour on real iPhone files: HEIC handling, the ICC profile, MakerNote and thumbnail EXIF.

[cc-hooks]: https://code.claude.com/docs/en/hooks
[cc-web]: https://code.claude.com/docs/en/claude-code-on-the-web
[libgit2-964]: https://github.com/libgit2/libgit2/issues/964
[gh-rulesets]: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets
[gh-protected]: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches
[gh-push-rules]: https://github.blog/changelog/2024-09-10-push-rules-are-now-generally-available-and-updates-to-custom-properties/
[gh-sensitive]: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository
[gh-trigger]: https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/trigger-a-workflow
