---
name: flashcard-generator
description: Turn notes, docs, or a topic into high-quality spaced-repetition (SRS) flashcards. Use when the user wants to study, memorize, prep for an exam/certification (e.g. AWS, CKA, Terraform), or convert reading material into Anki/Obsidian cards. Produces single-sided, bidirectional, and cloze-deletion cards grounded in cognitive science.
---

# Flashcard Generator

Transform source material (notes, documents, URLs, or a raw topic) into durable
spaced-repetition flashcards optimized for long-term retention.

## When to use
- "이거 카드로 만들어줘", "암기카드 / 플래시카드 만들어줘"
- Certification / exam prep (AWS AIF-C01, MLA-C01, CKA, Terraform Associate, etc.)
- Converting an article, PDF, or study note into an Anki / Obsidian deck

## Core principles (apply to every card)
1. **Atomicity** — one fact per card. Split compound facts into multiple cards.
2. **Bidirectional testing** — when a fact is symmetric (term ↔ definition), make
   both directions so recall works either way.
3. **Active recall** — the front must force retrieval, not recognition. Avoid
   yes/no cards.
4. **Minimum information** — keep answers short; long answers = split the card.
5. **Grounding** — only create cards from the provided source. Never invent facts.
   When a source is given, add the source reference to each card.

## Card types to produce
Generate a mix appropriate to the material:

### 1. Single-sided (Q → A)
```
Q: What scoring scale does the AWS MLA-C01 exam use?
A: 100–1000, passing score 720
```

### 2. Bidirectional (term ⇄ definition)
```
Front: SageMaker Model Monitor
Back: Detects data/quality/bias drift on deployed endpoints over time
---
Front: Detects data/quality/bias drift on deployed endpoints
Back: SageMaker Model Monitor
```

### 3. Cloze deletion
```
The CKA exam is a {{c1::performance-based}} test giving you a {{c2::live cluster}}
and {{c3::2 hours}} to solve real-world problems.
```

## Output format
Default to an **Anki-importable TSV** (tab-separated) block plus a human-readable
preview. Use this structure unless the user asks for Obsidian/Markdown:

```
# Deck: <topic>
# Format: front<TAB>back<TAB>tags

<front>\t<back>\t<tag1 tag2>
```

For cloze cards, emit a separate section using Anki's `{{c1::...}}` syntax and tag
them `cloze`.

If the user uses Obsidian/SRS plugins, emit Markdown with `?` separators instead:
```
Question #flashcard
Answer
```

## Workflow
1. Read the source (file path, pasted text, or URL the user provides).
2. Extract the key testable facts; group by sub-topic.
3. Draft cards applying the principles above; prefer 10–25 cards per pass unless
   told otherwise.
4. Show a short preview, then write the full deck to a file
   (e.g. `study/<topic>.tsv`) when the user wants to import it.
5. Offer to expand weak areas or generate a quiz from the same source.
