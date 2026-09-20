<!-- .slide: data-background="lime" -->
<!-- .slide: data-background="Blue" -->
<!-- .slide: data-background="Yellow" -->
<!-- .slide: data-background="Purple" -->
<!-- .slide: data-background="DarkViolet" -->
<!-- .slide: data-background="Cyan" -->

<!-- .slide: data-background="Blue" -->
# What’s Up, Mockup?

<br>
News from Mockup and Patternslib
<br>

<footer>
  <a href="https://thet.github.io/talk-ploneconf2026-mockup">
    https://thet.github.io/talk-ploneconf2026-mockup
  </a>
  <br>
  @ Plone Conference 2026
  <br>
  <br>
  Peter Mathis, <a href="https://www.kombinat.at/">Kombinat Media Gestalter GmbH</a><br>
  Johannes Raggam, <a href="https://www.syslab.com/">syslab.com GmbH</a><br>
</footer><!-- .element: class="footnote mt-8" -->




<!-- .slide: data-background="Yellow" -->
## Mockup in 2 minutes


<!-- .slide: data-background="Yellow" -->
### What is Mockup?

- The JavaScript behind <strong>Blicca</strong> (formerly Classic UI)
- A collection of <strong>patterns</strong>: declare behavior in HTML
- Built on top of <strong>Patternslib</strong>
- Shipped to Plone via <strong>plone.staticresources</strong>

```html
<input class="pat-contentbrowser" data-pat-contentbrowser='{"selectableTypes": ["Image"]}' />
```
<!-- .element: class="fragment" -->

Note:
TODO: optional Stack-Diagramm Patternslib → Mockup → plone.staticresources → Plone.


<!-- .slide: data-background="Yellow" -->
### Which version is where?

| Plone | plone.staticresources | Mockup            |
|-------|-----------------------|-------------------|
| 6.1   | 2.x                   | 5.4.x             |
| 6.2   | 3.0.x                 | **5.6.x** (5.6.11, Sept 2026) |
| 6.3   | 3.1.x                 | **5.7.x** (5.7.0-alpha.0)     |

Note:
5.6.0 released 2026-03-24, 5.6.11 on 2026-09-11 – 11 bugfix releases in 6 months.
Plone 6.1: staticresources 2.3.8 / Mockup 5.4.10.
Why staticresources 3.x? Not because of Mockup: 3.0.0a1 (Nov 2025) switched from pkg_resources to a
PEP 420 native namespace and dropped everything below Plone 6.2 / Python 3.10 – part of the Plone-wide
packaging cleanup. Mockup 5.5 (TinyMCE 8) and 5.6 then landed on that already-open 3.0 line; final 3.0.0
(May 2026) shipped Mockup 5.6.4. Same pattern presumably for 3.1.x and Plone 6.3.
TODO: verify whether staticresources 3.1.0a2 already bundles 5.7.0-alpha.0.


<!-- .slide: data-background="Yellow" -->
### The 5.6 stack

- Patternslib 9.10
- Bootstrap 5.3, Bootstrap Icons
- TinyMCE 8
- <strong>Svelte 5</strong>
- @plone/registry, Webpack Module Federation
- <strong>pnpm</strong> instead of Yarn (since 5.6)

<p>… and still: jQuery, Backbone, underscore.</p><!-- .element: class="fragment" -->

<p>The real PITA: an old, custom-patched <strong>select2</strong> fork (pat-relateditems).<br>
Hopefully gone in Mockup 6 (Plone 7).</p><!-- .element: class="fragment" -->

Note:
DataTables is fine: pat-datatables is a supported pattern and DataTables gets updated.
Backbone and underscore only live in pat-structure (see filemanager). The select2 fork is the one
we cannot upgrade, and pat-relateditems is the last big consumer.
Mockup 6 (Plone 7): Backbone dropped, jQuery updated to v4, select2 fork hopefully gone.




<!-- .slide: data-background="lime" -->
## Now: pat-contentbrowser


<!-- .slide: data-background="lime" -->
- Replaces <code>pat-relateditems</code> (deprecated)
- Offcanvas <strong>miller columns</strong> to browse, search and pick content
- Used by relation fields, TinyMCE link/image dialog, querystring path criteria
- Since 5.6: <strong>Svelte 5</strong>


