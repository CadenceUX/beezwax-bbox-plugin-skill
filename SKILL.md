---
compatibility: Claude.ai, Claude Chat, Claude Code
metadata:
  "Built and maintained": "Darrin Southern from CadenceUX"
  version: "1.0"
name: beezwax-bbox-plugin
description: |
  Reference skill for the bBox FileMaker Plug-in by Beezwax Datatools, Inc. — a free,
  closed-source plug-in with 87 cataloged functions/script steps covering shell/Bash/Zsh,
  AppleScript, cURL and HTTP verbs, FileMaker Server API login, DuckDB/SQLite, JavaScript
  (JavaScriptCore and Node), Python, Ruby, jq, GraphicsMagick/sips images, clipboard,
  WebViewer control, XPath, macOS text windows, and macOS/Ubuntu system utilities. Use
  whenever any bBox_ function or "bBox ..." script step appears in a calculation or script,
  or when asked about shelling out, running AppleScript/Python/Ruby/Node/JavaScript, cURL/
  HTTP/SOAP/REST calls, DuckDB/SQLite queries, image conversion, or pasteboard/WebViewer/
  filesystem access from FileMaker. Covers shared mode-flag bit values, the bBox_LastError
  reference parameter per function, container-field-reference shortcuts, PSoS/server-side
  limitations, and thread/session-safety rules. Do not use for other FileMaker plugins — see
  goya-be-plugin or monkeybread-mbs-plugin.
---

# bBox FileMaker Plug-in — Reference Skill

**Vendor:** Beezwax Datatools, Inc.
**Product page:** https://www.beezwax.net/products/bbox
**Download (free, registration optional):** https://beezwax.net/forms/download/bbox
**Docs repo:** https://github.com/beezwax/bbox-documentation
**Docs wiki (actual source of the documentation):** https://github.com/beezwax/bbox-documentation/wiki
**Wiki raw URL pattern:** `https://raw.githubusercontent.com/wiki/beezwax/bbox-documentation/{PageName}.md`

Unlike Goya's BaseElements Plugin or the MBS Plugin, bBox's documentation repo itself
(`beezwax/bbox-documentation`) contains almost nothing — just a README pointing at the
**GitHub wiki**, which is where every function page actually lives. Fetch wiki pages via
the raw URL pattern above (or `git clone https://github.com/beezwax/bbox-documentation.wiki.git`),
not the rendered `github.com/.../wiki/...` HTML page.

The function/script-step reference is in `references/function-catalog.json`. It has three
top-level keys: `meta` (build provenance, counts, update instructions), `categories` (15
groups, each with a `notes` string and a `functions` object keyed by name), and
`shared_reference` (cross-cutting tables — see below).

## `doc_status`: three confidence tiers

Every catalog entry carries a `doc_status` field. There are three tiers, not two — a second
primary source (the vendor's own bBox Plug-in Demo.fmp12 file, plus the compiled plugin
binary) was cross-checked against this catalog after the initial build, and both proved more
authoritative than the indirect cross-reference reconstructions the first tier ("stub") relied
on. That original tier has been retired in favour of confirming against this stronger source.

- **`"full"`** (39 entries) — has a dedicated wiki page. Carries `signature`, `description`,
  structured `parameters`, `result`, `error_reference`, `since`/`compatibility` where the
  page states them, `script_step_equivalent`, `notes`, and `example_code` (verbatim, `null`
  if the page has none). Highest confidence.
