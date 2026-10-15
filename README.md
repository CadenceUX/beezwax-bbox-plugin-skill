# beezwax-bbox-plugin-skill

A [Claude skill](https://docs.claude.ai/skills) for the **bBox FileMaker Plug-in** by Beezwax Datatools, Inc. — a free, closed-source FileMaker plug-in that lets solutions shell out to AppleScript, Bash/Zsh/shell, cURL, DuckDB/SQLite, JavaScript (JavaScriptCore and Node), Python, Ruby, jq, GraphicsMagick/sips, and OS-level pasteboard/file/WebViewer features. Gives Claude accurate, cross-checked knowledge of every `bBox_` function and `bBox ...` script step, without relying on potentially stale training data.

Built and maintained by [Darrin Southern](https://www.linkedin.com/in/darrin-southern/) from [CadenceUX](https://cadenceux.com.au).

---

## What it does

When this skill is active, Claude will:

- Look up any of the **87 cataloged bBox functions and script steps** by name or category — signature, description, parameters, result, the correct `bBox_LastError` reference parameter, platform compatibility, and example code where available
- Distinguish three confidence tiers: **39 entries with a dedicated wiki page** (richest detail), **48 entries confirmed via the vendor's own bBox Plug-in Demo file and/or the compiled plugin binary** (no wiki page, but solid primary-source evidence), and any future cross-reference-only entries — so guidance on the less-documented parts of the plugin's surface is appropriately hedged rather than overconfident
- Apply the shared **mode-flag bit table** (`+1`/`+2`/`+4`/`+8`/`+16`/`+32`/`+64`) that most functions use for line-ending, async, and stdout/stderr behaviour
- Resolve the **`bBox_LastError` reference parameter** for any function from a single lookup table, rather than guessing at `"posix"` vs `"js"` vs a session reference number
- Flag known **upstream wiki documentation bugs** — a copy/paste artifact in `bBox_SplitText`'s parameter description and a typo in `bBox_GetCharacterStyle`'s — rather than silently trusting them
- Apply critical usage patterns: container-field-reference shortcuts, the POSIX-path-only rule, the `fm` bridge for JavaScript/Python calling back into FileMaker, thread/session-safety exceptions, and PSoS/server-side script limitations

---

## Coverage

| Reference file | Contents |
|---|---|
| `function-catalog.json` | 87 functions/script steps across 15 categories, each tagged `doc_status: "full"` (39, dedicated wiki page), `"full_demo_confirmed"` (48, confirmed via the demo file/plugin binary, no wiki page), or `"stub_no_wiki_page"` (0 — retired once a stronger source was cross-checked). Full and full_demo_confirmed entries carry `signature`, `description`, structured `parameters`, `result`, `error_reference`, `since`/`compatibility` where stated, `script_step_equivalent`, `notes`, and `example_code`. A `shared_reference` block holds the cross-cutting tables: mode flags, the full error-reference table, container-field-reference rules, file-path/type-conversion notes, the native `fm` bridge, thread/session safety, and PSoS/calculation-field limits. |

**Categories:** Shell/Bash/Zsh, AppleScript & macOS Automation, Networking, Databases (DuckDB & SQLite), Text & Parsing, Files & Filesystem, Scripting Languages (JavaScript/Node/Python/Ruby/jq), Images, Clipboard, WebViewer Control, XPath & XML, System & Environment, Diagnostics & Session Info, Scripting Helpers, Logging & Schema Notifications.

---

## Install

**Claude desktop app (macOS):** download [`beezwax-bbox-plugin.skill`](https://github.com/CadenceUX/beezwax-bbox-plugin-skill/releases/latest/download/beezwax-bbox-plugin.skill) and
double-click it.

**Claude.ai in a browser, or Claude Desktop → Customize → Skills → upload:** download
[`beezwax-bbox-plugin.zip`](https://github.com/CadenceUX/beezwax-bbox-plugin-skill/releases/latest/download/beezwax-bbox-plugin.zip) and upload it.

> Use the files above, which are attached to the latest
> [release](https://github.com/CadenceUX/beezwax-bbox-plugin-skill/releases). Don't use GitHub's green
> **Code → Download ZIP** button or a release's **Source code (zip)**: those are snapshots of
> the repository, not a packaged skill, and Claude won't install them as one.

**Claude Code** (from the `cadenceux` plugin marketplace):

```bash
claude plugin marketplace add https://github.com/CadenceUX/cadenceux-skills.git
claude plugin install beezwax-bbox-plugin@cadenceux
```

**Cowork, or claude.ai as a plugin:** Customize → Plugins → Add marketplace from GitHub →
`CadenceUX/cadenceux-skills`, then install **beezwax-bbox-plugin**.

## How it works

Unlike `goya-be-plugin` or `monkeybread-mbs-plugin`, bBox's documentation doesn't live in its
GitHub repo — `beezwax/bbox-documentation` contains only a README pointing at the repo's
**GitHub wiki**, which holds most per-function pages (fetched here via
`bbox-documentation.wiki.git` / the `raw.githubusercontent.com/wiki/...` URL pattern). Only 39 of
the 87 catalogued functions and script steps have a dedicated wiki page. A same-day review
(2026-07-07, after Developer provided an actual plugin download) added a second primary source:
the **bBox Plug-in Demo.fmp12 file's own embedded function-reference text** (extracted via
`strings`) and the **compiled plugin binary's internal `Do_*` handler table**. Together these
confirmed 48 more functions with real signatures and descriptions — including 15 that weren't in
the wiki, the third-party draft, or the demo's own reference index at all (the `bBox_HTTP_*`
family, `bBox_FM_Admin_Login`, `bBox_Sort`, and others) — leaving zero entries in the original
cross-reference-only `stub_no_wiki_page` tier.

A third source was found and fully swept the same day: **https://www.beezwax.net/bbox-functions**,
an older ("0.97+"-era, pre-1.06) function index whose ~60 pages mirror the wiki's per-function
format (Parameters/Result/Error/Script Step block, since/compatibility, prose). All were fetched
and diffed against the catalog; about 20 `full_demo_confirmed` entries gained since/compatibility
fields and richer behavioural detail as a result — see CHANGELOG for the full list, and
`bBox_PythonEval`'s entry for one genuine, unresolved conflict this sweep surfaced between the
demo file and this older source.

---

## Known upstream issues

- **`bBox_SplitText`'s parameter description has a copy/paste artifact** — the live wiki page's
  `text` parameter reads "source texttext: sent to NodeJS' stdin", where the second half is
  clearly pasted from `bBox_JavaScriptNode`'s `script` parameter. Corrected in the catalog.
- **`bBox_GetCharacterStyle`'s first parameter is labelled `test:` instead of `text:`** in the
  wiki's Parameters block. Corrected in the catalog.
- **`Errors-&-Troubleshooting.md`'s reference table has two function-name typos** —
  `bBob_Javascript` and `bBob_JavascriptNode` should read `bBox_JavaScript` and
  `bBox_JavaScriptNode` — plus one in the worked example, `bBbox_LastError`. Corrected in
  `shared_reference.error_reference`.
- **`bBox_WebViewHistory`'s demo-file description is almost certainly a copy/paste of
  `bBox_WebViewLoadFile`'s** ("Loads the file into Web Viewer...") — a function named "History"
  should plausibly do something with browsing history instead. Flagged via `doc_bug_note` rather
  than corrected, since the actual behaviour isn't independently known.
- **A third-party draft catalog (`bbox-plugin-skill.json`) sits in the repo root**, apparently from
  a prior attempt at this exact task (dated 2026-07-06). It has a JSON syntax error (trailing comma).
  Two script steps it named ("bBox Text Window Action", "bBox Text Window Open") were originally
  excluded from this catalog as unverified — that was corrected in the same-day review once the
  demo file and plugin binary independently confirmed both are real.
- **No GitHub releases feed exists for the plugin itself** — version drift has to be checked
  against the wiki Home page's "Current Version" line and its blog-post list, or (more precisely)
  a fresh download's `Info.plist` `CFBundleVersion` if Developer can supply one.
- **A handful of internal plugin functions have no confirmed public name** — the compiled binary
  contains `Do_ListFields`, `Do_ListTables`, `Do_GitOpen`, `Do_CutTextCharacters`,
  `Do_CutTextFields`, `Do_SQLiteExecMany`, `Do_DuckDBOptions`, and `Do_AppleMapsWebSnapshot`
  handlers with no matching `bBox_` name found in the wiki, third-party draft, or demo file text.
  Real, but deliberately not catalogued with a guessed signature — see `meta.known_gaps`.

---

## Plugin version tracking

`function-catalog.json`'s `meta.plugin_version_at_build` records the bBox version documented at
build time (`1.06`, as of 2025-07-19 per the wiki); `meta.plugin_build_at_build` has the precise
build (`1.06.7`), read from a Developer-provided plugin download's `Info.plist` `CFBundleVersion`.
See the "Plugin version & staleness check" section in `SKILL.md` for the exact drift-check
procedure.

---

## Keeping it current

Re-run the fetch-and-parse pass periodically (see `function-catalog.json`'s `meta.update_instructions`
for the exact procedure):

1. Clone the wiki repo fresh (`git clone https://github.com/beezwax/bbox-documentation.wiki.git`)
   rather than scraping rendered HTML.
2. Diff `git ls-tree -r HEAD --name-only` (not a plain `ls` — see the case-collision note below)
   against this catalog's function names to find additions.
3. For new or changed pages, parse the **`` `function( params)` ``** heading, the Parameters/Result/
   Error/Script Step code block, and the prose below the second `***` divider.
4. If a fresh plugin download is available, `strings -n 6` the bundled demo `.fmp12` file and grep
   for `bBox_` to catch anything the wiki misses (this is how `bBox_Sort` and 14 other functions
   were found in the same-day review that produced this section) — see `meta.update_instructions`
   for the exact binary/demo-file extraction procedure, including the Itanium-mangled-symbol
   extraction for the compiled plugin binary.
5. Re-check the wiki Home page's "Current Version" line for a plugin version bump.

**Case-collision gotcha:** the wiki repo's filenames mix case inconsistently (`bBox_DuckDBOpen.md`
style vs. lowercase link slugs in the Function List page). On a case-insensitive macOS volume, a
plain `ls` after cloning can silently under-report files that collide by case — always verify
via `git ls-tree -r HEAD --name-only`, which reads git's case-sensitive object database directly.

---

## Contributing

Issues and PRs welcome — particularly:
- New functions added to the bBox plug-in, or confirmation of the internal-only functions listed
  in `meta.known_gaps`
- Corrections to `full_demo_confirmed` signatures, confirmed against beezwax.net or a newer demo file
- Further doc-page discrepancies spotted upstream

---

## Licence

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — free to use, adapt, and redistribute with attribution.

Built and maintained by [Darrin Southern](https://www.linkedin.com/in/darrin-southern/) from [CadenceUX](https://cadenceux.com.au).
