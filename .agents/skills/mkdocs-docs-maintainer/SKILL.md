---
name: mkdocs-docs-maintainer
description: Maintain MkDocs-based knowledge repositories when new documents, sections, or hub pages are added. Use when Codex needs to inspect a repo, identify newly added Markdown content, and synchronize README.md, docs/index.md, mkdocs.yml navigation, and section README pages so the site structure and contributor guidance stay consistent.
---

# MkDocs Docs Maintainer

## Overview

Synchronize the entry points of a MkDocs knowledge repository after documentation changes land. Keep human-facing guidance and MkDocs navigation aligned without rewriting the source documents unless the request explicitly requires it.

## Workflow

1. Inspect before editing.
Run `git branch --show-current`, `git status --short`, and `rg --files` to locate `mkdocs.yml`, `README.md`, `docs/index.md`, section hub pages, and the new documents. Read the current files with commands such as `sed -n` or `rg -n`. Do not edit until the synchronization scope is clear.

2. Decide the synchronization scope.
Always consider `README.md`, `docs/index.md`, and `mkdocs.yml`. Update section hub pages such as `docs/<section>/README.md` when the new document belongs to an existing section and should be discoverable there. Avoid exposing raw data files, screenshots, or internal artifacts in navigation unless the repo already treats them as reader-facing content.

3. Update repository guidance.
Reflect the repository purpose, major sections, and contributor or publish flow in `README.md`. Mention `mkdocs serve`, `mkdocs build`, and `mkdocs gh-deploy` only when that matches the repository's actual workflow or a clearly supported inference. Prefer concrete paths and branch names over vague wording.

4. Update landing pages.
Add or revise links in `docs/index.md` so the new documents are reachable from the landing page. Preserve section ordering unless there is a clear information-architecture problem. Use short, human-readable labels that match the document title or section naming.

5. Update `mkdocs.yml`.
Verify `site_name`, `docs_dir`, `site_dir`, `theme.language`, and `nav`. Add new pages to `nav` only when they are intended for readers. Keep the navigation shallow and grouped by section. Keep every path exact and relative to `docs/`.

6. Validate the result.
Review `git diff -- README.md docs/index.md mkdocs.yml` plus any section hub pages you touched. Run `mkdocs build` when available. If MkDocs is missing, state that validation was attempted but blocked by the missing dependency. If the repo tracks generated `site/`, note whether regeneration is now required.

## Editing Heuristics

- Prefer minimal, targeted edits over broad rewrites.
- Preserve the repository's existing naming language and section order when possible.
- Fix inconsistent branch or deployment wording instead of copying it forward.
- Distinguish confirmed facts from assumptions in user-facing communication.
- Add a minimal `nav` when none exists, but avoid turning support files into top-level pages.

## Common Requests

- `README.md の整理必要だね。ymlにも適切にして`
- `新しい Markdown を追加したのでトップと nav に反映して`
- `MkDocs の構成を崩さずに README と index を同期して`

## Output Expectations

- Report which entry points were updated.
- State whether `mkdocs build` succeeded, failed, or could not run.
- Call out remaining assumptions, especially around deployment flow and whether `site/` should be regenerated.
