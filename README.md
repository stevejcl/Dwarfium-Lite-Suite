# Dwarfium Lite Suite

📖 **https://stevejcl.github.io/Dwarfium-Lite-Suite/**

The documentation of the Dwarfium applications for DWARF II, DWARF 3 and DWARF mini telescopes:

| Application | What it does | Repository |
|---|---|---|
| **Dwarfium Hub** — the entry point | Install, update and start the other two; link your Dwarfs; synchronize installations | [dwarfium-hub](https://github.com/stevejcl/dwarfium-hub) · [download](https://github.com/stevejcl/dwarfium-hub/releases/latest) |
| **Dwarfium Lite** (Astro Dwarf Session) | Drive your Dwarfs: programs, scheduling, native schedules, mosaics | [astro_dwarf_session](https://github.com/stevejcl/astro_dwarf_session) |
| **Dwarfium Scope Archive** | Back up, explore, process and plan your sessions | [dwarfium-scope-archive](https://github.com/stevejcl/dwarfium-scope-archive) |

The site is built with [Zensical](https://zensical.org) (by the authors of Material for MkDocs, reading `mkdocs.yml`) from the Markdown files in [`docs/`](docs/) and published to GitHub Pages by [`.github/workflows/docs.yml`](.github/workflows/docs.yml) on every push to `main`. How to add or change a page: [Contribute to the docs](docs/contributing.md).

```sh
pip install -r docs/requirements.txt
zensical serve
```

MIT License.
