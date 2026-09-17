# How can AI agents on iPhone get WorkBook context?

Research for [#6](https://github.com/instant-coffee/Project-Betty/issues/6) (map: [#1](https://github.com/instant-coffee/Project-Betty/issues/1)). Researched 2026-09-16 against Anthropic's documentation (code.claude.com, support.claude.com, claude.com, platform.claude.com), OpenAI's (learn.chatgpt.com, with help.openai.com through a search index), GitHub's, the Agent Skills spec and Working Copy's manual. All sources were read on 2026-09-16. Several of these features are labelled research preview or beta, and this space changes monthly, so check the linked page before relying on a detail.

## The question

Away from the laptop, in a gravel lot or trailside, with an iPhone and some signal, can an AI agent:

1. **Read** the WorkBook: `CONTEXT.md`, `vehicle-profile.md`, the open **Case**, the **Walkthroughs**?
2. **Write** to it: append a round to a Case or add a **Build Log** entry, and get that change into git?
3. **Use the repo's skills** (today `.claude/skills/betty-diagnostics/`) as committed, not as a copy someone has to keep in step?
4. **Take a photo** of a notebook page or a multimeter reading and turn it into text in a record?

And whatever does that has to get along with **Working Copy**, the offline clone that commits without signal and pushes later.

One limit applies to every option below. **Cloud agents see GitHub, not the phone.** They never see the git-ignored `.env` (full VIN), the git-ignored private manuals, or commits still sitting unpushed in Working Copy.

## Answer in brief

**Use Claude Code cloud sessions, started from the Code tab of the Claude iOS app, as the WorkBook's phone agent.** Of the options I checked, it's the only one that does all four jobs with repo context:

- It clones the repo fresh, so it reads current files.
- It loads the committed `CLAUDE.md` and `.claude/skills/` exactly as Claude Code does on the laptop.
- It commits and pushes a branch you turn into a pull request.
- It accepts attachments.

It's included in Claude Pro and Max, with no separate compute charge ([Use Claude Code in the cloud][cc-web], [Configure cloud environments][cc-env], [Claude pricing][claude-pricing]).

There are two costs:

- **Writes go to a branch, not straight to `main`.** You merge the pull request from the phone before Working Copy sees the change.
- **You need signal for the whole exchange.**

Keep Working Copy for the no-signal case and as the manual editor. The habit is: push before starting a session, and pull after merging.

The other options fall short:

- **Chat surfaces are read-only snapshots.** That covers Claude Projects with GitHub sync and ChatGPT's GitHub app. They don't run repo skills and have no write path.
- **Codex can't be started from the ChatGPT iPhone app.** Mobile only drives a connected desktop computer.
- **GitHub Copilot's cloud agent is the strongest runner-up.** It's started from GitHub Mobile and reads `CLAUDE.md` and `.claude/skills`. But it adds a $10/month subscription and doesn't document photo input.
- **Working Copy's own Repository Agent** is the one agent that works on the local clone. It's promising but largely undocumented, so it's worth a look on the phone.

---

## 1. Claude Code cloud sessions (Code tab in the Claude iOS app)

A cloud session runs Claude Code on an Anthropic-managed VM. The phone is only the client: "The Claude app for iOS and Android is a client for Claude Code sessions rather than a place where code runs" ([Claude Code on mobile][cc-mobile]). "From the Code tab, select a repository and branch, describe the task, and submit it" ([Claude Code on mobile][cc-mobile]). The docs list "Code questions and exploration: understand a codebase … without a local checkout" as a supported use, so it's not only for code changes ([Get started in the cloud][cc-quick]).

| Question | Answer | Source |
|---|---|---|
| Reads repo files? | **Yes, the whole repo.** "Cloud sessions start from a fresh clone of your repository. Anything you commit to the repo is available." You pick the branch. | [Configure cloud environments][cc-env], [Get started in the cloud][cc-quick] |
| Writes / commits? | **Yes, to a branch.** "When Claude reaches a stopping point, it pushes its branch to GitHub. You review the diff, leave inline comments, create a PR." "Each task gets its own session and its own branch." The GitHub proxy enforces: "`git push` works only against the session's current working branch." | [Get started in the cloud][cc-quick], [Configure cloud environments][cc-env] |
| Uses repo `.claude/skills`? | **Yes.** The "What carries over" table marks the repo's `CLAUDE.md`, `.claude/settings.json` hooks and `.claude/skills/` as available ("Part of the clone"). User-level `~/.claude/` is not. "Cloud sessions additionally load project skills committed to the cloned repository's `.claude/skills/`." They also load skills enabled on your claude.ai account. | [Configure cloud environments][cc-env], [Skills][cc-skills] |
| Accepts a photo? | **Documented, but not spelled out for the iPhone Code tab.** "An attachment to a cloud session is saved in that session's own cloud environment." Changelog v2.1.239 (21 Aug 2026): "Remote sessions: images uploaded from mobile now include their saved file path, so Claude can copy them into files it creates." The mobile page describes photo attachments in detail only for Remote Control. **Attaching a photo to a cloud session from the Code tab is implied, not stated: verify on the phone.** | [.claude directory][cc-dir], [Changelog][cc-changelog], [Claude Code on mobile][cc-mobile] |
| Plan / cost | "Cloud sessions are in research preview for Pro, Max, and Team users." Pro is $20/month billed monthly or $17/month billed annually. Claude Code isn't on Free. "Cloud sessions share rate limits with all other Claude and Claude Code usage within your account … There is no separate compute charge for the cloud VM." | [Use Claude Code in the cloud][cc-web], [Claude pricing][claude-pricing] |
| iPhone | Native app: the **Code** tab, or open `claude.ai/code/new` on the phone. On mobile, "cloud sessions offer Accept edits, Plan, and Auto in the mode dropdown." | [Claude Code on mobile][cc-mobile] |
| Public vs private | "A session can clone any public repository, but can work in a private repository only when the Claude GitHub App is installed on it." The WorkBook is public. **Whether pushes to a public repo work without the App installed is unverified.** Install the App on the repo to be safe (it also enables Auto-fix). | [Get started in the cloud][cc-quick] |

**Gotchas**

- **Branch-only writes.** Nothing reaches `main` until you merge the PR. The routines doc describes `claude/`-prefixed branches, and pushes to other branches checked against branch protection. That page covers scheduled routines, not interactive sessions ([Routines][cc-routines]). **Whether an interactive session started on `main` can commit straight to `main` is unverified.**
- **Sessions expire.** "Cloud sessions stop after a period of inactivity and the session's VM is reclaimed." Reopening restores the conversation, but "background work that was still running … isn't restored" ([Use Claude Code in the cloud][cc-web]). A Case spread over several days works as one long session, as long as each exchange finishes before you walk away.
- **No photo tooling pre-installed.** The installed-tools list has Python 3 with pip, git, gh and jq, but no exiftool or ImageMagick. The Default "Trusted" network reaches PyPI, so a setup script could install Pillow ([Configure cloud environments][cc-env]).
- **Environment variables aren't secret.** "Anyone who uses the environment can read its environment variables and setup script." Don't put the VIN there ([Configure cloud environments][cc-env]).
- **Unpushed Working Copy commits are invisible.** The docs give the same warning for `--cloud`: "clones your current directory's GitHub remote … not your local checkout, so push first if you have local commits" ([Use Claude Code in the cloud][cc-web]).
- **Research preview.** Behaviour and plan coverage may change.

## 2. Claude iOS app: chat, Projects, GitHub integration, skills, connectors

### 2a. GitHub integration and Projects

In a chat: "+" → **Add from GitHub** → pick files and folders. In a Project: the knowledge "+" → **GitHub** → pick a repo, files and folders. "You can use the 'Sync' icon to ensure you're working with the most up-to-date version." Only files are synced: "Only files (names and contents) in a repo on a specific branch are synced. We do not retrieve commit history, PRs, or other metadata" ([Use the GitHub integration][sc-github]). It's "available on all plans including Free" ([Claude Docs: GitHub integration][cl-github]). Projects are available to everyone, with Free limited to five, and paid plans switch to RAG when knowledge approaches the context limit ([Create and manage projects][sc-projects], [RAG for projects][sc-rag]).

| Question | Answer |
|---|---|
| Reads repo files? | **A snapshot of the files you select**, as of the last manual **Sync now**. It can go stale the moment you commit from Working Copy or a cloud session. |
| Writes / commits? | **No.** Nothing in the integration writes back. |
| Uses repo `.claude/skills`? | **No.** Skills in chat are uploaded as a ZIP under Customize > Skills. They need "Code execution and file creation" turned on, are "private to your individual account", and don't come from the repo ([Use skills in Claude][sc-skills]). Anthropic's platform docs: "Custom Skills do not sync across surfaces" ([Agent Skills overview][pl-skills]). |
| Accepts a photo? | **Yes.** The widget has a camera button, and the **Analyze Photo with Claude** control works from Control Center or the Lock Screen (iOS 18+) ([App intents, shortcuts and widgets on iOS][sc-ios-intents]). |
| iPhone | The help articles describe the GitHub picker and **Sync now** without naming a platform. **Whether the iOS app offers "Add from GitHub" and "Sync now" is unverified.** Plan to set the Project up on a laptop. Projects themselves "are available on every surface" ([Cowork on web, desktop and mobile][sc-cowork]). |
| Public vs private | Public by URL. Private needs the Claude GitHub App ([Use the GitHub integration][sc-github]). |

**Skill description limits disagree.** The Help Center says the description is "200 characters maximum" ([How to create custom skills][sc-skills-create]). Anthropic's platform docs and the open spec both say 1024 ([Agent Skills overview][pl-skills], [Agent Skills specification][agentskills]). The description in `betty-diagnostics` is **852 characters**. **Whether claude.ai accepts it as uploaded is unverified.**

**Double-loading.** If the same skill is both uploaded to claude.ai and committed to the repo, cloud sessions load both. A name clash resolves to the repo copy, and "the synced skill still runs as `/anthropic-skills:<name>`" ([Skills][cc-skills]). Two copies of the diagnostics skill would drift apart, so don't upload it.

### 2b. Connectors (remote MCP)

Web connectors work in the mobile apps, and "installing connectors on mobile is currently in beta" ([Use connectors][sc-connectors]). Custom connectors connect from Anthropic's cloud "across every Claude client, including … the mobile apps" ([Custom connectors using remote MCP][sc-custom]). None of that gives write access to the WorkBook:

- **No GitHub entry in Claude's Connectors Directory.** On 2026-09-16, `claude.com/connectors/github` redirected to the directory index. None of its 844 listings is GitHub ([Connectors directory][cl-directory]). The "GitHub" in Claude's docs is the read-only file sync in 2a.
- **GitHub's own remote MCP server can't be added as a custom connector.** GitHub's install guide: "the GitHub remote MCP server requires OAuth authentication through a registered GitHub App (or OAuth App), which is not currently supported. Use the local Docker setup instead" ([github-mcp-server: install in Claude][gh-mcp-claude]). A local Docker server can't run on an iPhone. The custom-connector form does accept an OAuth Client ID and Secret, but **whether that gets around it is unverified**, and I wouldn't build on it.

### 2c. Files, Shortcuts and Cowork

- **Chat can hand you a file.** "Users on all paid plans can access these features on Claude for iOS or Android" ([Release notes][sc-relnotes], [Create and edit files][sc-files]). A chat can produce a Markdown round or Build Log entry. You then share it into Working Copy, which saves it to a folder and can "Commit this change immediately and Push" (Working Copy manual §6.2 [wc-manual]). That's a human-carried write path that works on any plan, but the chat only has whatever context you gave it.
- **Shortcuts.** The **Ask Claude** intent works in Shortcuts ([App intents][sc-ios-intents]). Working Copy's Shortcuts actions include **Write Repository File**, **Commit Repository** and **Push Repository** (manual §6.7 [wc-manual]). Together they could make a one-tap text-to-commit pipe. **Whether Ask Claude accepts an image input is unverified.**
- **Cowork in the cloud** is in beta on mobile for Pro, Max and Team. It supports skills, plugins, connectors and projects on mobile. Local files are reachable only "while the desktop app is open on that computer and the session was started on desktop" ([Cowork on web, desktop and mobile][sc-cowork]). It documents no GitHub repo access, so it doesn't reach the WorkBook beyond project knowledge. The article also notes Cowork and chat are merging into one Claude, which is rolling out to Pro and Max.

## 3. ChatGPT: the GitHub app

help.openai.com returned 403 to every fetch, so the quotes in this section come from the search index's copy of the Help Center pages (see §9).

| Question | Answer |
|---|---|
| Reads repo files? | **Yes, on demand.** "ChatGPT can retrieve permitted repository content on demand, including code, README files, and other documentation. This live access does not create a synced GitHub index." That makes it fresher than a Claude Project snapshot ([Connecting GitHub to ChatGPT][oa-github]). |
| Writes / commits? | **No.** "The GitHub app in ChatGPT only lets you read from your repositories to analyze and search your code. To generate, edit, and push code directly to GitHub, use Codex" ([Connecting GitHub to ChatGPT][oa-github]). |
| Limited to deep research? | **Sometimes.** "An account may be able to use GitHub in deep research or agent mode even when GitHub is not available in standard chat," and "deep research only uses read actions from connected apps" ([Connecting GitHub to ChatGPT][oa-github]). |
| Uses repo `.claude/skills`? | **No.** ChatGPT on mobile runs skills bundled in plugins: "Standalone skills are available in the ChatGPT desktop app, Codex CLI, and IDE extension. Skills bundled in plugins are also available in Chat and Work across ChatGPT on the web, desktop, and mobile" ([Build skills][oa-skills]). Nothing documents chat loading skill folders from a connected repo. |
| Accepts a photo? | Chat takes images. The docs I could open describe the web composer ("Attach, paste, or drag an image") ([Image inputs][oa-images]). **The iOS app's photo upload wasn't verified from a primary source.** |

## 4. ChatGPT: Codex

Codex cloud clones a repo into a container, runs the task and shows a diff you can open as a PR. That's the write path the GitHub app points to.

| Question | Answer |
|---|---|
| Reads repo files? | **Yes.** "Codex creates a container and checks out your repo at the selected branch or commit SHA" ([Cloud environments][oa-env]). |
| Writes / commits? | **Yes, via PR.** "When the agent finishes, it shows its answer and a diff of any files it changed. You can open a PR" ([Cloud environments][oa-env]). |
| Reads repo instructions and skills? | **`AGENTS.md`, not `CLAUDE.md`** ([Custom instructions with AGENTS.md][oa-agents]). Skills live under **`.agents/skills`, not `.claude/skills`** ([Build skills][oa-skills]). The skills page lists standalone skills for the desktop app, CLI and IDE, not cloud chats, so **whether Codex cloud loads repo skills is unverified.** |
| Accepts a photo? | Yes in the web composer ([Image inputs][oa-images]). |
| **iPhone** | **Not as a cloud agent.** "Codex is not selectable on mobile, but you can access supported desktop Codex chats from the Remote tab" ([Using Codex with your ChatGPT plan][oa-codex-plan]). Remote "runs each task on your connected computer … Keep your computer awake and online" ([Codex Remote][oa-remote]). The pricing page lists "Codex … on iOS" under Plus, which fits the Remote experience ([Pricing][oa-pricing]). **Driving `chatgpt.com/codex` from iPhone Safari is unverified.** |
| Plan / cost | Cloud needs "a Plus, Pro, Business, Enterprise, or Edu plan … a connected GitHub account, and at least one environment" ([Use Codex in Slack][oa-slack]). Plus is $20/month ([Pricing][oa-pricing]). |
| Gotchas | "Codex blocks internet access during the agent phase" by default ([Agent internet access][oa-internet]). Getting it to use `betty-diagnostics` would mean an `AGENTS.md` and a copy or symlink under `.agents/skills` (Codex "supports symlinked skill folders" [Build skills][oa-skills]). |

## 5. Other options that fit

### GitHub Copilot cloud agent (GitHub Mobile)

- **Starting a task.** In GitHub Mobile: "tap the Copilot icon … then tap New Session," then pick a repo and base branch. "Copilot will start a new session, work on the task, and push any code changes," and it can open a PR ([Copilot cloud agent on GitHub Mobile][gh-copilot-mobile]).
- **Reads the WorkBook's current layout without changes.** Project skills are discovered in "`.github/skills`, `.claude/skills`, or `.agents/skills`" ([About agent skills][gh-copilot-skills]). Instructions can be "a single `CLAUDE.md` or `GEMINI.md` file stored in the root of the repository" as an alternative to `AGENTS.md` ([Repository custom instructions][gh-copilot-instr]).
- **Cost.** Copilot Pro is $10/month and Pro+ is $39. Copilot Free doesn't include the cloud agent ([Copilot plans][gh-copilot-plans]).
- **Photos.** Adding images to a mobile prompt isn't documented ([Copilot cloud agent on GitHub Mobile][gh-copilot-mobile]).

GitHub's docs pages were read through a fetch tool that extracts text. The quoted phrases are as returned.

### Working Copy's integrations

- **Repository Agent** (manual §3.5 [wc-manual]): "an AI assistant that you interact with through conversation. Ask it to browse files, search content, read and modify files, commit, pull and push. Destructive operations like modifying files or pushing require your confirmation. It works with external LLM services including OpenAI, Anthropic, Google and Ollama using your own API keys. AI features are opt-in per repository." It's the **only agent here that works on the phone's own clone**, so it sees local unpushed commits and never waits on a merge. It still needs a network connection to reach the model, and usage is billed to whichever provider's API key you supply. **Unverified:** whether it reads `CLAUDE.md` or `.claude/skills`, whether it takes photos, and whether it needs the Pro unlock.
- **Plumbing for other apps.** "All repositories in Working Copy can be accessed in the Files app … Other applications are allowed to read and make changes to files" (§6). The share sheet saves a file into a repo with optional commit and push (§6.2). Shortcuts actions read, write, commit, pull, push and sync (§6.7) [wc-manual]. x-callback-url has `read`, `write`, `commit` and `push` commands, guarded by a key ([URL schemes][wc-url]). These let a chat app hand text to the clone without any cloud agent.
- **Privacy note.** Working Copy's *AI suggested commit messages* "require sending staged differences to OpenAI" (§1.3 [wc-manual]).

### Remote Control and Dispatch (not a trailside answer)

Both drive a Claude Code session **on the laptop**, which "needs to stay on with Claude Code or the Desktop app running" ([Claude Code on mobile][cc-mobile]). They see everything the laptop sees, including `.env` and the manuals. Attached photos reach the local session and are saved "under `~/.claude/uploads/`" ([Claude Code on mobile][cc-mobile]). Useful only if the laptop is left running at home. Dispatch requires Pro or Max.

---

## 6. Comparison

| Option | Reads WorkBook | Writes / commits | Repo `.claude/skills` | Photo in | On iPhone | Cost to add | Main gotcha |
|---|---|---|---|---|---|---|---|
| **Claude Code cloud session (Code tab)** | **Yes, fresh clone** | **Yes, branch + PR** | **Yes, plus `CLAUDE.md` and hooks** | Implied; verify | Native app | Included in Claude Pro/Max ($20/mo) | Merge before `main` changes; needs signal |
| Claude chat + Project + GitHub sync | Selected files, snapshot | No (share a file into Working Copy by hand) | No (ZIP upload, drifts) | **Yes** | App; GitHub picker on iOS unverified | Free–Pro | Stale until **Sync now** |
| Claude connectors / Cowork | Not the repo | No | No | Yes | App (beta) | Pro/Max | No GitHub connector exists |
| ChatGPT GitHub app | Yes, on demand | **No** | No | Yes (web doc) | App | Varies by plan | May be deep-research/agent only |
| Codex cloud | Yes | Yes, PR | No (`AGENTS.md`, `.agents/skills`) | Yes (web) | **Not selectable on mobile** | ChatGPT Plus $20/mo | Mobile only drives a desktop |
| Copilot cloud agent (GitHub Mobile) | Yes | Yes, branch + PR | **Yes** (and root `CLAUDE.md`) | Not documented | Native app | Copilot Pro $10/mo | Another subscription; no photo |
| Working Copy Repository Agent | Local clone, incl. unpushed | Yes, local commit + push | Unverified | Unverified | Native app | API tokens (own key) | Largely undocumented |
| Remote Control / Dispatch | Everything on laptop | Yes | Yes | Yes | App | Pro/Max | Laptop must be on |

## 7. Coexisting with Working Copy

Cloud agents and Working Copy write to the same `main` from two directions. A cloud agent writes on GitHub, through a branch, a PR and a merge. Working Copy writes on the phone, as local commits pushed later. Git reconciles them when Working Copy pulls. Pulling is "Fetch followed by a Merge" and "conflicts … can be resolved by manually editing files" (manual §1.4 [wc-manual]).

- **Push before you start a session.** Otherwise the session works from an older `main`.
- **Pull after you merge.** Working Copy doesn't see the agent's work until it fetches and merges.
- **Cases are the conflict hotspot.** A Case is one file that grows by appending rounds. An offline round typed in Working Copy and a cloud-agent round appended to the same file will both land at the end of the file and conflict. Rule: **one writer per Case between syncs.**
- **Build Log entries as one file per entry never collide.** Two new files can't conflict. That's worth weighing in the Build Log format decision.
- **Auto-Sync** "does all of this for you, committing, pulling and pushing whenever changes are detected," and "merge conflicts pause syncing until you resolve them inside Working Copy." It's a Pro feature. Repositories can be set to rebase instead of merge (Pro) (manual §1.4 [wc-manual]). Auto-Sync narrows the window for divergence when there is signal, but it commits with a generic message.

## 8. Gotchas worth carrying into other tickets

1. **Cloud agents see only what's on GitHub.** `.env`, the private manuals and unpushed commits don't exist for them. Skills and Walkthroughs used from the phone must still work without them, and should say so rather than silently guessing a VIN or quoting a manual they can't open.
2. **`docs/projectContext/ignition-context.md` is still tracked on `main`**, even though `.gitignore` now lists `docs/projectContext/*`. Ignore rules don't untrack files already committed (checked with `git ls-files` on 078e33c). Cloud agents and the public both see it. If it was meant to be private, it needs `git rm --cached`, and it stays in history either way.
3. **Only Claude Code and Copilot read `CLAUDE.md` and `.claude/skills` as they stand.** Codex wants `AGENTS.md` and `.agents/skills`. Claude Code's own advice when both exist is a `CLAUDE.md` that imports `@AGENTS.md` ([Memory][cc-memory]).
4. **Skill descriptions:** the claude.ai Help Center says 200 characters max, while the platform docs and spec say 1024. `betty-diagnostics` has 852.
5. **The photo metadata check has to live where every writer passes.** Claude Code cloud sessions run hooks from `.claude/settings.json`, but Copilot, Codex, Working Copy and a Claude chat hand-off don't. A server-side check on GitHub would cover them all. **Whether Working Copy runs git hooks is unverified.**
6. **The cloud VM has no exiftool or ImageMagick**, so committing a resized, stripped JPEG from a cloud session needs a setup script or a hook that installs tooling.
7. **Features are research preview or beta:** Claude Code cloud sessions, mobile connector install, Cowork on mobile, Codex on mobile.

## 9. Implications for open decisions

**"What agent context and skills does the WorkBook ship with?"**

- Committed `CLAUDE.md` plus `.claude/skills/` is the one format that reaches a phone agent (Claude Code cloud, and Copilot) with no extra step. Keep skills in the repo. Don't also upload them to claude.ai.
- If Codex ever matters, put the shared instructions in `AGENTS.md` and have `CLAUDE.md` import it.
- Skills need a no-`.env`, no-manuals mode, and should assume their output lands on a branch pending review.
- Consider keeping descriptions short enough for claude.ai if chat-side use is wanted.

**The capture workflow** (notebook photo or chat hand-off → Build Log entry or Case update)

- **With signal:** the Claude Code cloud session is both the agent and the device. The flow is: attach the photo, have it transcribed into the Case round or Build Log entry, review the diff on the phone, create the PR and merge.
- **Without signal:** the "hand-off" is just the photo in the library, plus any typed note committed in Working Copy. Transcription waits for signal.
- **Keep the notebook photo out of the commit** until the media pipeline (resize, strip, metadata check) exists somewhere cloud writes pass through. The transcription is the record, and the original stays in the iPhone library.
- **The chat hand-off** (Claude or ChatGPT chat writes a paste-ready round, which you share into Working Copy) works on any plan and commits offline. But it has no skill and only the context you paste in. It's a fallback, not the main path.

---

## Recommendation

1. **Confirm the Claude plan is Pro or Max.** Cloud sessions aren't on Free. If Claude Code already runs on the laptop under Pro or Max, there's nothing new to buy ([Claude pricing][claude-pricing]).
2. **On the laptop, set up once:** connect GitHub at `claude.ai/code`, install the Claude GitHub App on `instant-coffee/Project-Betty`, and keep the **Default** environment (Trusted network) ([Get started in the cloud][cc-quick]). Add no environment variables with private facts.
3. **Ask questions trailside:** Claude app → **Code** → Project-Betty on `main`, in **Plan** mode so nothing gets edited. The session reads `CONTEXT.md`, `vehicle-profile.md`, the Case and the `betty-diagnostics` skill as committed.
4. **Append to a Case or write a Build Log entry:** the same session in **Accept edits**. Review the diff, **Create PR** and merge.
5. **Capture notebook pages:** attach the photo in the session and have it transcribed into the record. Leave the image out of the commit for now.
6. **Working Copy habit:** push before starting a session, pull after merging, and one writer per Case between syncs. With no signal, write in Working Copy and let the cloud agent pick it up after the next push.
7. **Skip for now:** Claude Project GitHub sync (stale, read-only, no repo skills), ChatGPT's GitHub app (read-only), and Codex (not startable from the iPhone app).
8. **Revisit if** photos can't be attached from the Code tab, sessions are unusable on weak signal, or merging every change becomes friction. Then try Working Copy's Repository Agent (on-device clone, your own Anthropic key) and Copilot on GitHub Mobile, in that order.

### Verify on the phone before relying on this

- A photo attaches to a cloud session from the Code tab, and Claude can read it.
- The `betty-diagnostics` skill triggers in a cloud session.
- What branch a session started on `main` commits to, and whether it can push to `main` directly.
- **Create PR** and merge both work from the phone.
- Session start and response are tolerable on one or two bars of LTE.
- Whether "Add from GitHub" and "Sync now" exist in the Claude iOS app.
- Whether Working Copy's Repository Agent reads `CLAUDE.md` or skills, takes photos, and needs Pro.

---

## How the sources were read

- **code.claude.com:** the docs' Markdown pages and `llms-full.txt`, fetched directly and searched.
- **support.claude.com:** article HTML fetched directly; last-modified dates range from March to September 2026.
- **claude.com and platform.claude.com:** fetched directly or through a fetch tool.
- **learn.chatgpt.com:** OpenAI's Codex and ChatGPT docs, where `developers.openai.com/codex/*` now redirects. Read as Markdown twins (`/docs/<page>.md`) and `llms-full.txt`.
- **help.openai.com and openai.com:** returned **HTTP 403** (bot challenge) to both direct fetch and the fetch tool. Claims from those pages ([oa-github], [oa-codex-plan]) come from the web search index's excerpts of the pages and are quoted as the index returned them. Treat them as one step removed.
- **docs.github.com, agentskills.io and the `github/github-mcp-server` repo:** read through a fetch tool that returns extracted text. Quotes are as returned.
- **workingcopyapp.com:** manual and URL-scheme pages fetched directly. The manual is a single page, so section numbers are given.

[cc-web]: https://code.claude.com/docs/en/claude-code-on-the-web
[cc-quick]: https://code.claude.com/docs/en/web-quickstart
[cc-mobile]: https://code.claude.com/docs/en/mobile
[cc-env]: https://code.claude.com/docs/en/cloud-environments
[cc-skills]: https://code.claude.com/docs/en/skills
[cc-dir]: https://code.claude.com/docs/en/claude-directory
[cc-changelog]: https://code.claude.com/docs/en/changelog
[cc-routines]: https://code.claude.com/docs/en/routines
[cc-memory]: https://code.claude.com/docs/en/memory
[claude-pricing]: https://claude.com/pricing
[sc-github]: https://support.claude.com/en/articles/10167454-use-the-github-integration
[cl-github]: https://claude.com/docs/connectors/github
[sc-projects]: https://support.claude.com/en/articles/9519177-how-can-i-create-and-manage-projects
[sc-rag]: https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects
[sc-files]: https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude
[sc-skills]: https://support.claude.com/en/articles/12512180-use-skills-in-claude
[sc-skills-create]: https://support.claude.com/en/articles/12512198-how-to-create-custom-skills
[pl-skills]: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
[agentskills]: https://agentskills.io/specification
[sc-ios-intents]: https://support.claude.com/en/articles/10263469-use-claude-app-intents-shortcuts-and-widgets-on-ios
[sc-connectors]: https://support.claude.com/en/articles/10168395-set-up-claude-integrations
[sc-custom]: https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp
[cl-directory]: https://claude.com/connectors
[gh-mcp-claude]: https://github.com/github/github-mcp-server/blob/main/docs/installation-guides/install-claude.md
[sc-relnotes]: https://support.claude.com/en/articles/12138966-release-notes
[sc-cowork]: https://support.claude.com/en/articles/15520349-use-claude-cowork-on-web-desktop-and-mobile
[oa-github]: https://help.openai.com/en/articles/11145903-connecting-github-to-chatgpt
[oa-codex-plan]: https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan
[oa-skills]: https://learn.chatgpt.com/docs/build-skills
[oa-images]: https://learn.chatgpt.com/docs/image-inputs
[oa-env]: https://learn.chatgpt.com/docs/environments/cloud-environment
[oa-agents]: https://learn.chatgpt.com/docs/agent-configuration/agents-md
[oa-remote]: https://learn.chatgpt.com/docs/remote
[oa-pricing]: https://learn.chatgpt.com/docs/pricing
[oa-slack]: https://learn.chatgpt.com/docs/third-party/slack
[oa-internet]: https://learn.chatgpt.com/docs/cloud/internet-access
[gh-copilot-mobile]: https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-mobile
[gh-copilot-skills]: https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
[gh-copilot-instr]: https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions
[gh-copilot-plans]: https://docs.github.com/en/copilot/get-started/plans
[wc-manual]: https://workingcopyapp.com/manual.html
[wc-url]: https://workingcopyapp.com/url-schemes.html
