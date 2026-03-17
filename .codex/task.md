# Todo

- [x] 读取 `/Users/codeshareman/Documents/ME/GitHub/ZNorth/.lyrarc.json` 并抽取字段清单（含路径语义）。
- [x] 读取 `/Users/codeshareman/Documents/ME/GitHub/Lyra/schema/lyra.schema.json` 并抽取字段清单。
- [x] 读取 `src/cli/CLIInterface.ts` 并抽取与配置相关的解析点清单。
- [x] 输出差异清单：schema vs .lyrarc.json vs CLI 解析（缺失/冗余/语义冲突）。
- [x] 设计 schema 修订方案（以 .lyrarc.json 为准），列出变更点。
- [x] 设计 CLI 适配方案（仅列变更，不改代码），覆盖：
  - `global.ai.image` cover/inline/prompt 结构解析
  - 模块 `coverImage` 合并优先级
  - 路径解析顺序
- [x] 设计 hooks 配置对齐方案（只允许已声明 hookName）。
- [x] 设计测试回归清单（build/test/dry-run）。
- [x] 运行 `pnpm run build` 并确保编译通过。
- [x] 运行 `pnpm test -- CLIInterface.prompt.test.ts` 并通过。
