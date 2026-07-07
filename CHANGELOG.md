# CHANGELOG — beezwax-bbox-plugin

## v1.0 — 2026-07-07 (updated 2026-07-07, pre-release)

Initial release. **Not yet shipped anywhere** (no GitHub repo exists for this skill — confirmed
404 on repo/raw-file/releases fetches — and it has not been uploaded to Claude.ai), so a
same-day accuracy review's fix landed in place per the amend-in-place convention rather than
as a v1.1 bump — see the review note at the end of this entry.

- **Full catalog built from the actual documentation source** — the `beezwax/bbox-documentation`
  GitHub repo itself contains only a README pointing at the GitHub **wiki**; the wiki (cloned via
  `bbox-documentation.wiki.git`, 50 pages) is where every function page actually lives. Fetched and
  parsed all 37 function/script-step pages that exist on the wiki, plus 13 concept pages (Errors &
  Troubleshooting, Installation, Mode Parameters, Parameters, Sessions & Thread Safety, the two
  native-bridge pages, Home, and the Functions & Script Steps index).
- **66 cataloged entries across 15 categories**: 39 with `doc_status: "full"` (dedicated wiki page —
  structured parameters, result, error reference, notes, example code where available) and 27 with
  `doc_status: "stub_no_wiki_page"` (real, cross-referenced functions with no dedicated page — the
  legacy SQLite family, the WebViewer family, the XPath lifecycle, and several diagnostics/system
  functions). Stub signatures were reconstructed from the wiki's Function List page and
  cross-references on other pages (the `bBox_LastError` reference table, `Sessions-&-Thread-Safety.md`),
  not from a primary source — flagged accordingly in each stub entry and in `SKILL.md`.
- **Third-party draft catalog treated as a lead-finder only, not a source.** A file named
  `bbox-plugin-skill.json` was found sitting in the `bbox-documentation` repo root (dated
  2026-07-06, apparently from a prior session) — it has a JSON syntax error (trailing comma before
  the final closing brace, at `reference_pages.older_versions`) and materially thinner per-function
  detail than the actual wiki pages provide. Two script steps it lists — "bBox Text Window Action"
  and "bBox Text Window Open" — could not be confirmed anywhere in the wiki, function pages, or the
  Functions & Script Steps index, and are **excluded** from this catalog as unverified (see
  `meta.known_gaps`).
- **New `shared_reference` block** (this skill's analogue to `goya-be-plugin`'s `constants` block):
  `mode_flags` (the shared bit-flag table — +1/+2/+4/+8/+16/+32/+64), `error_reference` (the full
  `bBox_LastError` reference-parameter table per function, with two upstream wiki typos corrected —
  `bBob_Javascript`/`bBob_JavascriptNode` → `bBox_JavaScript`/`bBox_JavaScriptNode`),
  `container_field_references`, `file_paths`, `type_conversion`, `line_endings_and_encoding`,
  `native_fm_bridge` (the `fm.evaluate`/`fm.executesql`/`fm.run` bridge shared by JavaScript and
  Python), `thread_and_session_safety`, and `psos_and_calculation_field_limits`.
- **Two upstream documentation bugs flagged via `doc_bug_note`**: `bBox_SplitText`'s `text` parameter
  description on the live wiki page reads "source texttext: sent to NodeJS' stdin" — the second
  clause is a copy/paste fragment of `bBox_JavaScriptNode`'s `script` parameter description, not
  part of `bBox_SplitText`'s own docs. `bBox_GetCharacterStyle`'s first parameter is labelled `test:`
  instead of `text:` in the Parameters block. Both corrected in the catalog with the original noted.
- **No releases-API staleness check is possible for this plugin** (unlike `goya-be-plugin` or
  `claris-filemaker-pro`) — `beezwax/bbox-documentation` is a docs-only repo with no tagged plugin
  releases. `SKILL.md`'s staleness-check section instead points at the wiki Home page's "Current
  Version" line and its Related Blog Posts list (Beezwax announces each release there) as the only
  available drift signals.
- Standard CadenceUX skill scaffolding: `SKILL.md` frontmatter (compatibility, metadata, versioned),
  skill update self-check pointing at `CadenceUX/beezwax-bbox-plugin-skill`, `README.md`,
  `evals/evals.json` (3 test prompts), and the standard Licence attribution line.

