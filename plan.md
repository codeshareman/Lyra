# Plan (Post-Config Refactor)

## 1) Align CLI with new config structure (modules at top-level)
- Update config parsing to prioritize `modules` (no `prompting` wrapper)
- Ensure `moduleDir` and `outputDir` use `global.moduleBaseDir` and `global.outputBaseDir`
- Remove reliance on `publishDir`

## 2) Cover generation per module
- Use `modules.<key>.coverImage` for cover rules
- Merge: `global.ai.image` -> `template.ai.image` -> `module.coverImage`

## 3) Publish parsing
- Expect `wechat_publish.json` to have `articles[]`
- Each article should use `module` key to resolve module config

## 4) Ensure inline images follow content prompt
- Keep inline images derived from content prompt

## 5) Update docs & todolist
- Reflect new top-level `modules`
- Mark completed tasks
