# Classroom Copilot session template

Copy only the start-time file contents below into a new session directory. Replace `<...>` placeholders while creating the session. Start creates `_state.md`, `notes.md`, `concepts.md`, `questions.md`, and the empty `visuals/` directory only. **MUST NOT write `review.md` or `mindmap.md` at session start.** The optional study artifacts are templates only: do **not** create `quiz.md`, `flashcards.md`, or `feynman-practice.md` until the learner selects that action. Generate or update the close-time templates only during `closing` after source entries are known.

## `_state.md` — required

```text
session_id: <YYYY-MM-DD-course-slug-session-slug>
course: <课程名称>
state: in-class
note_count: 0
question_count: 0
concept_count: 0
last_menu:
  1: 记录课件、截图或照片
  2: 记录想法或问题
  3: 选择一个关系做可视化
files:
  notes: notes.md
  questions: questions.md
  concepts: concepts.md
  review: review.md
  mindmap: mindmap.md
pending_entries: {}
writer_lease: none
legacy_pending_entry_id:
updated_at: <ISO 8601 time or unknown>
```

## `notes.md` — required

```markdown
# Classroom notes

<!-- Add one complete N### entry for each successful capture. -->
```

## `concepts.md` — required

```markdown
# Concept index

<!-- Add course definitions and explicitly requested 外部来源 entries here. -->
```

## `questions.md` — required

```markdown
# Questions

<!-- Add one complete Q### entry for each learner question. -->
```

## `review.md` — close-time template; MUST NOT write at session start

```markdown
# Lesson review — <课程名称>

- session_id: <session id>
- generated_at: <ISO 8601 time or unknown>
- source_notes: []

## Key ideas

- <fill at close>

## Learner reflections and applications

- <fill at close>

## Questions

### Resolved

- <fill at close>

### Unresolved

- <fill at close>

## Uncertainty

- <fill at close>

## Next actions

- <fill at close>
```

## `mindmap.md` — close-time template; MUST NOT write at session start

```markdown
# Mind map draft — <课程名称>

- session_id: <session id>
- status: editable draft
- source_entries: []

## Editable outline

- 课程内容：<central topic> [<entry id>]
  - 学员想法：<reflection or none>
  - 未解决问题：<question or none>
  - AI 建议连接：<connection for learner review>
  - 外部来源：<only if explicitly researched>

## Learner corrections

- <fill at close>
```

## `visuals/` — empty directory created at session start

```text
visuals/
  # individual visual Markdown files are created only after selection
```

Create the empty directory at session start. Use the `visuals/*.md` schema in `references/file-formats.md` only after a visualization is selected; then reserve a unique stable visual ID, include editable Mermaid when appropriate and a text/table fallback, and reuse that ID only for a retry or an explicit update of the same visual.

## `quiz.md` — optional; create only after Quiz is selected

```markdown
# Quiz — <课程名称>

- generated_at: <ISO 8601 time or unknown>
- source_entries: []

## Q1

- prompt: <retrieval question>
- answer: <answer>
- evidence: [<entry id>]
```

## `flashcards.md` — optional; create only after flashcards are selected

```markdown
# Flashcards — <课程名称>

| Card ID | Front | Back | Evidence |
|---|---|---|---|
| F001 | <prompt> | <answer> | <entry id> |
```

## `feynman-practice.md` — optional; create only after Feynman practice is selected

```markdown
# Feynman practice — <课程名称>

- topic: <selected concept>
- evidence: [<entry id>]

## Explain it simply

<learner draft>

## Gaps to revisit

- <gap or question>
```