<!-- .slide: data-background="lime" -->
Live DEMO<!-- .element: class="big center middle" -->

Note:
DEMO 1 – Contentbrowser (Plone 6.2):
1. Edit page → related items field → browse miller columns.
2. Search mode, level filter, favorites, recently used.
3. Upload an image inside the widget → lands in the field immediately.
4. TinyMCE link dialog → same browser, images only (accepted mimetypes).
5. Single select (maximumSelectionSize: 1), e.g. querystring path in a collection.
Switch to browser tab: http://localhost:8080/Plone/demo-page/edit (TODO: final URL)


<!-- .slide: data-background="lime" -->
### Extend it

Register your own Svelte component for the selected items:

```js [1-8|4-7]
import plone_registry from "@plone/registry";

async function register() {
    plone_registry.registerComponent({
        name: "pat-contentbrowser.SelectedItem",
        component: (await import("./MySelectedItem.svelte")).default,
    });
}
```

<p>
  Add-on bundles share the <code>svelte</code> runtime via module federation (5.6.9).<br>
  Registering under the default key replaces it site-wide (5.6.11).
</p><!-- .element: class="fragment footnote" -->

Note:
Contentbrowser runs on Svelte 5 but still uses the classic writable() stores from svelte/store,
not runes. Runes (explicit $state/$derived in .svelte.ts classes) are used in the filemanager.
Or use a custom key and point the pattern option componentRegistryKeys.selectedItem at it.
Patterns are initialized inside the SelectedItems template as well (5.6.9).




<!-- .slide: data-background="Cyan" -->
## Next: pat-filemanager


<!-- .slide: data-background="Cyan" -->
### Why?

<code>pat-structure</code> (folder contents) is …

- Backbone + underscore + DataTables
- ~10 custom Plone JSON views (<code>/cut</code>, <code>/paste</code>, <code>/rearrange</code>, …)
- sorting only within the current page
- hard to extend


<!-- .slide: data-background="Cyan" -->
Live DEMO<!-- .element: class="big center middle" -->

Note:
DEMO 2 – Filemanager (coredev 6.3 with Mockup 5.7.0-alpha.0; Plone does not wire it up yet,
folder_contents is overridden via z3c.jbot – see the "Try it" slide):
1. folder_contents → table view, sort a column (whole result set, real dates).
2. Switch to grid view, size slider.
3. Drag & drop: reorder, then drag an item into a subfolder.
4. Drop a whole folder from the desktop → preview → confirm.
5. Multi-select → batch workflow in native dialog. Show querystring filter briefly.
Switch to browser tab: http://localhost:8081/Plone/demo-folder/folder_contents (TODO: final URL)


<!-- .slide: data-background="Cyan" -->
### What it is

- <strong>plone.restapi only</strong> – no custom views, no backend changes
- Svelte 5 runes, state in store classes
- No Backbone, no underscore, no DataTables
- Native <code>&lt;dialog&gt;</code>, ARIA menus, keyboard navigation


<!-- .slide: data-background="Cyan" -->
### Legacy views → restapi

| Legacy view    | restapi                          |
|----------------|----------------------------------|
| vocabularyUrl  | `@querystring-search`            |
| /cut /paste    | `@copy` / `@move`                |
| /workflow      | `@workflow`                      |
| /rename, /properties | `PATCH` (client-side loop) |
| upload         | `@tus-upload`                    |


<!-- .slide: data-background="Cyan" -->
### How it was built

- 5 days, one "Buschenschank" sprint
- <strong>vibe coded</strong> with AI, spec first
- 29 Svelte components, 13 stores, 25 test files
- ~8.6k lines of code, ~3.7k lines of tests

<p>A tool of this size in this time would not have been possible without AI.</p><!-- .element: class="fragment" -->

Note:
Spec-driven: pat-filemanager-spec.md with locked decisions, then iterate with the AI on components,
stores and tests. Humans reviewed, tested in the browser, and kept the architecture on track.


<!-- .slide: data-background="Cyan" -->
### Status

- 5.7.0-alpha.0, target: <strong>Plone 6.3</strong>
- Out of scope for now: add-content menu
- Ideas: miller column view, reuse in contentbrowser
- Not wired up in Plone yet: <code>folder_contents</code> still renders <code>pat-structure</code>


