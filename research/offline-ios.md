# What are the options for offline access to WorkBook content on iOS?

Research for [#5](https://github.com/instant-coffee/Project-Betty/issues/5) (map: [#1](https://github.com/instant-coffee/Project-Betty/issues/1)). Researched 2026-09-17 against Working Copy's, Obsidian's, Apple's, WebKit's and GitHub's own documentation. Prices, storage caps and platform behaviour change, so check the linked page before relying on a number.

## The question

Standing in a gravel lot or trailside with no signal, an iPhone, and Betty half apart, what does it take to have the **Walkthrough** for the job in hand — plus the **References** and the private manuals behind it — readable on the phone, and to get a **Build Log** entry or a **Case** update written there back into the WorkBook once signal returns?

Two piles of content have to travel, and they travel differently:

| Pile | What it is | Where it lives | Can git carry it? |
|---|---|---|---|
| **The WorkBook** | Walkthroughs, Cases, Build Log, References, Plans-as-notes, and the resized ~1600 px JPEGs committed alongside them | The public repo | Yes. This is the whole point of Markdown being the durable store |
| **The private manuals** | RAVE, parts catalogues, scanned workshop PDFs | A git-ignored private location, by standing decision | **No. Never.** They are copyrighted and the repo is public |

So the honest answer to a ticket that asks for one tool is that **one tool carries the WorkBook and a second, dumber thing carries the manuals**. Any option that claims to do both is either putting copyrighted PDFs somewhere public or quietly not carrying them at all.

Offline access is a boon, not vital. That sets the bar: the winner should be something you set up once in an afternoon and then forget about, not a build pipeline you have to feed.

## Answer in brief

**Use Working Copy as the primary tool.** It clones the whole WorkBook onto the phone, where it stays — no cloud, no eviction, no signal needed. It previews Markdown, and its preview resolves relative links to files inside the repository without an internet connection ([Text Editing][wc-edit]), which is exactly the `![Rear crossmember](media/2026-09-14-crossmember.jpg)` acid test. You can edit and commit while offline and push once you get back online, because commit and push are distinct actions ([Committing or reverting][wc-commit]). It is a free download; the only thing you must buy is the **$35.99 one-time Pro unlock**, which is what turns on pushing and lifts the five-repository cap ([How to purchase][wc-purchase]).

**The unavoidable second thing** is an ordinary iCloud Drive folder for the private manuals, with the PDFs marked **Keep Downloaded** in the Files app so iOS is not allowed to evict them ([Files basics][apple-files-basics]). That is not a compromise; it is the only lawful home for content that can never enter a public repo.

Obsidian is the strongest runner-up and the right answer for someone who wants a *writing* app rather than a *git* app — but on iOS it costs more moving parts for the same result, and Obsidian Git on mobile is described by its own author as "**very unstable**" ([obsidian-git README][og-readme]). A GitHub Pages PWA is read-only, cannot host the manuals at all, and its offline cache is at the mercy of WebKit's storage policy. Exported PDFs are a one-way street and not worth building the strategy on.

---

## 1. Working Copy

A native git client for iOS. The repository is cloned to the device and lives there.

### Cost

| Item | Cost | Source |
|---|---|---|
| App download, clone, read, edit, commit locally | Free | [How to purchase][wc-purchase] |
| Free tier limits | Up to 5 repositories; no pushing | [How to purchase][wc-purchase] |
| Trial | 10 days of Pro features for new users | [How to purchase][wc-purchase] |
| **Pro unlock** (one-time IAP) | **$35.99 USD** | [How to purchase][wc-purchase] |
| Students | Free via the GitHub Student Developer Pack | [How to purchase][wc-purchase] |

The unlock is a one-time purchase remembered by Apple, restorable on other devices under the same Apple ID. Features unlocked, plus any Pro features added in the following 12 months, are permanently available; Pro features introduced after that year are locked until you buy an upgrade, though the app keeps getting fixes and improvements regardless ([How to purchase][wc-purchase]).

Pro covers, among others: **Push to remote**, Unlimited repositories, **Linked external repositories**, External Preview, Repository Folders, Pull Request creation, Rebase from remotes, Stash, Commit Amend, SSH commit signing, Secure Enclave SSH keys ([How to purchase][wc-purchase]).

**Time cost (estimate, mine, not from a source):** twenty minutes. Install, sign in to GitHub, pick the repo from the list, clone. Working Copy has "special support for GitHub, BitBucket, GitLab and other hosting providers to list your available repositories, making cloning as simple as picking a repository and tapping clone" ([Cloning repositories][wc-clone]).

### Choosing "selected" content

Whole repository. **Working Copy's manual documents no sparse checkout, partial clone or per-folder selection** — cloning is described as duplicating a repository from a remote by URL ([Cloning repositories][wc-clone]). If there is a finer control, the vendor does not document it; do not assume it exists.

That matters less than it sounds. Working from the numbers already settled in the media ticket — roughly 350 KB per resized JPEG, so about 350 MB for a thousand photos — the whole WorkBook is a phone-sized thing. **Sizing estimate (mine, not from a source):** already-compressed JPEGs barely delta-compress, so a clone's `.git` will be roughly as large as its working tree; budget about **2× the repo size on the phone**, so on the order of 700 MB once there are a thousand photos, and a couple of hundred MB for the next few years. "Select everything" is a fine answer at that scale.

### Syncing back

This is where it wins. Commits are local. Working Copy's manual states you can commit while offline and push once you get back online, because commit and push are distinct actions; to push you tap and hold a repository and select Push, and pushing needs the Pro unlock ([Committing or reverting][wc-commit]). A Build Log entry written in the gravel lot is a real commit with a real timestamp, sitting on the phone until the bars come back.

### Markdown and relative images

Preview mode is enabled with the button in the upper-right corner and is available for **HTML, JavaScript, Markdown, org-mode, AsciiDoc and Jupyter notebook** files. For previews, "relative links to images, javascript and stylesheets resolve to files inside repository and will work without Internet connection", while external assets need a connection ([Text Editing][wc-edit]).

**Caveat, stated plainly:** Working Copy's manual spells the relative-link guarantee out for *HTML* preview. Markdown is in the same preview feature and is rendered through the same machinery, and in practice relative image paths inside the repo resolve, but **the vendor does not say so in as many words for Markdown**. Clone the repo and open one Walkthrough with an image before you commit to this. It is a two-minute test.

### Offline durability

Repositories are stored on the device by Working Copy itself, not streamed from a cloud, so there is nothing to evict. They are also exposed to the rest of iOS: all repositories can be accessed in the Files app and by third-party apps through the document picker, once Working Copy is enabled as a Location in Files. Other apps may read and change files, and those changes "stay inside Working Copy" for you to review and commit ([Files synchronisation][wc-files]).

Apple's own behaviour helps here too: iOS's automatic app offloading "frees up storage used by the app, but keeps its documents and data" ([Manage storage on iPhone][apple-storage]). Even an offloaded Working Copy keeps the clone.

---

## 2. Obsidian

### Cost

| Item | Cost | Source |
|---|---|---|
| Obsidian app (desktop and mobile) | Free — "Obsidian is free to use and is 100% user-supported" | [Commercial license][obs-license] |
| Commercial licence | Only for organisations; irrelevant to a personal restomod | [Commercial license][obs-license] |
| Obsidian Sync Standard | **$8/month billed annually, $10/month billed monthly** | [Obsidian pricing][obs-pricing] |
| Obsidian Git plugin | Free (community plugin) | [obsidian-git README][og-readme] |
| iCloud Drive | Whatever iCloud tier you already pay for | [Manage iCloud storage][apple-icloud-storage] |

Sync Standard caps: **1 synced vault, 5 MB maximum file size, 1 GB total storage, 1 month of version history**. Sync Plus raises those to 10 vaults, 200 MB per file and 10–100 GB, and storage is account-wide, with version history and attachments counting toward it ([Plans and storage limits][obs-plans]). **A WorkBook with a few hundred MB of JPEGs plus version history will eventually crowd the 1 GB Standard tier** — that is a real cost escalation, not a hypothetical.

### Where an Obsidian vault can live on iOS — the decisive question

Obsidian's own help documents exactly two locations on iPhone:

- **iCloud Drive:** create the vault in the app and "Toggle on **Store in iCloud**". The docs are emphatic that the vault must sit at `iCloud Drive/Obsidian/[Your Vault Name]` — "Vaults should be inside the **Obsidian** folder within iCloud Drive… If your vault is in a different location, you may experience syncing issues" ([Sync your notes across devices][obs-sync-notes]).
- **On the device:** "**iOS/iPadOS**: Store the vault **On My iPhone** or **On the Device**", and "On mobile devices, Obsidian operates in a sandboxed environment, meaning you cannot move vaults within the app like you can on desktop" ([Switch to Obsidian Sync][obs-switch]).

**Obsidian does not document opening a vault from an arbitrary third-party Files provider on iOS** — there is no mobile "Open folder as vault"; those instructions are written for the desktop ([Manage vaults][obs-vaults]). So you cannot point Obsidian at a folder inside Working Copy.

The combination works **the other way round**, and Working Copy documents it: "Linked external repositories is a pro feature… This can be used with apps such as Codea, iA Writer, **Obsidian**, Scriptable, Swift Playgrounds and other apps that store documents in iCloud Drive or On my Device." You tap **Link external directory**, pick the folder in the Files app, and "as you edit, commit, push and pull the changes are made to the original linked directory". Crucially: "Working Copy needs folder-level access which can only work with Files app locations that support picking folders such as iCloud Drive, On My Device or Secure ShellFish" ([External repositories][wc-external]). Obsidian's own help lists Working Copy under iPhone syncing and describes the same shape: clone in Working Copy, link it to the vault folder, commit and push from Working Copy ([Sync your notes across devices][obs-sync-notes]).

**So "Obsidian + Working Copy" is real — but it is Working Copy doing the git, with Obsidian as the editor on top, and it needs the same $35.99 Pro unlock.** That is a strictly larger setup than Working Copy alone, for a nicer editor.

### Obsidian Git on iOS

It exists and it is not recommended by its own maintainer:

> "The Git implementation on mobile is **very unstable**! I would not recommend using this plugin on mobile, but try other syncing services." ([obsidian-git README][og-readme])

It runs on [isomorphic-git][isogit], "a JavaScript-based re-implementation of Git — but it comes with serious limitations and issues. It is not possible for an Obsidian plugin to use a native Git installation on Android or iOS." Documented mobile limitations: **no SSH authentication; limited repo size because of memory restrictions; no rebase merge strategy; no submodules.** And a caution that Obsidian may "crash on clone/pull, create buffer overflow errors, run indefinitely… If that's the case for you, I have to admit this plugin won't work for you." The suggested mitigation for a large repo is to stage individual files rather than commit everything ([obsidian-git README][og-readme]).

A WorkBook carrying hundreds of megabytes of JPEGs is precisely the "large repo" case this warns about. **Do not build the offline story on Obsidian Git on the phone.**

### Choosing "selected" content

Obsidian is the only option here with genuinely finer-grained selection, via Obsidian Sync:

- **By file type.** Selective sync is on by default for Images, Audio, Videos and PDFs; you toggle `Sync all other types` to add more ([Sync settings and selective syncing][obs-selective]).
- **By folder.** Settings → Sync → **Excluded folders** → Manage, and the setting is configured per device ([Sync settings and selective syncing][obs-selective]).
- Files and folders beginning with `.` are treated as hidden and excluded, except `.obsidian` ([Sync settings and selective syncing][obs-selective]) — note that this means `.git` would not ride along on Obsidian Sync.

That is a real advantage if you ever wanted "Walkthroughs and Areas only, no photos" on the phone. It costs a subscription.

### Markdown and relative images

Obsidian renders images, and it accepts both syntaxes — but its **default is the wrong one for a public GitHub repo**:

> "By default, due to its more compact format, Obsidian generates links using the Wikilink format. If interoperability is important to you, you can disable Wikilinks and use Markdown links instead." Settings → Files and Links → disable **Use [[Wikilinks]]** ([Internal links][obs-links]); the setting's description is "Auto-generate Wikilinks for `[[links]]` and `![[images]]` instead of Markdown links and images" ([Settings][obs-settings]).

Obsidian's help documents `![[Engelbart.jpg]]` for vault images and a Markdown link for an **externally hosted** image ([Embed files][obs-embeds]). **It does not document relative-path Markdown embeds of vault files** such as `![Rear crossmember](media/2026-09-14-crossmember.jpg)`. They do work in practice, but since the vendor does not say so, test one before trusting it. Accepted formats include `.jpg`, `.jpeg`, `.png` and `.pdf` ([Accepted file formats][obs-formats]).

**The gotcha worth shouting about:** if you author in Obsidian with the default setting on, you will produce `![[photo.jpg]]` in files that github.com will render as literal text. The WorkBook would silently stop showing pictures on the website it exists to be. Turn Wikilinks off on day one.

### Offline durability

The vault is local either way — "Obsidian stores notes locally on your device so you always have access to them, even offline" ([Sync your notes across devices][obs-sync-notes]). But if you put the vault in iCloud Drive, Obsidian's own docs warn about eviction and tell you to keep it downloaded: "If services like OneDrive or iCloud offload files… Obsidian can't access them, causing sync issues. Mark your vault folder as **Always keep on this device** (OneDrive) or ensure **Keep Downloaded** is enabled (iCloud)" ([Sync your notes across devices][obs-sync-notes]). Storing the vault **On My iPhone** side-steps that entirely.

---

## 3. GitHub Pages as an offline-capable PWA

The idea: publish the WorkBook as a Pages site, add a service worker that caches it, add the site to the Home Screen, read it in a gravel lot.

### It cannot carry the manuals, at all

> "**GitHub Pages sites are publicly available on the internet, even if the repository for the site is private.** If you have sensitive data in your site's repository, you may want to remove the data before publishing." ([Securing your GitHub Pages site with HTTPS][gh-pages-https], quoting the shared warning reused across the Pages docs)

RAVE and the parts catalogues are copyrighted and the repo is public. There is no configuration of GitHub Pages that makes hosting them acceptable. This route covers, at best, half the problem.

### It is read-only

There is no path from a cached web page back to a commit. A Build Log entry written trailside on a PWA goes nowhere. That alone disqualifies it as *the* answer.

### Would the cache actually survive?

Partly — and better than its reputation, if you add it to the Home Screen.

- Service Workers and the Cache API arrived in Safari on iOS 11.3; the Cache API "allows storing fetch requests and responses persistently" and is the key API for offline support ([Workers at Your Service][wk-workers]).
- Pages serves over HTTPS, which service workers require: sites created after 15 June 2016 on `github.io` domains "are served over HTTPS automatically" ([Securing your GitHub Pages site with HTTPS][gh-pages-https]).
- **The seven-day rule.** ITP "delet[es] all of a website's script-writable storage after seven days of Safari use without user interaction on the site", covering Indexed DB, LocalStorage, Media keys, SessionStorage, and **Service Worker registrations and cache** ([Full Third-Party Cookie Blocking and More][wk-itp]).
- **Home Screen web apps are exempt from that rule.** WebKit states that "the first-party domain of home screen web applications is exempt from ITP's 7-day cap on all script-writable storage", meaning ITP always skips that domain in its website-data removal algorithm; and that home-screen web apps "are not part of Safari and thus have their own counter of days of use" ([Updates to Storage Policy][wk-storage], [Full Third-Party Cookie Blocking and More][wk-itp]).
- **Exempt is not immortal.** The same storage-policy post is explicit that eviction "can happen when exceeding the overall quota, when the system is under storage pressure, or when the site has not been interacted with by the user for some time", and that persistent mode is granted by heuristics — one of which is whether the site is opened as a Home Screen Web App ([Updates to Storage Policy][wk-storage]).

Apple's own (archived but still the clearest) statement of what a Home Screen web app is: in standalone mode "Safari is not used to display the web content — specifically, there is no browser URL text field at the top of the screen or button bar at the bottom", enabled with `<meta name="apple-mobile-web-app-capable" content="yes">`; and a launch image "is especially useful when your web application is offline" ([Configuring Web Applications][apple-webapps], last updated 2016-12-12).

### Verdict

Genuinely offline-capable if you add it to the Home Screen, but you would be **hand-writing a service worker and a cache manifest** — Jekyll will not do it for you — precaching a few hundred megabytes of JPEGs into a quota you do not control, for a read-only copy of half the content. Against "a boon, not vital", that is over-engineering. Keep GitHub Pages on the roadmap as the *showcase website*, which is what it is good at. Do not make it the offline plan.

---

## 4. Exported PDFs in Files or Books

### Is exporting the WorkBook to PDF worth it?

**No, not as the offline strategy.** The objections are structural, not cosmetic:

- **One-way.** A PDF cannot become a commit. Every Build Log entry written trailside would have to be retyped.
- **Stale by construction.** Every edit to a Walkthrough means regenerating and re-copying the PDF. Nothing enforces that, so at some point you will be reading last month's torque figures in a gravel lot.
- **Links break.** Relative links between WorkBook files — a Walkthrough pointing at a Reference, a Case pointing at a Build Log entry — have no meaning once each file is a separate PDF. Images embed fine; the web of cross-references does not.
- **It is work you must remember to do,** which is exactly what "a boon, not vital" says you should not sign up for.

The narrow case where it *is* worth it: one Walkthrough you want to print for the paper notebook on Betty's dash, or hand to a friend who does not use git. Export that one, from the laptop, when you need it. Do not build a pipeline.

### The private manuals: Files or Books?

This is the second, unavoidable path, and it is where the answer is Files.

| | **Files + iCloud Drive** | **Books** |
|---|---|---|
| Guaranteed offline | Yes, explicitly: touch and hold the file, tap **Keep Downloaded** ([Files basics][apple-files-basics]) | Downloads are managed by the app; Apple documents iCloud sync of PDFs but not a per-file "keep" guarantee |
| Structure | Real folders, so the private library can mirror the WorkBook's `references/` shape | A flat library organised by Collections |
| One canonical copy shared with the Mac | Yes — the same iCloud Drive folder on both | Importing copies the PDF into Books, creating a second copy that diverges from the Mac folder |
| Annotating a scanned manual | Markup on PDFs: pen, marker, pencil, shapes, signature ([Write and draw in documents with Markup][apple-markup]) | Underline, highlight and notes tools ([Annotate books][apple-books-annotate]) |
| Annotations sync | Follows the file in iCloud Drive | "books, audiobooks, PDFs, collections, highlights, notes, and bookmarks appear automatically in Books" across devices ([Access books on other Apple devices][apple-books-icloud]) |
| Reading a long manual | Serviceable | Better: page position, collections, resumes where you left off |
| Eviction risk | Managed by you via **Keep Downloaded**; iOS will otherwise remove downloads to free space ([Files basics][apple-files-basics], [Manage storage on iPhone][apple-storage]) | Not documented per-file |

**Use Files + iCloud Drive with Keep Downloaded**, because the manuals should stay one canonical folder that the Mac and the phone share, sitting beside — never inside — the repo. Books is a fine *addition* if you want to read one manual cover-to-cover, but it should not be the system of record.

Two warnings. First, a scanned manual with no OCR text layer is not searchable in any of these apps; if the PDFs are scans, OCR them on the Mac once and the phone benefits forever. Second, **keep this folder private** — not a shared link, not a public album. These are copyrighted manuals kept as a personal copy, which is the entire reason they are git-ignored.

---

## 5. Runners-up

| Option | Verdict | Key facts |
|---|---|---|
| **GitSync** | Worth a look if Working Copy's price grates | Named in the obsidian-git README as the alternative it recommends over itself on mobile ([obsidian-git README][og-readme]). Its own README describes it as "a cross-platform git client for Android and **iOS**", supports Android 5+ and **iOS 13+**, HTTP/S, SSH and OAuth auth, cloning, background sync, and syncs from "an iOS shortcut or automation", with an App Store listing ([GitSync README][gitsync]). **I could not verify iOS feature parity or price from an Apple-owned page** — treat the README as the vendor's claim, not as tested fact |
| **iA Writer** | A nicer editor, not an offline strategy | Documents live in iCloud Drive or on the device, which is exactly the shape Working Copy's **Link external directory** feature is built for — iA Writer is named in that list ([External repositories][wc-external]). One-time purchase, not a subscription, on iPhone/iPad ([iA Writer pricing][ia-pricing]) — **I could not retrieve the current USD price from iA's own page; the App Store listing is the authority** ([iA Writer on the App Store][ia-appstore]) |
| **Textastic** | Same shape, more code-editor flavour | Has a built-in web preview covering HTML, CSS, JavaScript and **Markdown**, rendered with the MultiMarkdown library; local files are reachable from the Files app under On My iPhone → Textastic; and its own documentation points at **Working Copy as its git client** ([Textastic manual][textastic]). **Price not verified from the vendor's page** |
| **GitHub Mobile** | Not an offline tool | GitHub's documentation lists what Mobile does — triage notifications, read and review issues and pull requests, edit files in pull requests, search and browse repositories — and **says nothing about offline access or a local copy** ([GitHub Mobile][gh-mobile]). It is a client for github.com, so with no signal it has nothing to show. Useful for Plans (which are GitHub issues) when you *do* have bars |
| **Files + a plain iCloud folder for the WorkBook** | The right answer for the manuals, the wrong one for the WorkBook | It carries files offline once you tap **Keep Downloaded** ([Files basics][apple-files-basics]), but Files has no Markdown renderer, so a Walkthrough shows as raw text with broken-looking image syntax, and there is no path back into git |

---

## 6. Side by side

| | **Working Copy** | **Obsidian + iCloud** | **Obsidian + Working Copy link** | **Obsidian + Obsidian Git** | **GitHub Pages PWA** | **Exported PDFs (Files/Books)** |
|---|---|---|---|---|---|---|
| Money | Free to read; **$35.99 one-time** to push | Free app; iCloud tier you already pay for | **$35.99** (same unlock) + free app | Free | Free | Free |
| Setup effort (estimate, mine) | ~20 min | ~30 min | ~45 min, two apps to keep straight | ~30 min, then fighting it | Hours: hand-written service worker + manifest | Minutes per export, forever |
| Selection granularity | **Whole repo only** — no documented sparse checkout ([Cloning repositories][wc-clone]) | Whole vault | Whole linked folder | Whole vault | Whatever you precache | Per file, manually |
| …finer selection available? | No | Only with Sync: by file type **and** excluded folders ([Sync settings][obs-selective]) | No | No | Yes, but you write the code | Yes, by hand |
| Sync back from the gravel lot | **Yes** — commit offline, push later ([Committing or reverting][wc-commit]) | Only between your own devices; no git | Yes, via Working Copy | Yes in theory; "very unstable" ([README][og-readme]) | **No — read-only** | **No** |
| Markdown renders | Yes, Markdown is a preview format ([Text Editing][wc-edit]) | Yes | Yes | Yes | Yes (it is a website) | It is a picture of Markdown |
| Relative-linked images render | Documented for HTML preview: relative links resolve inside the repo, no internet needed ([Text Editing][wc-edit]); **Markdown not stated explicitly — test it** | Works in practice; **Obsidian documents `![[wikilink]]` embeds and external Markdown links, not relative-path Markdown embeds** ([Embed files][obs-embeds]) | Same as Obsidian | Same as Obsidian | Yes, if precached | Embedded in the PDF |
| Survives with no signal | **Yes — local clone, nothing to evict** | Yes if vault is On My iPhone; iCloud vaults need **Keep Downloaded** ([Sync your notes][obs-sync-notes]) | Yes | Yes | Exempt from ITP's 7-day cap as a Home Screen app, but **still evictable under quota or storage pressure** ([Updates to Storage Policy][wk-storage]) | Yes with **Keep Downloaded** |
| Carries the private manuals | **No** | No | No | No | **No, and never can** ([Pages is public][gh-pages-https]) | **Yes** |

---

## 7. Failure modes and gotchas

- **Nothing git-based carries the private manuals.** Say it twice, because every git option above looks like a complete answer and is not. Budget for the second path from the start.
- **Wikilinks will break the showcase.** Obsidian's default `![[image.jpg]]` syntax does not render on github.com. If Obsidian ever touches the WorkBook, disable **Use [[Wikilinks]]** first ([Internal links][obs-links]).
- **Two devices, one repo, one merge conflict.** Editing the same Walkthrough on the laptop and the phone without pushing in between produces a conflict that has to be resolved on a 6-inch screen. Habit to build: pull before you leave the driveway, push before you open the laptop. Obsidian's help gives the same warning in its own idiom — avoid syncing the same vault through two services at once, or you get "data conflicts or corruption" ([Sync your notes across devices][obs-sync-notes]).
- **Repo growth is the slow leak.** The WorkBook is markdown plus a few hundred MB of JPEGs and grows forever. Working Copy carries the full history; **estimate (mine): about 2× the working-tree size on the phone**. Check it once a year. If it ever becomes a problem, the fix is a cleanup of the repo, not a cleverer phone app. And GitHub's recommended repo limit is 1 GB, with the published Pages site capped at 1 GB ([GitHub Pages limits][gh-pages-limits]) — the same ceiling governs both the website and the phone.
- **Obsidian Sync Standard's 1 GB account-wide storage** counts attachments and version history ([Plans and storage limits][obs-plans]). A photo-heavy WorkBook will hit it, and the escalation is Sync Plus, not a workaround.
- **iCloud eviction is real but controllable.** iOS will remove downloads to reclaim space; **Keep Downloaded** is the documented control, and it must be applied deliberately ([Files basics][apple-files-basics]). Note that Apple documents it as a per-file action; **whether marking a folder covers files added later is not something I could confirm from Apple's documentation — check the manuals are still showing as downloaded before a trip.**
- **App offloading is not a threat.** Offloading "frees up storage used by the app, but keeps its documents and data" ([Manage storage on iPhone][apple-storage]), so a clone survives.
- **Obsidian Git will eat a photo-heavy repo.** Memory-limited, no SSH, and its author's own advice for large repos is to stage individual files ([obsidian-git README][og-readme]).
- **A PWA cache is not a filing cabinet.** Even with the Home Screen exemption from the 7-day cap, eviction is still on the table under quota or storage pressure ([Updates to Storage Policy][wk-storage]).
- **Licensing.** The manuals are copyrighted. They stay in a private iCloud Drive folder as a personal copy — not in the repo, not on Pages, not in a shared album, not in a link sent to the forum thread. Only notes and extracts get committed.

---

## Recommendation

**Working Copy, plus an iCloud Drive folder for the manuals.** Working Copy is the primary tool: it carries the entire WorkBook, renders it, and is the only option that gets a trailside Build Log entry back into git. The iCloud Drive folder is the second thing, and it is unavoidable because the private manuals can never enter a public repo — no git client can solve a licensing problem.

1. **Install Working Copy and clone the WorkBook.** Free. Use the GitHub repository picker rather than typing a URL ([Cloning repositories][wc-clone]). Take the 10-day Pro trial ([How to purchase][wc-purchase]).
2. **Within those 10 days, run the acid test.** Open a Walkthrough that contains `![Rear crossmember](media/2026-09-14-crossmember.jpg)`, turn on Airplane Mode, tap Preview. If the picture shows, the whole plan is confirmed. If it does not, say so on this ticket before spending money — the fallback is Obsidian pointed at an On-My-iPhone vault, with Working Copy linked to it.
3. **Buy the $35.99 Pro unlock** ([How to purchase][wc-purchase]). This is the one purchase in the whole plan. It buys pushing, which is the difference between a read-only copy and a working WorkBook.
4. **Set up the private manuals as an iCloud Drive folder** mirroring the WorkBook's References structure — outside the repo, named so it is obvious it is not part of it. Long-press each PDF in Files and tap **Keep Downloaded** ([Files basics][apple-files-basics]). OCR any scans on the Mac first so they are searchable. If one manual gets read cover-to-cover often, add a copy to Books for the reading experience ([Annotate books][apple-books-annotate]) — but the iCloud Drive folder stays the system of record.
5. **Adopt one habit:** pull before leaving, push when signal returns. That is the whole conflict-avoidance strategy for a one-person project.
6. **Do not build the GitHub Pages PWA for offline.** It cannot carry the manuals, it cannot write back, and it would mean hand-writing a service worker. Keep Pages for the public showcase, which is what the map already wants it for.
7. **Do not build a Markdown-to-PDF export pipeline.** Export a single Walkthrough by hand, from the laptop, on the rare occasion you want it on paper for the notebook on Betty's dash.
8. **Revisit only if one of two things happens:** the repo grows past roughly a gigabyte on the phone, or you find yourself wanting to *write* long entries on the phone rather than short ones — at which point Obsidian as an editor with Working Copy's **Link external directory** underneath ([External repositories][wc-external]) is the upgrade, and it costs no extra money.

[wc-purchase]: https://workingcopy.app/manual/purchase/
[wc-clone]: https://workingcopyapp.com/manual/cloning-repos/
[wc-commit]: https://workingcopyapp.com/manual/commit-revert/
[wc-edit]: https://workingcopyapp.com/manual/edit/
[wc-external]: https://workingcopy.app/manual/external-repos/
[wc-files]: https://workingcopyapp.com/manual/files-sync/
[obs-license]: https://obsidian.md/help/teams/license
[obs-pricing]: https://obsidian.md/pricing
[obs-plans]: https://obsidian.md/help/sync/plans
[obs-selective]: https://obsidian.md/help/sync/settings
[obs-sync-notes]: https://obsidian.md/help/sync-notes
[obs-switch]: https://obsidian.md/help/sync/switch
[obs-vaults]: https://obsidian.md/help/manage-vaults
[obs-links]: https://obsidian.md/help/links
[obs-embeds]: https://obsidian.md/help/embeds
[obs-formats]: https://obsidian.md/help/file-formats
[obs-settings]: https://obsidian.md/help/settings
[og-readme]: https://github.com/Vinzent03/obsidian-git
[isogit]: https://isomorphic-git.org/
[gitsync]: https://github.com/ViscousPot/GitSync
[gh-pages-https]: https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https
[gh-pages-limits]: https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits
[gh-mobile]: https://docs.github.com/en/get-started/using-github/github-mobile
[wk-itp]: https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/
[wk-storage]: https://webkit.org/blog/14403/updates-to-storage-policy/
[wk-workers]: https://webkit.org/blog/8090/workers-at-your-service/
[apple-webapps]: https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html
[apple-files-basics]: https://support.apple.com/guide/iphone/iphe9d46e90f/ios
[apple-storage]: https://support.apple.com/guide/iphone/manage-storage-on-iphone-iph47c931112/ios
[apple-icloud-storage]: https://support.apple.com/en-us/108922
[apple-markup]: https://support.apple.com/guide/iphone/write-and-draw-in-documents-iph893c6f8bf/ios
[apple-books-annotate]: https://support.apple.com/guide/iphone/annotate-books-iph17bf340c1/ios
[apple-books-icloud]: https://support.apple.com/guide/iphone/access-books-on-other-apple-devices-iphb886e1752/ios
[ia-pricing]: https://ia.net/writer/pricing
[ia-appstore]: https://apps.apple.com/us/app/ia-writer/id775737172
[textastic]: https://www.textasticapp.com/v10/manual/
