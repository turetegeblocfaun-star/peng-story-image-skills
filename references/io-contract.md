# 小说章节插图接入契约

仅在需要 API、结构化输入输出或其他 Agent 接入时读取。

## 输入

```json
{
  "requestId": "chapter-18-v1",
  "storyId": "novel-001",
  "chapterNo": 18,
  "chapterVersion": 1,
  "chapterTitle": "夜袭",
  "regenerationReason": "可选；重新生成时填写",
  "narrative": {
    "segments": [
      {
        "segmentId": "p-1801",
        "kind": "narration",
        "order": 1,
        "text": "夜色压在营门上……"
      },
      {
        "segmentId": "p-1802",
        "kind": "dialogue",
        "speakerName": "张休",
        "order": 2,
        "text": "张休低声下令……"
      },
      {
        "segmentId": "p-1803",
        "kind": "event",
        "order": 3,
        "text": "众人冲向营门……"
      }
    ]
  }
}
```

约束：

- `requestId` 在同一故事内唯一并用于幂等；重新生成使用新 ID。
- `segmentId` 和 `order` 在本章内唯一。
- `kind` 仅允许 `dialogue | narration | event`。
- 只有原文明确说话者时才提供 `speakerName`，不得猜测。
- 同一 `requestId` 达到成功或最终失败后，只能重放原结果。

## 成功输出

```json
{
  "status": "published",
  "attempts": 1,
  "illustration": {
    "version": 2,
    "isCurrent": true,
    "model": "doubao-seedream-4.5",
    "scenePlan": {
      "theme": "营门夜袭",
      "environment": "夜色笼罩的古代军营，营门与火把清晰可见",
      "keyAction": "主角带人冲向营门并迎敌",
      "subjectInteraction": "守军与来敌隔着营门形成对峙",
      "composition": "16:9 中远景，前中后景呈现攻守关系",
      "insertAfterSegmentId": "p-1803",
      "anchorTextHash": "sha256...",
      "anchorQuote": "众人冲向营门……",
      "evidenceSegmentIds": ["p-1802", "p-1803"],
      "characterIds": ["char-001"]
    },
    "asset": {
      "permanentUri": "tos://bucket/stories/novel-001/chapters/18/illustrations/id/v2.png",
      "objectKey": "stories/novel-001/chapters/18/illustrations/id/v2.png",
      "width": 2560,
      "height": 1440
    },
    "audit": {
      "passed": true,
      "safetyPassed": true,
      "scores": {
        "narrativeMatch": 0.94,
        "sceneStorytelling": 0.95,
        "characterConsistency": 0.95,
        "imageQuality": 0.92
      },
      "reasons": []
    }
  }
}
```

## 状态

| 状态 | 含义 | 处理 |
|---|---|---|
| `waiting_visual_bible_lock` | 全书画风尚未锁定 | 完成视觉圣经确认后继续 |
| `waiting_reference_approval` | 主要人物参考图未确认 | 返回人物 ID 和候选图，等待用户 |
| `published` | 图片已永久化并通过审核 | 可插入正文并设为当前版本 |
| `generation_failed` | 最多四次生成或存储均失败 | 返回尝试次数和失败原因 |
| `review_rejected` | 最多四张图均未通过审核 | 不展示图片，返回审核原因 |

等待审批不是幂等终态；审批完成后可用原 `requestId` 继续。成功或最终失败是幂等终态。

## 稳定服务边界

实现时保持以下端口可替换：

- `NovelChapterAnalyzer`
- `NovelVisualBiblePlanner`
- `ImageGenerator`
- `IllustrationReviewer`
- `AssetStorage`
- `IllustrationRepository`
- `ChapterIllustrationAgentPort`

模型、数据库和对象存储的替换不应改变章节生图主流程。
