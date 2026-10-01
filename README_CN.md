![MiYago LOGO.png](image/MiYago%20LOGO.png)

# ReedsyWriting

<p>
中文 |
<a href="README.md">English</a>
</p>

## 介绍

[reedsy.com/creative-writing-prompts](https://reedsy.com/creative-writing-prompts)是知名独立出版与作家服务平台 Reedsy 旗下专门为写作者打造的创意写作灵感与短篇小说比赛社区，包含数千个短篇小说灵感和开头。

本数据集爬取了此网站的写作提示，并以之作为 prompt 调用 LLM 生成满足提示要求的多语种故事，从而为故事写作模型提供训练数据。

## 数据组成和结构

### 文件

本数据集包含一个 JSON Lines 数据文件。

| 文件 | 条目数 |
| --- | --- |
| `data/distilled_stories_standard.jsonl` | 2,740 |

共2,740 条样本。

### 字段结构

每行是一个独立的 JSON 对象（JSON Lines 格式）：

```json
{
  "conversations": [
    {
      "role": "system",
      "content": "你是一个作家。请基于我给你提供的提示、要求或想法创作一篇小说，语言：English。"
    },
    {
      "role": "user",
      "content": "A job interview has gone horribly wrong. You... Approximately 400 words."
    },
    {
      "role": "assistant",
      "content": "# The Last Question They Asked\n\nThe fluorescent lights in Conference Room B buzzed like angry hornets..."
    }
  ]
}
```

- **`conversations`**：三轮对话，依次为 system 指令、user 的英文 prompt、assistant 返回的故事全文。
- **`language`**：故事正文的语种标签，取值为 ISO 639-1 语言代码（如 `ja`、`ru`）。英文与中文故事无此字段。

assistant 的回复统一为 Markdown 文本，多数以 `# 标题` 开头。

### 数据样例

#### 英文故事

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家。请基于我给你提供的提示、要求或想法创作一篇小说，语言：English。"},
    {"role": "user", "content": "A job interview has gone horribly wrong. You... Approximately 400 words."},
    {"role": "assistant", "content": "# The Last Question They Asked\n\nThe fluorescent lights in Conference Room B buzzed like angry hornets, and I was starting to think I'd made a terrible mistake..."}
  ]
}
```

#### 非中英故事

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家。请基于我给你提供的提示、要求或想法创作一篇小说，语言：Indonesian。"},
    {"role": "user", "content": "Describe your ideal holiday. Around 400 words."},
    {"role": "assistant", "content": "# Liburan Impianku\n\nLiburan ideal menurut saya adalah perpaduan antara ketenangan, petualangan, dan kedekatan dengan alam..."}
  ],
  "language": "id"
}
```

### 语言分布

全部 2,740 条样本的输出语言构成：

| 语言 | 代码 | 条目数 |
| --- | --- | --- |
| 英语 | （无字段） | 1,001 |
| 中文 | （无字段） | 978 |
| 韩语 | `ko` | 55 |
| 阿拉伯语 | `ar` | 55 |
| 德语 | `de` | 55 |
| 越南语 | `vi` | 55 |
| 意大利语 | `it` | 55 |
| 法语 | `fr` | 55 |
| 泰语 | `th` | 55 |
| 西班牙语 | `es` | 55 |
| 印尼语 | `id` | 54 |
| 葡萄牙语 | `pt` | 54 |
| 日语 | `ja` | 53 |
| 波兰语 | `pl` | 53 |
| 俄语 | `ru` | 53 |
| 印地语 | `hi` | 53 |
| 旁遮普语 | （无字段） | 1 |

英文与中文故事不带 `language` 字段，故不计入 721 条已标注条目。旁遮普语那一条正文实际使用旁遮普语（Gurmukhi 字母），但因检测器判为中英内容而未打标，故上表单列。

## 构建

### 正文

爬取 Reedsy 网站的写作提示以作为 prompt 并附加了输出语言要求，然后调用 minimax-m3 生成故事正文。

### 输出语言

转换时为每条样本补出统一的 system prompt：

```
你是一个作家。请基于我给你提供的提示、要求或想法创作一篇小说，语言：<LANG>。
```

### 长度提示

2,734 条样本在 `user` prompt 末尾追加了长度提示，以提升模型对于输出长度的指令遵循能力。

## 使用方式

数据以 JSON Lines 格式发布，直接读取即可：

```python
import json

with open("data/distilled_stories_standard.jsonl", encoding="utf-8") as f:
    for line in f:
        if line.strip():
            sample = json.loads(line)
            system, user, assistant = sample["conversations"]
            # system["content"] 为写作指令与输出语言
            # user["content"] 为英文 prompt（含长度提示）
            # assistant["content"] 为故事全文（Markdown）
            # sample.get("language") 为故事正文的 ISO 639-1 语种标签（若有）
```

## 问题和局限

欢迎报告问题或提出建议。

## MiYago Datasets

[MiYago Datasets](https://huggingface.co/collections/Mikoris/miyago-datasets)是一个专为提升模型创意写作和角色扮演能力而生的数据集 Collection。

## 许可协议

本数据集采用 [Apache License 2.0](LICENSE) 许可。