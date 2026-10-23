# Contribute to the docs

These docs are plain Markdown files in the `docs/` folder of [Dwarfium-Lite-Suite](https://github.com/stevejcl/Dwarfium-Lite-Suite/tree/main/docs), published automatically to GitHub Pages on every change to `main`. The ✎ icon at the top of each page opens it for editing on GitHub.

## How the docs are organised

| Section | Answers | Write it as |
|---|---|---|
| **Get started** | "I'm new, where do I begin?" | A walk-through, in order |
| **I want to…** | "How do I do X?" | Short recipes: one `##` per need, the app and the button first |
| **The apps** | "What can this app do?" | One table per screen, each line linking to a recipe |
| **Reference** | "What is X? Where is Y?" | Glossary, tables, troubleshooting |
| **What's new** | "What changed?" | A few lines per version, linking to a recipe |

The rule that keeps it light to maintain: **a feature is explained once**, in its *I want to…* recipe. Everywhere else just links to it.

## When a feature is added

In the same pull request as the feature, or right after it — in this repository, whichever app changed.


1. Add a `##` section to the right page of `docs/how-to/` (template below), with an `{ #anchor }`.
2. Add a line for it in `docs/how-to/index.md`.
3. Add a line in the app's table in `docs/apps/`, and in `docs/whats-new.md`.

```markdown
## Do something useful { #do-something }

**App:** Dwarfium Lite → page → **Button**

One sentence on what it does and when to use it.

1. Step.
2. Step.

!!! tip
    The thing people get wrong.
```

## Preview locally

```sh
pip install -r docs/requirements.txt
zensical serve           # http://127.0.0.1:8000, reloads on save
zensical build --strict  # what the CI runs: fails on a broken link or anchor
```