**Same-day accuracy review (2026-07-07):** re-cloned the wiki and programmatically diffed its
actual `bBox_*` page list (`git ls-tree`, not `ls` — see the case-collision note in
`meta.update_instructions`) against every catalog entry's `doc_status`, rather than trusting the
original build's counts. Found and fixed one real miscataloging: **`bBox_SetEnvironmentVariable`
has a dedicated wiki page** (its content was fetched and read during the original build) but had
been incorrectly filed as `stub_no_wiki_page` — the fetched detail was discarded rather than used.
Promoted to a full entry with its actual `parameters`/`result`/`error`/`script step` content from
the page. This also corrected an off-by-one in the page count the original build recorded
(50 wiki pages, not 49 — 37 function pages, not 36) and shifted the full/stub split from 38/28 to
39/27; `function-catalog.json`'s `meta`, `SKILL.md`, and `README.md` all updated to match. No other
discrepancies found: every other `full` entry's wiki page was confirmed to exist, and every other
`stub_no_wiki_page` entry was confirmed absent from the wiki's actual file listing — a clean
reconciliation both directions. Wiki content itself is unchanged since the original build (Home.md
still documents plugin version 1.06 as of 2025-07-19). Version kept at v1.0 per the amend-in-place
convention — this catalog has not shipped anywhere yet.

**Second same-day review (2026-07-07) — real plugin download provided:** Developer supplied an
actual bBox_1.06_FMP_plug-in download (saved to the Skill Masters folder), containing
Documentation/, per-OS plugin binaries, and the bBox Plug-in Demo.fmp12 file (349 MB, ships with
~200 worked examples). This turned out to be a second primary source independent of the wiki:

- **`strings -n 6` on the demo `.fmp12` file** surfaced an embedded function-reference text index
  (name, signature, description for most of the plugin's surface) baked into its layouts —
  distinct content from the wiki, not a copy of it.
- **`strings -n 4` on the compiled plugin binary**, with the Itanium-mangled `_Z<N>Do_<Name>`
  symbols parsed by length prefix (a plain regex under-matches — the mangled suffix length
  varies), surfaced the plugin's internal handler table directly from the compiled code.
- **Info.plist's `CFBundleVersion`** gave the precise build, `1.06.7` — more specific than the
  wiki's bare "1.06". Recorded as `meta.plugin_build_at_build`.

Net result: **catalog grew from 66 to 87 entries.** Introduced a third `doc_status` tier,
`full_demo_confirmed` (48 entries) — no wiki page, but confirmed via the demo file/binary, which
is materially stronger evidence than the retired `stub_no_wiki_page` reconstruction approach (0
entries remain in that tier; every prior stub was either promoted or, if genuinely unconfirmable,
moved to `meta.known_gaps` instead).

- **One real signature/description discovered for ~20 previously-stub entries**, several with
  corrections to the earlier reconstruction: `bBox_FileWrite` was missing an `unzip` parameter,
  `bBox_FileStatus`'s and `bBox_FileSymLink`'s parameter names were wrong (`path`→`filePath`,
  `useHardLink`→`hardLink`), `bBox_DoScript` was missing a fourth `control` parameter, and
  `bBox_DuckDBOpen`'s signature was far more under-specified than the real one (adds
  columnDelimiter/rowDelimiter/optionName/optionValue).
