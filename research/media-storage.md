# How should photos and video be stored and linked?

Research for [#4](https://github.com/instant-coffee/Project-Betty/issues/4) (map: [#1](https://github.com/instant-coffee/Project-Betty/issues/1)). Researched 2026-09-16 against GitHub, Apple, Google, Flickr and Cloudinary documentation. Limits and prices change, so check the linked page before relying on a number.

## The question

The WorkBook is a public GitHub repo that will collect hundreds of iPhone photos and some video for Build Log entries and the showcase. Two options are on the table:

- **(c) External + linked:** media lives in Google Photos, iCloud Shared Albums, Google Drive, Flickr, Cloudinary or similar, and the Markdown links to it.
- **(d) Resized in git, originals external:** small web-sized copies are committed next to the entries, and full-resolution originals stay in a photo library outside the repo.

## Answer in brief

Go with **(d)** for photos. GitHub's limits are generous for resized JPEGs. Images in the repo render inline both on github.com and on a future GitHub Pages site, with no third-party link to rot. None of the consumer photo services (Google Photos, iCloud Shared Albums, Google Drive) documents a stable direct image URL that can be embedded. Flickr requires a link back and caps free accounts at 1,000 items. Cloudinary hotlinks cleanly, but it is another account on a credit budget. **Video** is the exception: keep it out of git, and commit only a still frame that links to the clip. The real risk in (d) is privacy: a committed photo with GPS data stays in git history for good, so strip metadata *before* the commit.

---

## 1. GitHub limits

| Limit | Value | Source |
|---|---|---|
| Per-file warning / hard block | 50 MiB warning, 100 MiB blocked | [About large files on GitHub][gh-large] |
| Browser upload (github.com "Add file > Upload files") | 25 MiB per file, up to 100 files at once | [About large files][gh-large], [Adding a file to a repository][gh-add-file] |
| Recommended repository size | "Ideally … less than 1 GB, and less than 5 GB is strongly recommended" | [About large files][gh-large] |
| GitHub Pages source repo | "recommended limit of 1 GB" | [GitHub Pages limits][gh-pages-limits] |
| GitHub Pages published site | "may be no larger than 1 GB" | [GitHub Pages limits][gh-pages-limits] |
| GitHub Pages bandwidth | soft limit of 100 GB per month | [GitHub Pages limits][gh-pages-limits] |
| Bandwidth in general | GitHub may throttle or suspend if use is "significantly excessive in relation to other users of similar features" | [Acceptable Use Policies][gh-aup] |

**Sizing estimate (mine, not from a source):** an iPhone photo resized to about 1600 px on the long edge and saved as JPEG at ordinary quality is usually 200–500 KB. At about 350 KB each, 1,000 photos come to about 350 MB. That fits under both the 1 GB repo recommendation and the 1 GB Pages site cap with room to spare. Full-resolution originals, often several MB each, would not fit once there are a few hundred.

### Git LFS

- Free and Pro personal accounts each get **10 GiB storage and 10 GiB bandwidth** per month ([Git LFS billing][gh-lfs-billing]). Overage runs about **$0.07/GiB storage and $0.0875/GiB transfer** ([GitHub pricing calculator][gh-pricing]).
- Bandwidth "always count[s] against the repository owner's account. Forking and pulling a repository counts against the parent repository's bandwidth usage" ([Git LFS billing][gh-lfs-billing]). On a public showcase repo, strangers' clones and forks spend the owner's quota.
- Without a payment method, going over the bandwidth quota means "Git LFS support is disabled on your account until the next month". Going over storage means clones "only retrieve the pointer files" and new pushes are blocked ([Git LFS billing][gh-lfs-billing]).
- **"Git LFS cannot be used with GitHub Pages sites."** ([About Git LFS][gh-lfs-about]). That alone rules out LFS here, because keeping the door open to a website is a standing preference.
- Conclusion: LFS isn't needed for resized JPEGs, which are far below 50 MiB, and it would break Pages. Don't use it.

### GitHub Releases (a possible home for video or original archives)

- "Each file included in a release must be under 2 GiB. There is no limit on the total size of a release, nor bandwidth usage." Up to 1,000 assets per release ([About releases][gh-releases]).
- Release assets are not part of git history, so they don't grow clones. They are download links, though, not inline media.

## 2. How images render in GitHub Markdown and GitHub Pages

- **Images in the repo:** GitHub recommends relative links: "When you want to display an image that is in your repository, use relative links instead of absolute links" ([Basic writing and formatting syntax][gh-md]). A relative link such as `![Rear crossmember](media/2026-09-14-crossmember.jpg)` renders on github.com. The same file also ships with a Jekyll/Pages build, because Pages publishes the repo's files, and LFS isn't involved.
- **Formats:** GitHub's file viewer displays "PNG, JPG, GIF, PSD, and SVG" ([Working with non-code files][gh-noncode]). Issue/comment attachments accept PNG, GIF, JPEG and SVG ([Attaching files][gh-attach]). **HEIC, the iPhone's default capture format, appears in neither list**, so photos must become JPEG before they go into the repo.
- **External images go through a proxy:** GitHub rewrites external image URLs through Camo, which requires an image MIME type and cannot fetch "images from private networks or requiring authentication" ([About anonymized URLs][gh-camo]). The open-source Camo whitelist includes `image/jpeg`, `image/png` and `image/webp` but not HEIC ([camo mime-types.json][camo-mime]). Its defaults are a 5 MB `Content-Length` cap and a 10 s fetch timeout ([atmos/camo README][camo]). GitHub doesn't publish its production values, so treat these as indicative. In practice an external photo renders inline on github.com only if the host serves a raw, public, reasonably small JPEG/PNG at a stable URL. A share-page URL (an album web page) won't render. It shows as a link at best.
- **Video:** GitHub documents video uploads (`.mp4`, `.mov`, `.webm`) only as attachments to issues, PRs and comments. The limit is **10 MB on a free plan** and 100 MB on a paid plan ([Attaching files][gh-attach]). The docs don't cover embedding those attachment URLs in repository Markdown files, so an agent-built WorkBook shouldn't depend on that. In public repos, "uploaded files can be accessed without authentication" ([Attaching files][gh-attach]).

## 3. External options: durability and hotlinkability

| Option | Direct, embeddable image URL? | Durability / limits | Source |
|---|---|---|---|
| **Google Photos** (shared album or link) | No documented one. Sharing makes "a link … anyone with the link can view", which is a web page. The API's media `baseUrl`s "remain active for 60 minutes". Since 31 Mar 2025 the Library API can only reach items the app created itself, and shared-album API calls return `403`. | Counts toward the 15 GB Google account quota. The link survives as long as the album and account do. | [Share photos & videos][gphotos-share], [Access media items][gphotos-api], [Library API updates][gphotos-updates], [Google storage][g-storage] |
| **iCloud Shared Albums** ("Public Website") | No. "Your photos publish to a website that anyone can see", and Apple documents no per-image URL. | Photos are reduced to **2,048 px on the long edge**, video to 15 min at 720p, with 5,000 items per album. Doesn't count toward iCloud storage. | [Shared Albums][icloud-shared], [Shared Album limits][icloud-limits] |
| **Google Drive** | No. Google deprecated Drive web hosting in 2015 and shut off `googledrive.com/host` on 31 Aug 2016. Direct-download URL tricks in circulation are undocumented. | Counts toward 15 GB. | [Deprecating web hosting in Drive][gdrive-hosting], [Google storage][g-storage] |
| **Flickr** | Possible, with conditions: the Community Guidelines require that you "link back to Flickr when you post your Flickr content elsewhere", pointing to the photo page, not the static image URL. | Free accounts can't upload more than 1,000 items. Content over the limit risks deletion unless you upgrade to Pro. | [Flickr Community Guidelines][flickr-guidelines], [Free account limits][flickr-limits] |
| **Cloudinary** | Yes. A CDN delivery URL with on-the-fly resizing. | Free plan: 25 credits/month, where 1 credit = 1 GB storage **or** 1 GB bandwidth **or** 1,000 transformations. No card required. Links die if the account lapses or goes over budget. | [Cloudinary pricing][cloudinary] |
| **GitHub issue/comment attachments** | Yes in practice. Documented only for issues, PRs and comments. | Images 10 MB, video 10 MB on the free plan. Hosted by GitHub, so they last as long as the repo does. | [Attaching files][gh-attach] |

**What this means for (c):** only Cloudinary, and Flickr within its rules, give embeddable images, and each is one more account whose lapse breaks every entry at once. The services the owner already uses from the iPhone (iCloud, Google Photos) give *album links*, not *inline images*. A Build Log built on them would read as text plus "see album" links, and a `git clone` or offline copy would carry no pictures at all.

## 4. From iPhone to entry: friction and privacy

- **Format:** iPhones capture HEIF/HEVC by default. Settings > Camera > Formats > **Most Compatible** switches new captures to JPEG/H.264. When sharing to something that doesn't handle HEIF, "the media might automatically be shared in a more compatible format, such as JPEG or H.264" ([Apple: HEIF/HEVC media][apple-heif]). Either way, the pipeline has to end with a JPEG.
- **Location:** Apple documents two controls: removing location from a photo (Photos > photo > More > Adjust Info > Adjust Location > **No Location**), and removing it at share time ("Tap the Share Sheet button, then tap **Options**. Turn off **Location**") ([Apple: Manage location metadata in Photos][apple-location]). Google Photos excludes location from new shared albums and links by default ([Google Photos location data][gphotos-location]). Apple documents these controls for *location*, not for all EXIF (such as device model and timestamps), so an automated strip step is still worth having.
- **Why privacy matters more in (d):** once a photo is pushed to a public repo, removing it requires rewriting history, and "You cannot remove sensitive data from other users' clones of your repository". Cached views and forks keep it too ([Removing sensitive data][gh-sensitive]). A gravel-lot or trailside photo carrying GPS data could reveal where Betty is parked. **Metadata must be stripped before the commit**, and the repo should verify that automatically, for example with a check that rejects JPEGs containing GPS tags.
- **Resizing on the phone:** the Shortcuts app has image *transform* actions (resize, convert format) that can build a one-tap share-sheet shortcut ([Shortcuts transform actions][apple-shortcuts]). Apple doesn't document whether the convert step keeps metadata, so test it before trusting it.
- **Resizing on the laptop:** macOS ships `sips`, which can resize (`-Z <max px>`) and convert HEIC to JPEG (checked locally with `sips --help`). A small script or an agent can resize, strip and name files in one pass. The exact tool is an implementation detail for the spec.
- **Friction compared:**
  - (c) Share to album (location off) → copy the link → paste into the entry. It feels low-friction, but the result is a link, not a picture, and costs one link per photo.
  - (d) Share via a Shortcut (resize + JPEG + location off) → upload to the repo, either on github.com (25 MiB/100 files per upload) or through the agent or laptop → a relative image link in the entry. That's one more step up front, but it can be scripted once and the picture renders forever.
  - Uploading full-size originals into the repo and resizing afterwards would put GPS data and multi-MB files into history. **Resize and strip before anything touches git.**

## 5. (c) vs (d) at a glance

| | (c) External + linked | (d) Resized in git, originals external |
|---|---|---|
| Renders inline on github.com | Only with a true image host (Cloudinary, Flickr within rules) | Yes (relative links) |
| Works on a future GitHub Pages site | Same as above | Yes. No LFS, well under the 1 GB cap |
| Link rot risk | High: every entry depends on a third-party account and URL scheme | None for display copies. Originals are a backup, not a dependency |
| Offline / clone has pictures | No | Yes |
| Repo size | Tiny | Hundreds of MB over years at web size. Fine |
| Privacy failure mode | Recoverable: delete or unshare the album | Permanent once pushed, so strip before commit |
| Video | Natural fit | Poor fit. Keep video external |
| Fits "repo is the single home" | Weakly | Yes |

## Recommendation

**Choose (d): resized copies committed to git, originals stored outside.**

1. **Display copies in git:** JPEG, about 1600 px on the long edge, all metadata (especially GPS) removed, stored next to the entries that use them and referenced with relative links. Enforce the metadata strip with an automated check so a mistake never reaches public history.
2. **Originals outside git:** keep full-resolution photos in the owner's existing iPhone library (iCloud Photos or Google Photos) as the archive. Entries don't depend on it. An album link can be added as an optional "full-resolution set" pointer.
3. **Video outside git:** commit a still frame (resized like any photo) that links to the clip wherever it lives: a shared album, a video host, or a GitHub Release asset for clips worth keeping alongside the repo (2 GiB per file, no total or bandwidth cap).
4. **No Git LFS** (it doesn't work with Pages, and forks spend the owner's bandwidth), and **no dependence on Google Photos, iCloud or Drive URLs for inline images** (none of them documents a stable direct image URL).
5. **Capture path:** an iOS Shortcut on the share sheet (resize, convert to JPEG, drop location) feeding either a github.com upload or the agent or laptop. The mobile-agent and templates tickets should settle the exact hand-off.

[gh-large]: https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-large-files-on-github
[gh-add-file]: https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository
[gh-pages-limits]: https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits
[gh-aup]: https://docs.github.com/en/site-policy/acceptable-use-policies/github-acceptable-use-policies
[gh-lfs-billing]: https://docs.github.com/en/billing/concepts/product-billing/git-lfs
[gh-lfs-about]: https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-git-large-file-storage
[gh-pricing]: https://github.com/pricing/calculator?feature=lfs
[gh-releases]: https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases
[gh-md]: https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
[gh-noncode]: https://docs.github.com/en/repositories/working-with-files/using-files/working-with-non-code-files
[gh-attach]: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/attaching-files
[gh-camo]: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-anonymized-urls
[gh-sensitive]: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository
[camo]: https://github.com/atmos/camo
[camo-mime]: https://github.com/atmos/camo/blob/master/mime-types.json
[gphotos-share]: https://support.google.com/photos/answer/6131416
[gphotos-api]: https://developers.google.com/photos/library/guides/access-media-items
[gphotos-updates]: https://developers.google.com/photos/support/updates
[gphotos-location]: https://support.google.com/photos/answer/11190100
[g-storage]: https://support.google.com/googleone/answer/9312312
[gdrive-hosting]: https://workspaceupdates.googleblog.com/2015/08/deprecating-web-hosting-support-in.html
[icloud-shared]: https://support.apple.com/en-us/108314
[icloud-limits]: https://support.apple.com/en-gb/108916
[flickr-guidelines]: https://www.flickr.com/help/guidelines
[flickr-limits]: https://www.flickrhelp.com/hc/en-us/articles/13690320471060-Free-account-limits-and-enforcement
[cloudinary]: https://cloudinary.com/pricing
[apple-heif]: https://support.apple.com/en-us/116944
[apple-location]: https://support.apple.com/guide/personal-safety/manage-location-metadata-in-photos-ips0d7a5df82/web
[apple-shortcuts]: https://support.apple.com/guide/shortcuts/transform-actions-apd1d9413cfb/ios
