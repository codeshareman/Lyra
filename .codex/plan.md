# Plan (Pre-Change)

目标：在不立刻修改代码的前提下，先完成配置一致性检查与改造计划，保证后续改动可控、可回滚。

1. 现状检查（Config/Schema/Code 对齐）
   - 读取最终版 `/Users/codeshareman/Documents/ME/GitHub/ZNorth/.lyrarc.json`
   - 逐项对照 `/Users/codeshareman/Documents/ME/GitHub/Lyra/schema/lyra.schema.json`
   - 交叉核对 `src/cli/CLIInterface.ts` 的解析/默认值/回退逻辑
   - 输出一份“对齐差异清单”（字段缺失、字段冗余、语义不一致）

2. Schema 规划（以 .lyrarc.json 为准）
   - 固定模板：`weekly` / `article`（`additionalProperties: false`）
   - 固定模块：当前 8 个模块（`additionalProperties: false`）
   - 补齐所有字段 `description`
   - 明确路径语义（相对 `configDir` / `moduleDir` / `outputBaseDir`）

3. CLI 适配计划（不改代码，仅列变更）
   - 解析 `global.ai.image` 新结构（cover/inline/prompt）
   - `modules.<key>.coverImage` 优先级与 `global.ai.image.cover` 合并规则
   - 路径解析顺序统一（configDir → moduleDir → outputBaseDir）
   - hooks 仅允许定义的 hookName（与 schema 一致）

4. 测试与验证计划
   - `pnpm run build`
   - `pnpm test -- CLIInterface.prompt.test.ts`
   - dry-run：`node dist/cli.js publish --dry-run --file /Users/codeshareman/Documents/ME/GitHub/ZNorth/wechat_publish.json`

5. 变更执行顺序（后续阶段）
   - 先更新 schema
   - 再更新 CLI 解析逻辑
   - 最后验证与回归
