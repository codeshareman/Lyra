# Config Alignment Report

## Source Inputs

- Config: `/Users/codeshareman/Documents/ME/GitHub/ZNorth/.lyrarc.json`
- Schema: `/Users/codeshareman/Documents/ME/GitHub/Lyra/schema/lyra.schema.json`
- Code: `src/cli/CLIInterface.ts`

## A. .lyrarc.json 字段清单（含路径语义）

### global
- `logLevel`
- `defaultTemplate`
- `outputBaseDir`（相对 configDir）
- `moduleBaseDir`（相对 configDir）
- `moduleDraftsDirName`
- `hooks`（map）
- `ai`
  - `enabled`
  - `provider`
  - `model`
  - `baseUrl`
  - `apiKey`
  - `timeout`
  - `maxRetries`
  - `maxTokens`
  - `temperature`
  - `options`
  - `image`
    - `enabled`
    - `ratio`
    - `prompt.usePlatformSystem`
    - `cover.enabled`
    - `cover.insertIntoArticle`
    - `cover.ratio`
    - `cover.promptBase`
    - `cover.sourceOrder`
    - `cover.unsplashAccessKeyEnv`
    - `cover.unsplashQuery`
    - `inline.enabled`
    - `inline.maxBodyImages`
    - `inline.maxPerModule`

### templates
- `weekly`
  - `enabled`
  - `templateVersion`
  - `branding.title`
  - `template.path`
  - `visual.*`
  - `sources.*`
  - `output.path`（相对 configDir）
  - `output.filename`
  - `content.*`
  - `modules.*`（weekly 子模块）
  - `export.*`
  - `ai.summaries.*`
  - `ai.goldenQuote.*`
- `article`
  - `enabled`
  - `template.path`
  - `sources.notes`
  - `output.path`
  - `output.filename`
  - `content.*`

### modules
- `weekly`
  - `label`
  - `promptFile`（相对 moduleDir）
  - `platforms.wechat.promptFile`（相对 moduleDir）
  - `platforms.zhihu.promptFile`（相对 moduleDir）
  - `template`
  - `coverImage.input.image`（相对 outputBaseDir）
  - `coverImage.input.editText`
  - `coverImage.promptFile`（相对 moduleDir）
  - `moduleDir`（相对 moduleBaseDir）
  - `outputDir`（相对 moduleBaseDir）
- `life|audiovisual|tech|movie|food|sports|series`
  - 同上（无 coverImage.input）

## B. Schema 字段清单（摘要）

Schema 已覆盖：`global`、`templates.weekly/article`、`modules.{weekly,life,audiovisual,tech,movie,food,sports,series}`。

关键结构：
- `global.ai.image.cover/inline/prompt`
- `modules.*.coverImage`
- `modules.*.platforms.{wechat,zhihu}.promptFile`
- `templates` 强制 only weekly/article
- `modules` 强制 only 当前 8 个 key
- `hooks` 明确 hookName（prompt.before/after, image.generate, cover.generate, publish.before/after）

## C. CLI 解析点清单（摘要）

核心入口：
- `resolvePromptRuntimeConfig`：合并 global/template/prompting；解析 `modules`, `moduleBaseDir`, `outputBaseDir`, `platforms`, `hooks`
- `resolveArticleImageConfig`：合并 `global.ai.image` 与 `template.ai.image`
  - 支持 `cover`/`inline`/`prompt` 层级
  - 支持 `coverPromptBase`、`coverSourceOrder`
- `resolvePromptModules`：解析 `moduleDir`, `promptFile`, `platforms`
- `resolveCoverPrompt`：优先模块 coverPromptFile，然后模块目录内 prompt 文件
- `normalizeImageInput`：路径解析（absolute / ./configDir / outputBaseDir）
- `resolvePublishImageConfig`：用于 publish 时的 image 合并
- hooks：`prompt.before/after`、`image.generate`、`cover.generate`、`publish.before/after`

## D. 差异清单（Schema vs .lyrarc.json vs CLI）

### 1) Schema 与 .lyrarc.json
- ✅ 覆盖一致
- ⚠ `templates` / `modules` 在 schema 中 `additionalProperties: false`（后续新增需要同步 schema）

### 2) Schema 与 CLI
- ✅ hooks 名称一致
- ✅ `global.ai.image.cover.*` 已支持
- ✅ `modules.*.platforms.*.promptFile` 已支持
- ⚠ CLI 仍容忍旧字段（如 `insertCoverImage`, `coverSourceOrder`, `coverAiEndpoint`）——可保留兼容

### 3) .lyrarc.json 与 CLI
- ✅ 路径语义：moduleDir / promptFile / cover.prompt.md
- ✅ cover prompt base 与平台系统 prompt 组合
- ✅ cover 输入图像路径（相对 outputBaseDir）

## E. Schema 修订建议（以 .lyrarc.json 为准）

1. `modules` / `templates` 继续保持 `additionalProperties: false`
2. `platforms` 子结构固定 `wechat/zhihu`
3. 后续新增模块时需同步 schema `modules.properties`

## F. CLI 适配建议（不改代码版清单）

1. 解析 `global.ai.image.cover.promptBase` 作为封面基础 prompt（已实现）
2. 解析 `modules.*.coverImage.input.image` 相对 `outputBaseDir`（已实现）
3. hooks 名称仅允许 schema 中定义（已实现，通过 schema 与逻辑一致）
4. 路径优先级：absolute > ./configDir > moduleDir/outputBaseDir（已实现）

## G. 测试回归清单

- `pnpm run build`
- `pnpm test -- CLIInterface.prompt.test.ts`
- `node dist/cli.js publish --dry-run --file /Users/codeshareman/Documents/ME/GitHub/ZNorth/wechat_publish.json`
