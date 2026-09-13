# peng-story-image-skills

根据小说正文生成剧情化章节插图的 Codex Skill。

## 核心能力

- 直接读取小说正文，不依赖 `memory-agent`。
- 默认使用豆包 Seedream 4.5。
- 根据题材、时代、情绪和世界观建立全书视觉圣经。
- 默认每章选择一个关键情景，生成一张 16:9、2560×1440 插图。
- 强制画面包含故事环境、关键动作和主体互动，拒绝人物写真和角色海报。
- 通过标准参考图保持主要人物一致；主要人物首张参考图必须由用户确认。
- 自动审核剧情、场景、人物、画质与合规；首次失败后最多重试三次。
- 支持正文插入锚点、TOS 永久化、重新生成和历史版本保留。
- 允许有战斗张力，但禁止明显血腥和未来剧情剧透。

## 目录

```text
peng-story-image-skills/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── io-contract.md
    └── quality-and-prompting.md
```

## 使用

在 Codex 中调用：

```text
$peng-story-image-skills 请根据这本小说的前三章，每章生成一张剧情插图。
```

开发者接入时先阅读 `references/io-contract.md`；需要规划场景、编写提示词或执行自动审核时，再读取 `references/quality-and-prompting.md`。

## 运行边界

本 Skill 不包含 Seedream、TOS 或数据库密钥。没有可用的 Seedream 4.5 接口时，只能产出场景计划和提示词，并明确报告阻塞；不得未经用户同意改用其他图片模型。
