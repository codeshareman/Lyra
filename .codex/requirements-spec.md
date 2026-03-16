# Lyra 需求功能文档（完整版）

> 本文档为 `.codex` 目录下多份设计文档的**统一归并版**，以“需求/规则/行为/配置/兼容/优先级”为主线，确保不丢失信息。

---

## 1. 目标
- 可扩展、可维护的配置结构
- 多平台、多内容发布
- 模块化提示词与发布/生成强关联
- AI 图像生成统一入口（封面 + 插图）
- 系统级 prompt 与用户级 prompt 分层
- 兼容旧配置，平滑迁移

---

## 2. 核心原则
1. **模块唯一标识使用 module key**
2. **outputBaseDir + publishDir** 用于匹配模块
3. **global 默认 + template 覆盖** 的合并策略
4. **系统级 prompt 与用户级 prompt 分离**
5. **发布配置只包含发布字段**（AI 配置跟随 global/template）

---

## 3. 配置结构（全局默认 + 模板覆盖）

### 3.1 全局默认
```json
{
  "global": {
    "logLevel": "info",
    "defaultTemplate": "weekly",
    "ai": {
      "enabled": true,
      "provider": "local",
      "model": "minimax-m2.5:cloud",
      "baseUrl": "http://localhost:11434",
      "apiKey": "",
      "timeout": 180000,
      "maxRetries": 1,
      "maxTokens": 1600,
      "temperature": 0.7,
      "options": {},
      "image": {
        "enabled": true,
        "script": "./scripts/generate-cover-image.js",
        "ratio": "16:9",
        "prompt": {
          "base": "极简/留白/纸质质感",
          "useModulePrompt": true,
          "usePlatformPrompt": true,
          "usePlatformImageSystem": true
        },
        "cover": { "enabled": true, "insertIntoArticle": true },
        "inline": { "enabled": true, "maxBodyImages": 5, "maxPerModule": 2 }
      }
    },
    "prompting": {
      "defaultPlatform": "wechat",
      "defaultModule": "life",
      "outputBaseDir": "./Output/Z° North",
      "modulesBaseDir": "./Output/Z° North",
      "outputDraftsDirName": "drafts",
      "platforms": {
        "wechat": {
          "systemPromptFile": "./prompts/platform.wechat.system.md",
          "imageSystemPromptFile": "./prompts/platform.wechat.image.md",
          "imageCoverSystemPromptFile": "./prompts/platform.wechat.image.cover.md",
          "imageInlineSystemPromptFile": "./prompts/platform.wechat.image.inline.md"
        },
        "zhihu": {
          "systemPromptFile": "./prompts/platform.zhihu.system.md",
          "imageSystemPromptFile": "./prompts/platform.zhihu.image.md",
          "imageCoverSystemPromptFile": "./prompts/platform.zhihu.image.cover.md",
          "imageInlineSystemPromptFile": "./prompts/platform.zhihu.image.inline.md"
        }
      }
    },
    "hooks": {
      "prompt.before": "./hooks/prompt-before.js",
      "prompt.after": "./hooks/prompt-after.js",
      "cover.generate": "./hooks/cover-generate.js",
      "image.generate": "./hooks/image-generate.js",
      "publish.before": "./hooks/publish-before.js",
      "publish.after": "./hooks/publish-after.js"
    }
  }
}
```

### 3.2 模板级覆盖
```json
{
  "templates": {
    "weekly": {
      "ai": { "summaries": { "enabled": true }, "goldenQuote": { "enabled": true } }
    },
    "article": {
      "ai": {
        "maxTokens": 1600,
        "prompting": {
          "modules": { /* module 定义 */ },
          "aliases": { /* alias 映射 */ }
        }
      }
    }
  }
}
```

---

## 4. 模块与发布关联

### 4.1 模块 key 规则
- module key 为唯一标识
- `weekly` 必须显式作为模块 key

### 4.2 自动匹配
- 如果 `article.module` 指定则优先
- 否则根据 `contentFile` 路径匹配：
  - `outputBaseDir + publishDir` 前缀匹配
  - 多命中取最长前缀

---

## 5. 发布配置（多平台 + 多内容）

### 5.1 wechat.publish.json 示例
```json
{
  "lyraConfig": "./.lyrarc.json",
  "title": "默认标题",
  "author": "Lyra",
  "digest": "默认摘要",
  "thumb_image_path": "./Output/Z° North/Publish/default-cover.png",
  "articles": [
    {
      "title": "Weekly #12",
      "module": "weekly",
      "contentFile": "./Output/Z° North/Z°N Weekly/drafts/2026-03-16-weekly.html"
    },
    {
      "title": "声图志 · 春天的街头声音",
      "contentFile": "./Output/Z° North/Z°N 声图志/drafts/2026-03-16-sound.html"
    },
    {
      "title": "生活志 · 通勤观察",
      "contentFile": "./Output/Z° North/Z°N 生活志/drafts/2026-03-16-life.html"
    }
  ]
}
```

### 5.2 .lyrarc.json publish 默认入口（可选）
```json
"publish": {
  "wechat": {
    "mode": "draft",
    "contentFile": "./Output/Z° North/Z°N 生活志/drafts/示例文章.wechat.html",
    "configFile": "./wechat.publish.json"
  },
  "zhihu": {
    "mode": "draft",
    "contentFile": "./Output/Z° North/Z°N 生活志/drafts/示例文章.zhihu.html",
    "configFile": "./zhihu.publish.json"
  }
}
```

---

## 6. 图像生成统一（封面 + 插图）

### 6.1 自动模式推断
1. `mask` / `editText` → edit
2. `image + prompt` → text+image
3. `image` → image
4. none → text

### 6.2 文本局部替换（textOverlay）
- 支持模板化文本与局部 replace

```json
"textOverlay": {
  "textTemplate": "Weekly #{{issueNumber}}",
  "font": "Source Han Sans",
  "size": 64,
  "x": 80,
  "y": 160,
  "color": "#ffffff"
}
```

---

## 7. 系统级 vs 用户级 Prompt
- **系统级**：平台默认风格/约束（CLI 内置，或配置覆盖）
- **用户级**：模块 prompt、cover.prompt.*、prompt.*

组合顺序：
1. platform image system prompt
2. module prompt
3. user prompt
4. content

---

## 8. Hooks（轻量方案）
- Hook 注册表：global/template/publish
- Hook 点：
  - prompt.before / prompt.after
  - cover.generate / image.generate
  - publish.before / publish.after

---

## 9. 兼容性
- `ai.image` 优先
- 旧 `articleImage` 仍兼容
- 旧 `wechat_publish.json` 中 `cover_*` 字段可继续使用，但推荐迁移到 `global.ai.image`

---

## 10. 合并优先级
1. global 默认
2. template 覆盖
3. module/item 级字段最终覆盖

---

## 11. 最小模板清单
- 必填：
  - enabled
  - template.path
  - sources
  - output.path
  - output.filename
  - content
- 可选：
  - ai.*
  - ai.prompting.*
  - ai.image.*