- **`"full_demo_confirmed"`** (48 entries) — no dedicated wiki page, but the signature and
  description are confirmed via the bundled **bBox Plug-in Demo.fmp12 file's own embedded
  function-reference text** (extracted with `strings` — its layouts embed a full name/
  signature/description index for most of the plugin's surface) and/or the **compiled plugin
  binary's internal `Do_*` handler table**. Materially more reliable than a cross-reference
  reconstruction, since it comes from the vendor's own primary artifact rather than being
  inferred indirectly — but still thinner than a wiki page (no worked examples, no version
  history). This is now the larger of the two non-wiki-page tiers; it includes the entire
  legacy SQLite family, the WebViewer family, the XPath lifecycle, several diagnostics
  functions, the HTTP-verb family, and more.
- **`"stub_no_wiki_page"`** (0 entries as of this build) — reserved for a function confirmed
  real only via indirect cross-reference (another page's mention, an error-table entry), with
  no primary-source signature available at all. Every entry that started in this tier during
  the initial build has since been either promoted to `full_demo_confirmed` (the demo file
  had a description of its own) or, if genuinely unconfirmable, left uncatalogued in
  `meta.known_gaps` instead of guessed at.

Two script steps ("bBox Text Window Action", "bBox Text Window Open") and one function
("bBox_SelectMenuItem") were excluded from the original v1.0 build as unverified — they
appeared only in a third-party draft file, not the wiki. That exclusion was wrong: all three
are confirmed real via the demo file and/or the compiled binary, and are now catalogued
under `full_demo_confirmed`. See `meta.known_gaps` for what's still genuinely unconfirmed —
a handful of internal `Do_*` handlers (e.g. `Do_ListFields`, `Do_ListTables`, `Do_GitOpen`,
`Do_CutTextCharacters`) with no public bBox_-prefixed name found in any available source.
Don't invent a signature for these; test directly in FileMaker or check beezwax.net first.

**A third primary source was fully swept**: https://www.beezwax.net/bbox-functions, an older
("0.97+"-era, pre-1.06) function index whose ~60 pages mirror the wiki's per-function page
format. All were fetched and diffed against this catalog — about 20 `full_demo_confirmed`
entries gained since/compatibility fields and richer descriptions as a result (see CHANGELOG
for the full list). One genuine, **unresolved** conflict surfaced: `bBox_PythonEval`'s
demo-file signature (`reference; expression`, session-based) disagrees with this older page's
(`expression` only, session-less, described as a one-shot alternative to the Compile/Execute/
Finalize family) — see that entry's `doc_bug_note` before trusting either without testing.

---

## Plugin version & staleness check

`meta.plugin_version_at_build` (currently `1.06`, documented as current as of 2025-07-19;
`meta.plugin_build_at_build` has the precise build, `1.06.7`, read from a Developer-provided
plugin download's `Info.plist` `CFBundleVersion` — more specific than the wiki's bare "1.06")
records the bBox version this catalog was built against. Unlike Goya's or MBS's plugins,
**bBox has no GitHub releases feed** to query for drift (`beezwax/bbox-documentation` is a
docs-only repo; the plugin binary itself isn't distributed via GitHub). The only available
signals:

1. Re-fetch `https://github.com/beezwax/bbox-documentation/wiki/Home` and check the
   "### Current Version" line near the bottom — compare against `meta.plugin_version_at_build`.
2. Check the "Related Blog Posts" list on that same page for a numbered post newer than
   "bBox 1.06 for FileMaker Now Available" — Beezwax announces each release there.
3. If Developer has (or can get) a fresh plugin download, `Info.plist`'s `CFBundleVersion`
   inside `{platform}/bBox.fmplugin/Contents/` gives the exact build number — compare against
   `meta.plugin_build_at_build`. This is more precise than either wiki signal above, but
   depends on Developer supplying a download rather than being fetchable independently.
4. If any signal shows a newer version, flag it to the user:

   > ⚠️ **Plugin version drift detected** — the bBox wiki now documents version [X], but this
   > skill's catalog was built against 1.06. New or changed functions since then won't be
   > reflected here. Consider running a catalog rebuild (`meta.update_instructions` in
   > `function-catalog.json`) before relying on anything not already in this catalog.

5. Still answer using the local catalog — the flag is advisory, not a blocker.
6. Only run this check when asked, or when something seems to disagree with the catalog —
   not on every routine lookup.

**Enumeration drift** (new function pages added to the wiki without a version bump): clone
the wiki repo and diff `git ls-tree -r HEAD --name-only` against this catalog's function
names. Use `git ls-tree`, not a plain `ls` — the wiki's mixed-case filenames (e.g.
`bBox_DuckDBOpen.md` vs a differently-cased path) can collide on a case-insensitive macOS
volume and silently vanish from a directory listing while still present in git history.

---

## Skill update self-check

At the start of each session, on the first bBox question, run once:

1. Fetch `https://github.com/CadenceUX/beezwax-bbox-plugin-skill/raw/main/VERSION`
2. Parse the returned string as the latest available version
3. Compare with this skill's installed version (currently `"1.0"`)
4. If latest > installed, prepend this notice to your first response:

   > ⚠️ **Skill update available**
   > This skill is v[installed]. v[latest] is available at
   > https://github.com/CadenceUX/beezwax-bbox-plugin-skill/releases
   > Update your local skill files to get the latest bBox coverage.

5. Do not repeat the notice again in the same session.
6. If the fetch fails or returns an unexpected value, skip silently — do not surface the
   error (this skill may not be published to that repo yet).

---

## Critical patterns

### Mode flags (bit flags, combinable by addition)

See `shared_reference.mode_flags` for the full table. Support varies per function — check
the specific function's own mode description before assuming a flag applies.

| Flag | Effect |
|---|---|
| +1 | Skip CR→LF conversion on input |
| +2 | Skip LF→CR conversion on output |
| +4 | Run asynchronously (don't wait for output) |
| +8 | Skip finalization (keep context) |
| +16 | Use alternate path for command |
| +32 | Combine stdout & stderr |
| +64 | Unzip container references (0.99.7+) |

Some function pages describe pre-combined values (0, 1, 2, 3...) as a convenience enum
rather than spelling out each bit — e.g. `bBox_Shell`'s mode `3` is just `+1` and `+2` added
together.

### Error checking — always via `bBox_LastError`

Every bBox function reports errors through `bBox_LastError(reference)`, where `reference`
is either a fixed family name (`"posix"`, `"curl"`, `"js"`, `"xpath"`, `"util"`...) or a
session reference number returned by an open/compile call (`bBox_DuckDBOpen`,
`bBox_PythonCompile`). **The right reference value differs per function** — see
`shared_reference.error_reference` for the full table (built from
`Errors-&-Troubleshooting.md`, with two upstream name typos corrected:
`bBob_Javascript`/`bBob_JavascriptNode` → `bBox_JavaScript`/`bBox_JavaScriptNode`). A result
of `"0"` means no error; error codes are not cleared by reading them, so `bBox_LastError` can
be called more than once. For shell-family functions, add mode `+32` to surface stderr
output that's otherwise silently discarded — essential when debugging a Python traceback via
`bBox_Python3Run`.

### Container field references

Since 0.97, several functions (`bBox_Bash`, `bBox_Curl`, `bBox_GraphicsMagick`, `bBox_Grep`,
`bBox_JavaScriptNode`, `bBox_JQ`, `bBox Python3 Run` script step, `bBox_Ruby`, `bBox_Sips`,
`bBox_Zsh`) accept a container field reference directly in place of a file path — bBox
writes it to a temp file (`bBox_file_XXXXXXXXX.bin`, always `.bin` regardless of real type)
and substitutes the path in. These temp files are purged after 3 days or a reboot; clean up
sooner for large files or high-volume server use (see `shared_reference.container_field_references`
for the exact `find`-based cleanup commands).

### File paths are always POSIX

Convert a FileMaker path (`Get(DocumentsFolder)` etc.) with FileMaker's native
`ConvertFromFileMakerPath` before passing it to any bBox function — bBox never accepts
FileMaker-style paths directly.

### The `fm` bridge (JavaScript/Python calling back into FileMaker)

`bBox_JavaScript`, `bBox_JavaScriptNode`, and the Python session functions
(`bBox_PythonExecute`/`bBox_PythonEval`) can expose an `fm` class/object for calling back
into FileMaker: `fm.evaluate(expression)`, `fm.executesql(expression{, values})`,
`fm.run(filename, scriptname, {parameter})`. Python requires `import fm` first. DuckDB has
an analogous `fmevaluate` SQL function. See `shared_reference.native_fm_bridge` for full
per-method detail, including one known bug: `fm.evaluate` used to *set* a FileMaker global
from Python does not work correctly in bBox 1.06.

### Thread and session safety

Don't assume a stateful function (Python session calls, DuckDB, XPath) is safe to call
concurrently. `bBox_PythonExecute`/`bBox_PythonEval` share **one process-wide Python VM** and
are **not** thread or session safe — implement a mutex if PSoS/schedules might overlap.
`bBox_Python3Run` is thread safe instead (spawns a separate interpreter per call) but can't
use the `fm` bridge. DuckDB functions are both thread and session safe. See
`shared_reference.thread_and_session_safety` for the full per-function table.

### PSoS / server-side limitations

Functions needing a logged-in GUI session (most AppleScript automation) don't work under
PSoS or script schedules — there's no accessible window manager server-side even if a user
is nominally logged in. The FMS script engine is also more resource-constrained (large
native-code loops can exceed stack limits and crash it), and PSoS results are capped at
1,000,000 characters. Shell/Bash/Python/Ruby-family functions do not behave reliably inside
a **stored** calculation field — use unstored calculations instead. See
`shared_reference.psos_and_calculation_field_limits`.

---

## Category quick index

| Category | Key functions |
|---|---|
| Shell, Bash, and Zsh | bBox_Shell, bBox_Bash, bBox_Zsh, bBox_ShellExitStatus |
| AppleScript and macOS Automation | bBox_Applescript, bBox_ApplescriptLastError, bBox_PerformKeystrokes, bBox_MacCalendar, bBox_MacNotificationsGetSettings, bBox_MacNotification, bBox_SelectMenuItem, bBox Text Window Open/Action |
| Networking | bBox_Curl, bBox_Scp, bBox_FM_API_URL, bBox_FM_API_Login, bBox_FM_Admin_Login, bBox_HTTP_GET/DELETE/PATCH/POST/Option |
| Databases (DuckDB and SQLite) | bBox_DuckDB* (Open/Exec/Close), bBox_SQLite* (legacy), bBox_ExecuteSQL, bBox_SQLiteImport and bBox_DuckDBRun (both "Not Implemented") |
| Text and Parsing | bBox_Grep, bBox_EvaluateList, bBox_SplitText, bBox_ParsePositionList, bBox_GetCharacterStyle, bBox_Sort |
| Files and Filesystem | bBox_FileRead, bBox_FileWrite, bBox_FileStatus, bBox_FileSymLink, bBox_MimeTypeForPath |
| Scripting Languages | bBox_JavaScript, bBox_JavaScriptNode, bBox_JQ, bBox_Ruby, bBox_Python3Run, bBox_Python(Compile/Execute/Eval/GetVar/SetVar/Finalize) |
| Images | bBox_GraphicsMagick, bBox_Sips |
| Clipboard (Pasteboard) | bBox_PasteboardFlavors, bBox_PasteboardGet, bBox_PasteboardSet |
| WebViewer Control | bBox_WebView* (8 functions, incl. Print and History) |
| XPath and XML | bBox_XPathInitializeFromText, bBox_XPathEvaluate, bBox_XPathFinalize |
| System and Environment | bBox_SetEnvironmentVariable, bBox_Sysctl, bBox_URLHandlerInfo |
| Diagnostics and Session Info | bBox_Version, bBox_LastError, bBox_ReferenceInfo, bBox_SessionInfo, bBox_ThreadInfo, bBox_StackBacktrace |
| Scripting Helpers | bBox_CurrentTime, bBox_DoScript |
| Logging and Schema Notifications | bBox Syslog (+ Open/Close), bBox Schema Notifications, bBox_SchemaMessageHandler |

---

## Licence

This skill is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

Built and maintained by [Darrin Southern](https://www.linkedin.com/in/darrin-southern/) from [CadenceUX](https://cadenceux.com.au).
