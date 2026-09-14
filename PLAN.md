# Talk plan: What's Up, Mockup? (PloneConf 2026)

Frame:
- 25 min total. Peter ~11 min (Mockup, contentbrowser, filemanager), Johannes ~12 min (Patternslib), ~2 min Q&A.
- Short and informative. Contentbrowser and filemanager are shown LIVE in the browser; slides only frame the
  demos and carry the key messages. No feature-list slides, the features get clicked.
- Tooling: `talk.md` (`\n\n\n\n` = new section, `\n\n` = vertical slide), one background color per section.
  Live demos: a plain "Live DEMO" slide, then switch to the browser tab with the running Plone instance
  (URL is in the speaker notes), switch back to the slides afterwards.

Facts (as of 2026-09-14):
- Plone 6.1 → plone.staticresources 2.x → Mockup 5.4.x.
- Plone 6.2 → plone.staticresources 3.0.x → Mockup 5.6.x (currently 5.6.11 from 2026-09-11, 5.6.0 from 2026-03-24).
- Plone 6.3 (coredev) → plone.staticresources 3.1.0a2 → Mockup master / 5.7.0-alpha.0 (2026-06-08).
- 5.6.x stack: Patternslib 9.10.6, Bootstrap 5.3.8, TinyMCE 8.6.0, Svelte 5, @plone/registry,
  pnpm 11 (switched from Yarn in 5.6), Webpack Module Federation. Legacy still on board: jQuery 3.7, Backbone, underscore (pat-structure only).
  DataTables is fine: pat-datatables is a supported pattern and DataTables gets updated. The real pain is the old,
  custom-patched select2 fork used by pat-relateditems; hopefully removed in Mockup 6 (Plone 7).
- Mockup 6 (Plone 7) outlook: Backbone dropped, jQuery updated to v4, select2 fork hopefully gone.
- pat-relateditems deprecated → pat-contentbrowser (Svelte 5, classic writable stores).
  pat-structure → pat-filemanager (5.7, restapi only, Svelte 5 runes: $state/$derived in .svelte.ts store classes).
- pat-filemanager was vibe coded with AI during the 5-day "Buschenschank" sprint. Size today:
  29 Svelte components, 13 rune stores, 25 test files, ~8.6k lines of code + ~3.7k lines of tests.
- Local demo sources: buildout.coredev-6.2/src/mockup (5.6.x), buildout.coredev-6.3/src/mockup (master, 5.7.0-alpha.0).
- Filemanager is NOT wired up in Plone 6.3 yet (no branch): folder_contents still renders pat-structure. For the
  demo, override the folder_contents template via z3c.jbot (or an own browser view) with a `pat-filemanager` div
  reusing `view/options`. In the local coredev 6.3 the egg template of plone.app.content 5.0.0 is patched by hand.

## Time budget Peter (~11 min)

| # | Section                     | min | Slides | Content                                                              |
|---|-----------------------------|-----|--------|----------------------------------------------------------------------|
| 0 | Title                       | 0.5 | 1      | exists                                                               |
| 1 | Mockup in 2 minutes         | 2   | 3      | What/who, version matrix, 5.6 stack (+ legacy punchline: select2)    |
| 2 | Now: pat-contentbrowser     | 4   | 3      | What's new (1 slide), LIVE DEMO, extending it (dev slide)            |
| 3 | Next: pat-filemanager       | 4   | 6      | Why, LIVE DEMO, what it is, how it was built (AI sprint), status, try it (jbot) |
| 4 | Handover                    | 0.5 | 1      | "Johannes: Patternslib"                                              |

Johannes (~12 min): Patternslib, own section(s), placeholder in talk.md.
Wrap-up (both, 1 min): timeline + links, questions.

## Live demo script

Contentbrowser (Plone 6.2, ~2.5 min of clicking):
1. Edit a page → open the related items field → browse the miller columns.
2. Search mode, level filter, favorites / recently used.
3. Upload directly inside the widget (image), lands in the field immediately.
4. TinyMCE link dialog: same browser, images only (accepted mimetypes).
5. Single select (maximumSelectionSize 1) on another field, e.g. querystring path in a collection.

Filemanager (coredev 6.3 + jbot override, ~2.5 min of clicking):
1. Open folder_contents → table view, sort a column (whole result set, not just the page).
2. Switch to grid view, size slider.
3. Drag & drop: reorder, then drag an item into a subfolder.
4. Drop a whole folder from the desktop → preview → confirm.
5. Multi-select → batch workflow in the native dialog. Show the querystring filter briefly.

Preparation:
- Both instances running beforehand, logged in, demo content in place (folder with images, subfolders).
- Filemanager demo: the hand-patched egg template gets lost on a rebuild; move the override into a small
  demo add-on with z3c.jbot before the conference.
- Browser zoom up (125–150 %), test the window at presentation size.
- A folder with 5–10 images on the desktop for the folder drop.
- Record a screencast (WebM) of both demos as an emergency fallback.
