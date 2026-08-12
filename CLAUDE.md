# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is **not** an application — it's a curated, documentation-only catalog of Claude Skills ("Awesome Claude Skills"), maintained by Composio. There is no build system, package manifest, linter, or test suite. "Development" here means editing Markdown files and skill folders, not compiling or running code.

Two kinds of content live side by side:

1. **The catalog** (`README.md`) — a categorized list of skills, most of which live in *other* people's repos and are only linked to.
2. **First-party skill folders** — actual `SKILL.md` packages hosted directly in this repo, each usable on its own by copying the folder into a Claude Skills directory.

## Repository structure

```
README.md              Master catalog — the primary deliverable of this repo
CONTRIBUTING.md         Contribution rules + SKILL.md template for new skills
template-skill/         Blank skeleton to copy when starting a new first-party skill
skill-<name>/           ~30 first-party skills, each a folder with SKILL.md (+ optional
                         scripts/, references/, templates/, LICENSE.txt)
document-skills/        Multiple related skills nested one level deeper
                         (docx/, pptx/, pdf/, xlsx/), each with its own SKILL.md
composio-skills/        ~830 auto-generated, near-identical "X-automation" skills,
                         one per SaaS app integration reachable via Rube MCP (Composio)
connect/, connect-apps/ Skills showing how to wire Claude to external apps via Composio
connect-apps-plugin/    An actual Claude Code *plugin* (not a skill) — has
                         .claude-plugin/plugin.json and commands/setup.md
.github/workflows/      CI that gates community PRs adding external skill links to README.md
```

Every first-party skill folder follows the same minimal shape:

```
skill-name/
├── SKILL.md      # required: YAML frontmatter (name, description) + Markdown instructions
├── scripts/      # optional: helper scripts the skill invokes
├── references/   # optional: docs loaded on demand
├── templates/    # optional: starter files/assets
└── LICENSE.txt   # optional, present on Anthropic-authored skills
```

`composio-skills/*/SKILL.md` files share one pattern: `requires: mcp: [rube]` frontmatter, then a workflow of `RUBE_SEARCH_TOOLS` (discover tool schemas) → `RUBE_MANAGE_CONNECTIONS` (verify/establish OAuth to the target app) → execute the discovered tool. When touching one of these, match the sibling skills' structure rather than inventing a new format.

## Working in this repo

There is nothing to build, lint, or test. The closest thing to validation is:

- **Skill frontmatter sanity check**: `head -n5 <skill-dir>/SKILL.md` should show valid YAML with at least `name` and `description`.
- **README structural check**: the CI workflow below is the real validator; there's no local equivalent to run, so mirror its rules by hand before opening a PR.

### Adding a new *external* skill listing to README.md

This is the most common contribution and is enforced by `.github/workflows/label-ready-skill.yml` on every PR. The workflow auto-labels a PR `ready-to-merge` only if **all** of these hold — violating any one fails CI:

- The PR touches **only** `README.md` (no other files changed).
- All diffs fall strictly between the `## Skills` and `## Getting Started` headers — nothing before or after that window may change.
- At least one new bullet of the form `- [Name](https://external-url) - Description.` is added.
- Every added link is an external `http(s)://` URL, and must **not** point at a `composio.dev` or `anthropic.com` host (first-party/sponsor links go through a different path, not this workflow).
- No added line contains crypto/web3 terms (`crypto`, `web3`, `blockchain`, `nft`, `defi`, `token(omics)`, `wallet`, `solana`, `ethereum`, `bitcoin`) — these are hard-blocked.
- New bullets must sit in correct case-insensitive alphabetical order relative to their immediate neighbors within their `###` category (pre-existing disorder elsewhere is grandfathered, but new entries must not introduce new disorder).

Practical implication: when asked to "add skill X", edit only `README.md`, insert the bullet in the right `###` category in alphabetical position, and use the exact format `- [Name](url) - One-sentence description.` (see `CONTRIBUTING.md` for the full template and attribution conventions like `*By [@user](https://github.com/user)*` or `**Inspired by:** ...`).

### Adding a new first-party skill (hosted in this repo)

1. Copy `template-skill/` (or an existing similar skill) to a new top-level `skill-name/` folder (lowercase, hyphenated).
2. Write `SKILL.md` with YAML frontmatter (`name`, `description`) followed by instructions — see `CONTRIBUTING.md` for the expected section layout (When to Use, What This Skill Does, How to Use, Example, Tips).
3. Add a corresponding bullet to the correct category in `README.md`, using a relative link (`./skill-name/`) instead of an external URL. Note this path is *not* what the CI workflow above validates — it's aimed at pure external-link PRs — so don't rely on the bot to catch mistakes here.

### Editing an existing skill

Just edit the `SKILL.md` (and any `scripts/`/`references/`/`templates/`) in place; there's no build step to re-run afterward.

## Conventions worth knowing

- Skill folder names and README anchors use lowercase-hyphenated slugs matching the folder name.
- `composio-skills/` is large (800+ folders) and mechanically generated — prefer targeted `Glob`/`Grep` over broad directory listings when working there.
- The repo has no root LICENSE; it's Apache-2.0 per `README.md`, but individual skill folders may carry their own `LICENSE.txt`.
- Git history is almost entirely single-purpose "Add \<skill\> skill" PRs against `README.md` — keep unrelated changes out of the same commit/PR.