<!-- .slide: data-background="Cyan" -->
### Try it: override folder_contents

Mockup 5.7.0-alpha.0 bundle + z3c.jbot override in your add-on:

```xml
<include package="z3c.jbot" file="meta.zcml" />
<browser:jbot directory="overrides" layer=".interfaces.IMyLayer" />
```

```html
<!-- overrides/plone.app.content.browser.contents
              .templates.folder_contents.pt -->
<metal:content-core fill-slot="content-core">
  <span tal:replace="structure
                     context/@@authenticator/authenticator" />
  <div class="pat-filemanager"
       tal:attributes="data-pat-filemanager view/options" />
</metal:content-core>
```

<p>Same view, same options JSON – the pattern reads the legacy <code>pat-structure</code> options.</p><!-- .element: class="fragment footnote" -->

Note:
Or register your own browser view for folder_contents on your layer.
The stock FolderContentsView still provides view/options (vocabularyUrl, urlStructure, …);
pat-filemanager picks urlStructure.base as portalUrl and derives contextUrl from the page URL.
Keep the rest of the template (main_template macro, top_slot disabling the columns) as in the original.
Report issues on github.com/plone/mockup.




<!-- .slide: data-background="Purple" -->
## Patternslib


<!-- .slide: data-background="Purple" -->
### What is it?

- JavaScript framework for frontend UI functionality
- Define and register "Patterns"


<!-- .slide: data-background="Purple" -->
### Idea

- Daniël Kohlbach (Cornelis), 2009
- Let UI designers add JavaScript functionality without writing JavaScript<!-- .element: class="fragment" -->


<!-- .slide: data-background="Purple" -->
### Development

- Initial implementation: Guillaume Potier
- Wichert Akkerman, Florian Friesdorf, JC Brand, Yiorgis Gozadinos, Fullvio Casali, Alexander Pilz, Johannes Raggam
- ES6 Modernization in 2020


<!-- .slide: data-background="Purple" -->
### Mockup

- Rok Garbas forked in 2012 from Patternsib
- JC Brand Back to Patternslib in 2015
- ES6 Modernization in 2022




<!-- .slide: data-background="Purple" -->
## What's up?


<!-- .slide: data-background="Purple" -->
### pat-bind

- Data binding Pattern
- Why: Update UI without pat-inject
- How: Based on JavaScript Signals

<!-- Why: Allow to update parts of the UI without reloading via pat-inject -->


<!-- .slide: data-background="Purple" -->
### pat-bind demo

Demo


<!-- .slide: data-background="Purple" -->
###  Internals

- Dependency management: pnpm<!-- .element: class="fragment" -->
- Build framework: Vite<!-- .element: class="fragment" -->
- Test framework: Vitest<!-- .element: class="fragment" -->
- Module federation<!-- .element: class="fragment" -->


<!-- .slide: data-background="Purple" -->

- Remove Modernizr<!-- .element: class="fragment" -->
- Remove jQuery<!-- .element: class="fragment" -->
- Dependencies major updates<!-- .element: class="fragment" -->


<!-- .slide: data-background="Purple" -->

- Split core from Patterns<!-- .element: class="fragment" -->
- Split Patterns with huge external dependencies<!-- .element: class="fragment" -->




<!-- .slide: data-background="lime" -->
## Where this is going

- 5.6 → Plone 6.2: contentbrowser, Svelte 5, TinyMCE 8
- 5.7 → Plone 6.3: filemanager, restapi only
- Mockup 6 → Plone 7: drop Backbone and the select2 fork, jQuery 4
- Patternslib 10 & Patternslib core: Plone 7




<!-- .slide: data-background="lime" -->
## Links

- https://github.com/plone/mockup
- https://github.com/plone/mockup/blob/master/CHANGES.md
- https://patternslib.com
- https://github.com/patternslib/Patterns
- https://thet.github.io/talk-ploneconf2026-mockup




<!-- .slide: data-background="Purple" data-background-image="./resources/imgs/thats_all_folks.svg" -->




<!-- .slide: data-background="Black" -->
Questions?<!-- .element: class="big center middle" -->
