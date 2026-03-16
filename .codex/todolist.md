# Task Plan (Aligned with requirements-spec.md)

> Source of truth: `/Users/codeshareman/Documents/ME/GitHub/Lyra/.codex/requirements-spec.md`

---

## Epic 1 — Config Structure & Merge (P0)
**Outcome:** Global defaults + template overrides + module-level overrides work with backward compatibility.

### Task 1.1 — Spec alignment
- [x] Consolidate requirements into `requirements-spec.md`
- [x] Document merge precedence & compatibility

### Task 1.2 — Merge utilities
- [x] `mergeAIConfig(global, template)` with provider options merge
- [x] `mergePromptingConfig(global, template)` with platforms/modules/aliases merge

### Task 1.3 — Backward compatibility
- [x] `ai.image` preferred; fallback to legacy `articleImage`

---

## Epic 2 — Module & Publish Alignment (P0)
**Outcome:** publish can match module key and use module prompt automatically.

### Task 2.1 — Module inference
- [x] Match by `outputBaseDir + publishDir`
- [x] Prefer explicit `article.module`
- [x] Longest-prefix resolution

### Task 2.2 — Publish warnings
- [x] Warn when no module matched
- [x] Warn when lyra config not loaded

---

## Epic 3 — Unified Image System (P1)
**Outcome:** cover + inline images share unified config & prompt pipeline.

### Task 3.1 — Unified config
- [x] `global.ai.image` + template override
- [x] text/image/text+image/edit mode inference
- [x] textOverlay support (template + partial replace)

### Task 3.2 — Prompt composition
- [x] Compose: platform image system -> module -> user -> content
- [x] Apply to cover & inline

### Task 3.3 — Platform image system prompts
- [x] `imageSystemPromptFile`
- [x] `imageCoverSystemPromptFile`
- [x] `imageInlineSystemPromptFile`
- [x] Fallback order: cover/inline > imageSystem > built-in

---

## Epic 4 — Hooks (P2)
**Outcome:** light-weight hooks integrated across prompt, image, publish.

### Task 4.1 — Hook registry
- [x] Read & merge `global.hooks` / `templates.*.hooks` / `publish.hooks`

### Task 4.2 — Hook points
- [x] prompt.before / prompt.after
- [x] cover.generate / image.generate
- [x] publish.before / publish.after

---

## Epic 5 — Docs & Examples (P2)
**Outcome:** docs reflect consolidated spec and examples.

### Task 5.1 — Main spec
- [x] `requirements-spec.md`

### Task 5.2 — Example docs
- [x] `lyrarc-ai-publish-design.md`
- [x] `lyrarc-final-sample.md`
- [x] `merge-rules.md`
- [x] `publish-module-link-design.md`

### Task 5.3 — CLI / README
- [x] `CLI_GUIDE.md` updated
- [x] `README.md` updated
- [x] `README.zh-CN.md` updated

---

## Epic 6 — Release Validation (P3)
**Outcome:** verify build & publish dry-run.

- [x] `pnpm run build`
- [x] `pnpm test -- CLIInterface.prompt.test.ts`
- [x] publish dry-run with sample config

