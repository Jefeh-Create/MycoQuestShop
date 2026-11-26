# MycoQuest translations

Both pages (`Index.html` and `lab.html`) load the same files:

| File | What it is |
| --- | --- |
| `en.js` | English. The source text and the fallback for every other language. |
| `es.js` | Spanish for U.S. visitors. Complete draft; `meta.review` lists lines that need expert review. |
| `hmn.js` | Hmong. Prepared but empty and switched off (`enabled: false`). |
| `i18n.js` | The runtime: lookup, fallback, plurals, the language selector, and date and number formatting. |

## How text gets on the page

- Static markup: `data-i18n="key"` sets an element's text. `data-i18n-attr="aria-label: key; placeholder: key"` sets attributes.
  Elements inside it with `data-slot="name"` (such as links) are kept and placed where the text says `{name}`.
- Script: `t("key", { name: value })`. Plural entries are objects such as `{ one: "...", other: "..." }`, chosen with `count`.
- A key missing from a language shows the English text, marked `lang="en"`, and logs a console warning.

## What is never translated

Scientific names (marked `lang="la"`), people's and business names, package sizes and measurements, and anything a person
typed: grow names, notes, supplier details, catalog drafts. Saved records keep English values for stages, statuses, and
types, so changing language never rewrites data or backups.

## The language setting

The choice is saved in the browser under the key `mycoquest-language`, separately from all records. It holds only a
language code. English is used until someone picks another language; the browser's own language is not used to switch
automatically.

## Adding a language

1. Copy `hmn.js` to `<code>.js`. Set `name` (the language's own name for itself), `htmlLang` (such as `vi` or `fr-CA`),
   and keep `enabled: false`.
2. Translate every key in `en.js` (both sections: the demo and the prep lab). Keep `{placeholders}` exactly as they are.
3. Add `<script src="i18n/<code>.js"></script>` to both pages, after `es.js` and before `i18n.js`.
4. Run `node tools/check-translations.mjs` from the repository root. It lists missing keys, changed placeholders, and
   incomplete plurals.
5. Have a fluent speaker who did not write the translation review it in the running pages, including at phone width.
   Supplements, tinctures, food safety, and cultivation guidance also need a subject expert.
6. Set `enabled: true`. The language then appears in the selector on both pages.

## Hmong

Not enabled. It needs a fluent Hmong translator, a decision on dialect (for example Hmong Daw or Mong Leng) and writing
system, and a second reviewer. Machine translation must not be published as the finished text.