- **~15 functions added that had no presence anywhere before this review**: the `bBox_HTTP_GET/
  DELETE/PATCH/POST/Option` family, `bBox_FM_Admin_Login`, `bBox_FM_API_Login`,
  `bBox_SchemaMessageHandler`, `bBox_Sort` (previously missing entirely despite being listed on
  Home.md's own Function List), `bBox_ApplescriptLastError`, `bBox_ShellExitStatus`,
  `bBox_MacNotification`, `bBox_WebViewPrint`, `bBox_WebViewHistory` (flagged with a doc_bug_note
  — its description reads as a copy/paste of WebViewLoadFile's), and "bBox Syslog Open"/"bBox
  Syslog Close" script steps.
- **Corrected an earlier wrong exclusion**: "bBox Text Window Action", "bBox Text Window Open",
  and `bBox_SelectMenuItem` were excluded from the original build as unverified (seen only in the
  third-party draft). Both text-window script steps and the menu-item function are confirmed real
  via the demo file and the compiled binary's `Do_TextWindowAction` handler — now catalogued.
- **`bBox_SysCtl` renamed to `bBox_Sysctl`** to match the vendor's own exact casing, confirmed via
  the demo file (FileMaker's calculation engine is case-insensitive for function names, so this
  never affected correctness — a spelling correction, not a behaviour fix).
- **Two functions confirmed explicitly "Not Implemented" by the vendor's own demo file text**:
  `bBox_SQLiteImport` and `bBox_DuckDBRun`. Catalogued with that fact prominent rather than
  omitted, so nobody tries to use either.
- **New `meta.known_gaps` entries for internal-only functions with no confirmed public name**:
  `Do_ListFields`, `Do_ListTables`, `Do_GitOpen`, `Do_CutTextCharacters`, `Do_CutTextFields`,
  `Do_SQLiteExecMany`, `Do_DuckDBOptions`, `Do_AppleMapsWebSnapshot` — real, compiled-in features,
  deliberately not catalogued with an invented signature.
- `SKILL.md`, `README.md`, and `function-catalog.json`'s `meta` block all updated for the new
  three-tier schema and counts. Version kept at v1.0 per the amend-in-place convention — still
  unshipped.

**Third same-day check (2026-07-07) — beezwax.net checked directly:** Developer asked to check
`https://www.beezwax.net/bbox-functions`. This turned out to be a genuine third source: an older
("0.97+"-era, pre-1.06) function index whose ~66 entries mostly duplicate the wiki's own Function
List (down to the same broken DuckDB/SQLite cross-links), but each name links to its own dedicated
`beezwax.net` page in the same rich format as a wiki page. Spot-checked 4 of the catalog's thinner
`full_demo_confirmed` entries against their dedicated page — all 4 yielded real corrections:

- **`bBox_SQLiteClose`**: added `since: 0.80`, full compatibility list, and a proper error result
  (`0, or a SQLite3 error code`) in place of "none confirmed".
- **`bBox_Sort`**: full parameter breakdown recovered (`mode`, `text`, `param1`, `...`) plus the
  correct error reference `bBox_LastError ("posix")` (previously "none confirmed") and richer
  behavioural detail (sortedness-check mode, `-k`/`-u` options).
- **`bBox_DoScript`**: new behavioural facts not in any other source — **not PSoS-compatible**,
  **runs asynchronously** (so `Get(ScriptResult)` can't retrieve its result), **undefined which
  script runs** if multiple scripts share a name, returns `"?"` if no match. Also noted: this older
  page's own signature doesn't show the demo file's `control` fourth parameter — flagged as a
  possible later addition rather than a conflict to silently resolve either way.
- **`bBox_Sysctl`**: confirmed the vendor's own casing a second, independent way (the *index*
  page's link text had a stray "SysCtl", but the actual dedicated page agrees with the demo file
  on lowercase "Sysctl") — this catalog's original spelling correction stands, now doubly
  confirmed. Also added `since: 0.99.11`, full compatibility, and a richer description with
  example output.

Only 4 of 87 entries cross-checked against this source so far — a full sweep (all ~66 linked
pages) was flagged to Developer as worth doing on a future pass rather than assumed to generalize
from these 4. Documented in `meta.update_instructions` for whoever runs that pass next. Version
kept at v1.0 — still unshipped.

**Fourth same-day check (2026-07-07) — full beezwax.net sweep, ahead of GitHub upload:**
Developer asked for the full sweep flagged above, ahead of publishing this skill's repo. All ~60
unique linked pages were fetched (`curl -A "Mozilla/5.0"`, no auth needed) and parsed with a
regex-based extractor (the page format is consistent: `name( signature )`, then Parameters/Result/
Error/Script Step, then "First appeared in / Examples in demo file / Compatibility", then a prose
description after the final divider). 6 URLs — the entire `bBox_WebView*` family — returned HTTP
302 to `/404`: genuinely absent from this older source, not a fetch failure, consistent with
WebView support being a post-"0.97+" addition. Diffed the remaining ~54 real pages against every
matching catalog entry by name and by parameter count.

**~20 entries gained real corrections** (since/compatibility fields, richer descriptions, and in
several cases behavioural facts absent from every other source):
- **`bBox_CurrentTime`**: `since: 0.3`, full compatibility, default format string
  (`"%d:%02d:%02d.%06d"`), and a previously-unknown `useGMT` second parameter (purpose still
  undocumented anywhere).
- **`bBox_FileRead`/`FileWrite`/`FileStatus`/`FileSymLink`**: since/compatibility added to all
  four, plus a shared gotcha not in any other source — **the path parameter is a literal value;
  do not escape characters or enclose it in quotes**. `FileSymLink` also gained: hard-link
  requires same-volume + file-only, and error #17 occurs if the destination already exists.
  `FileWrite`'s result clarified as bytes written (negative on error, not 1:1 with character
  count for non-ASCII text).
- **`bBox_PythonExecute`**: since/compatibility added, plus real behavioural detail — retrieve
  results via `bBox_PythonGetVar` or `fm.executesql`, uncaught exceptions return `"?"` with the
  exception text in `bBox_LastError`, call `bBox_PythonFinalize` when done.
- **`bBox_PythonSetVar`**: compatibility added (missing even from the wiki page itself).
- **`bBox_PythonEval`**: **genuine, unresolved conflict found and deliberately NOT silently
  resolved.** The demo file (1.06-era, three identical occurrences) shows a 2-parameter,
  session-based `(reference; expression)` signature. This older beezwax.net page (since 0.84)
  describes a materially different 1-parameter, session-less `(expression)` version, explicitly
  billed as "a shorter, one-shot version of what PythonCompile/PythonExecute/PythonFinalize
  does" that "invokes Python directly via the Python library" — plus a different error family
  (`bBox_LastError ("python")` vs. `(-3)`). Kept the newer demo-file signature as primary; full
  conflict documented in the entry's `doc_bug_note` rather than guessed away.
- **`bBox_SQLiteOpen`/`Exec`/`Options`**: since/compatibility added to all three; `SQLiteOpen`'s
  own vendor docs explicitly note it was slated for replacement by `bBox_DuckDBOpen` in 1.05 —
  now recorded directly in the entry rather than only in category notes.
- **`bBox_Sysctl`**: since `0.99.11`, full compatibility, richer description with example output
  (`user.cs_path`, etc.) — and a second, independent confirmation of the vendor's lowercase
  "Sysctl" casing (the index page's own link text had a stray capital-C typo; the actual
  dedicated page agrees with the demo file).
- **`bBox_ThreadInfo`**: since/compatibility added, output format detailed (thread #, CPU usage,
  priority, run state, etc.), and clarified which process is measured under PSoS/schedules (the
  FMS script engine, not the interactive client).
- **`bBox_StackBacktrace`**: since `0.99.14`, full compatibility, clearer description of what a
  frame contains — the worked example's raw memory addresses/engine symbols weren't reproduced,
  since they're build-specific noise rather than something to rely on.
- **`bBox_URLHandlerInfo`**: since `0.99.15`, and a narrower compatibility list than assumed
  (Client/macOS/Server only — no Ubuntu/WebDirect/WPE), plus confirmation the result is JSON.
- **`bBox_XPathInitializeFromText`/`Evaluate`/`Finalize`**: compatibility added to all three;
  `XPathEvaluate` gained a real limitation — namespace selection for XPath expressions isn't
  currently possible.
- **`bBox_Zsh`**: `since: 0.95` and the 160 MB stdout truncation limit confirmed (already had
  this from the demo file; now doubly sourced).

No further genuine signature conflicts were found across the rest of the wiki-backed (`full`)
entries checked this pass (Bash/Curl/FileWrite/Sips showed only cosmetic ellipsis-formatting
differences, not real ones). `meta.update_instructions` updated with the exact retrieval/parsing
method for a future re-sweep. Version kept at v1.0 per the amend-in-place convention — still
unshipped, ahead of the planned GitHub publish.
