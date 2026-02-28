---
name: quartz-blog-frontmatter-normalizer
description: Use when preparing Quartz markdown posts for deployment and you must normalize filenames to kebab-case, fill missing frontmatter defaults, translate/normalize tags, and preserve the article body unchanged.
---

# Quartz Blog Frontmatter Normalizer

## Overview

Use this skill to standardize Quartz blog markdown posts before deployment.

Non-negotiable rules:
- Rename markdown filename to kebab-case.
- Keep article body unchanged.
- Add missing `description` with a short Chinese summary.
- Add missing `draft` and default it to `true`.
- Convert Chinese `tags` to English and normalize to kebab-case.
- If the filename is renamed, append the original title to `aliases`.

## Required Output Rules

1. Only edit YAML frontmatter and filename.
2. Do not modify content below frontmatter delimiter (`---`).
3. Preserve existing frontmatter keys unless they conflict with required rules.
4. Keep `draft` value if already present; only default to `true` when missing.
5. Only modify `aliases` when the filename is renamed; never overwrite existing `aliases` entries.

## Workflow

1. Locate target markdown files (usually under Quartz content folder).
2. For each file:
   - Read and parse frontmatter.
   - Ensure `description` exists:
     - If missing or empty, generate one short Chinese sentence summarizing the post.
   - Ensure `draft` exists:
     - If missing, set `draft: true`.
    - Ensure `tags` are English kebab-case strings:
      - Translate Chinese tags to English first.
      - Normalize each tag to kebab-case (`lowercase-words-with-hyphen`).
    - Rename file stem to kebab-case.
    - If the filename changed, ensure `aliases` includes the original `title`:
      - If `aliases` missing, create it.
      - If `aliases` exists, append (do not replace).
      - De-duplicate if the title is already present.
      - If `title` is missing, report it and do not add `aliases`.
3. Verify no article body lines changed.
4. Report changed files and any tags that required manual translation choices.

## Tag Conversion Policy

- Prefer explicit translation for domain terms (example: `前端` -> `frontend`, `后端` -> `backend`).
- Avoid pinyin unless user explicitly requests it.
- If a Chinese tag is ambiguous, stop and ask for a one-time mapping decision.

## Filename Policy

- Use kebab-case ASCII filename stems.
- Keep `.md` extension.
- If source filename is non-ASCII and no clear English translation is available, ask for slug mapping before rename.

## Verification Checklist

- Every target file has `description` and `draft` in frontmatter.
- `draft` defaults to `true` only when originally missing.
- All tags are English kebab-case.
- Markdown body is byte-equivalent before vs after.
- Renamed filenames are kebab-case.
- For renamed files, `aliases` contains the original `title`.
- For non-renamed files, do not add or modify `aliases`.
