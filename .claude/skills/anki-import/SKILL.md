---
name: anki-import
description: Fetch a web article, doc, or official exam guide and turn it into a sourced, Anki-importable deck. Use when the user gives a URL/document and wants ready-to-import flashcards with citations for spaced repetition study.
---

# Anki Import

Turn an online or local source into an **Anki-ready deck** with proper sourcing, so
facts can be traced back to where they came from.

## When to use
- "이 링크/문서로 Anki 카드 만들어줘", "출처 포함해서 덱 만들어줘"
- Building a deck from an official exam guide PDF or documentation page

## Workflow
1. **Fetch** the source (WebFetch for a URL, Read for a local file). If a URL is
   blocked, ask the user to paste the text.
2. **Extract** key testable facts. Skip fluff, marketing, and navigation text.
3. **Generate cards** following the `flashcard-generator` principles (atomic,
   active-recall, bidirectional where symmetric). Aim for 10+ cards.
4. **Add a source field** to every card: the page title + URL (or file + section).
5. **Write the deck** to `study/<slug>.tsv` in this format:

```
# Deck: <title>
# Source: <url-or-file>
# Columns: front<TAB>back<TAB>source<TAB>tags

<front>\t<back>\t<source>\t<tags>
```

6. Tell the user how to import: Anki → File → Import → select the `.tsv`, set field
   separator to Tab, map columns (Front, Back, plus extra fields for source/tags).

## Notes
- Never fabricate facts not present in the source. If the source is thin, say so.
- For multi-page sources, fetch sections and dedupe overlapping facts.
- Offer a cloze variant of dense definitional content.
