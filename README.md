![MiYago LOGO.png](image/MiYago%20LOGO.png)

# ReedsyWriting

<p>
<a href="README_CN.md">中文</a> |
English
</p>

## Introduction

[reedsy.com/creative-writing-prompts](https://reedsy.com/creative-writing-prompts) is the creative-writing prompt and short-story contest community of Reedsy, a well-known independent publishing and writer services platform, containing thousands of short-story prompts and openings.

This dataset scrapes the writing prompts from that site and uses them as prompts to call an LLM to generate stories that satisfy the given prompts, providing training data for training story-writing models.

## Dataset Composition and Structure

### Files

The dataset contains one JSON Lines data file.

| File | Entries |
| --- | --- |
| `data/distilled_stories_standard.jsonl` | 2,740 |

2,740 samples in total.

### Field Structure

Each line is a standalone JSON object (JSON Lines format):

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

- **`conversations`** — the three dialogue turns, being the system instruction, the English prompt from the user, and the full story returned by the assistant.
- **`language`** — the language tag of the story body, given as an ISO 639-1 language code (e.g. `ja`, `ru`). English and Chinese stories have no such field.

The assistant reply is Markdown text, usually opening with a `# TITLE`.

### Data Samples

#### English story

```json
{
  "conversations": [
    {"role": "system", "content": "你是一个作家。请基于我给你提供的提示、要求或想法创作一篇小说，语言：English。"},
    {"role": "user", "content": "A job interview has gone horribly wrong. You... Approximately 400 words."},
    {"role": "assistant", "content": "# The Last Question They Asked\n\nThe fluorescent lights in Conference Room B buzzed like angry hornets, and I was starting to think I'd made a terrible mistake..."}
  ]
}
```

#### Non-Chinese/English story

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

### Language Distribution

The output language composition of all 2,740 samples:

| Language | Code | Entries |
| --- | --- | --- |
| English | (no field) | 1,001 |
| Chinese | (no field) | 978 |
| Korean | `ko` | 55 |
| Arabic | `ar` | 55 |
| German | `de` | 55 |
| Vietnamese | `vi` | 55 |
| Italian | `it` | 55 |
| French | `fr` | 55 |
| Thai | `th` | 55 |
| Spanish | `es` | 55 |
| Indonesian | `id` | 54 |
| Portuguese | `pt` | 54 |
| Japanese | `ja` | 53 |
| Polish | `pl` | 53 |
| Russian | `ru` | 53 |
| Hindi | `hi` | 53 |
| Punjabi | (no field) | 1 |

English and Chinese stories carry no `language` field, so they are not counted among the 721 tagged entries. The single Punjabi entry is written in Gurmukhi script but was left untagged because the detector classified it as Chinese/English content, so it is listed separately above.

## Construction

### Story Bodies

The writing prompts on the Reedsy site were scraped as prompts with the output-language requirement appended, and then minimax-m3 was called to generate the story bodies.

### Output Language

During conversion a uniform system prompt is filled in for every sample:

```
你是一个作家。请基于我给你提供的提示、要求或想法创作一篇小说，语言：<LANG>。
```

### Length Hints

2,734 samples have a length hint appended to the end of the `user` prompt, to improve the model's instruction-following on output length.

## Usage

The data is released in JSON Lines format and can be read directly:

```python
import json

with open("data/distilled_stories_standard.jsonl", encoding="utf-8") as f:
    for line in f:
        if line.strip():
            sample = json.loads(line)
            system, user, assistant = sample["conversations"]
            # system["content"] is the writing instruction and output language
            # user["content"] is the English prompt (with the length hint)
            # assistant["content"] is the full story (Markdown)
            # sample.get("language") is the ISO 639-1 story tag, if any
```

## Issues and Limitations

Please report any issues or suggestions.

## MiYago Datasets

[MiYago Datasets](https://huggingface.co/collections/Mikoris/miyago-datasets) is a dataset collection built specifically to improve the creative writing and roleplay capabilities of models.

## License

This dataset is licensed under the [Apache License 2.0](LICENSE).