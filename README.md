# throneproj.github.io

Documentation for [Throne](https://github.com/throneproj/Throne) and
[Throne for Android](https://github.com/throneproj/ThroneForAndroid), published at
<https://throneproj.github.io>. Pull requests are welcome.

The site is built with [Hwaro](https://github.com/hahwul/hwaro) and is available in
English, Persian (فارسی), Russian (Русский) and Simplified Chinese (简体中文).

## Layout

| Path | What it holds |
| --- | --- |
| `content/<section>/<page>.md` | English pages; `<page>.fa.md`, `<page>.ru.md` and `<page>.zh.md` are the translations |
| `content/<section>/_index.md` | Section overview pages |
| `data/sidebar_<lang>.yml` | The sidebar of each language |
| `data/versions.yml` | Release versions for the Downloads page (the deploy refreshes it) |
| `i18n/<lang>.toml` | Interface strings used by the templates |
| `templates/`, `static/` | Theme |
| `scripts/check_docs.py` | Consistency checks run by CI |

## Writing a page

1. Copy `archetypes/default.md` to `content/<section>/<page>.md` and fill in
   `title`, `description` and `weight` (the order within the section).
2. Write for readers who may use a translator: short sentences, the app's exact
   English labels in backticks (`Settings` → `Tun Settings`), and numbered steps.
   Keep it short: describe the current release (no "since 1.x" notes or history),
   skip what the app already makes obvious, and link to the page that owns a topic
   instead of repeating it.
3. End every H2 with an explicit id, `## Simple rules {#simple-rules}`, and keep the
   same ids in every translation so links to a section work in all languages.
4. Link other pages with Hwaro's `@/` syntax, pointing at the file in the same
   language: `[Routing](@/guides/routing.md)` in English,
   `[…](@/guides/routing.fa.md)` in Persian. Never write `/guides/routing/`.
5. Notes and warnings use the theme's shortcodes:

   ```text
   {% alert_info() %}
   Text of the note.
   {% end %}
   ```

6. Add the translations (UI labels as the translated app shows them, with the
   English label in parentheses) and add the page to all four sidebar files.
7. Run the checks:

   ```bash
   pip install pyyaml
   python3 scripts/check_docs.py
   ```

## Publishing

Every push to `main` builds and deploys the site. The deploy reads the latest
desktop and Android releases from GitHub and links the Downloads page to them; an
hourly scheduled run redeploys when a new release appears. To pin other versions,
run the **Hwaro CI/CD** workflow by hand and fill in the version fields. Pull
requests are checked by the **Check docs** workflow.
