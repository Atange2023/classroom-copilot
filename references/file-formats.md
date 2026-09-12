# Portable session file formats

All paths are canonical and relative to one session directory. At start, create only `_state.md`, `notes.md`, `concepts.md`, `questions.md`, and `visuals/`. Create `review.md` and `mindmap.md` only during `closing`; create optional study artifacts only when the learner selects them. Preserve original captures: corrections append a revision/correction rather than silently replacing the captured text. Follow `state-machine.md` for writer lease, `pending_entries`, retry, legacy migration, and resume order.

```text
Classroom-Copilot/<course-slug>/<YYYY-MM-DD-session-slug>/
├── _state.md
├── notes.md
├── concepts.md
├── questions.md
├── review.md                 # generated or updated at close only
├── mindmap.md                # generated or updated at close only
├── quiz.md                    # optional; selection only
├── flashcards.md              # optional; selection only
├── feynman-practice.md        # optional; selection only
└── visuals/
    └── V001-N001-flowchart.md # one uniquely identified selected visual
```

## `_state.md`

```text
session_id: <YYYY-MM-DD-course-slug-session-slug>
course: <课程名称>
state: <idle|in-class|visual-choice|question-refinement|closing|closed>
note_count: <integer>
question_count: <integer>
concept_count: <integer>
last_menu:
  1: <displayed action>
  2: <displayed action>
  3: <displayed action>
files:
  notes: notes.md
  questions: questions.md
  concepts: concepts.md
  review: review.md
  mindmap: mindmap.md
pending_entries:
  <event-or-fingerprint key>: <entry_id> | <destination path> | <input fingerprint> | <source reference>
writer_lease: <runtime lease/coordinator owner or none>
legacy_pending_entry_id: <empty or migrated pre-v2 pending_entry_id>
updated_at: <ISO 8601 time or unknown>
```

`active` is an incoming legacy value only: normalize it to `in-class` before persisting. Keep the `files` mapping exactly as shown. `last_menu` is the latest displayed menu only, and values must match displayed numbers.

## `notes.md`

```markdown
# Classroom notes

## N001

- entry_id: N001
- sequence: 1
- captured_at: <ISO 8601 time or unknown>
- input_type: <screenshot|photo|pasted-slide-text|quotation|learner-reflection|learner-idea|example|action>
- source: <course slide, attachment reference, learner, or unknown>
- source_confidence: <high|medium|low|unknown>
- source_reference: <attachment URL/path, slide title, or unknown>
- match_key: <event:<id>|fingerprint:<value>|legacy:<entry-id>>
- original_capture: <faithful text or attachment reference; preserve uncertainty>
- learner_annotation: <learner wording or none>
- derived_artifacts: [<relative artifact paths or none>]

### Interpretation

<optional concise AI summary; label inference and do not overwrite original_capture>

### Corrections

- <timestamp>: <append-only correction or none>
```

Every note entry must contain all of: `entry_id`, `sequence`, `captured_at`, `input_type`, `source`, `source_confidence`, `source_reference`, `match_key`, `original_capture`, `learner_annotation`, and `derived_artifacts`. A retry may clear its pending reservation only after these fields and their persisted input/source contract validate as complete.

## `concepts.md`

```markdown
# Concept index

## C001 — <term>

- entry_id: C001
- source_entries: [N001]
- status: <course-defined|external-researched|draft>

### 课程定义

<course-grounded definition or none recorded>
```

A course-defined concept stops here: it contains course definition and provenance only, with no `外部来源` heading, URL, or access date.

### External research append-only subtemplate

Append this subtemplate to the same concept entry **only** after the learner explicitly requests research:

```markdown
### 外部来源

- source_title: <title>
- url: <URL>
- accessed_at: <YYYY-MM-DD>
- definition: <external definition>
```

Only explicitly requested research creates this append-only `外部来源` section. It must retain URL and access date and must not overwrite `课程定义`.

## `questions.md`

```markdown
# Questions

## Q001

- entry_id: Q001
- sequence: 1
- asked_at: <ISO 8601 time or unknown>
- question: <learner's question>
- source_entries: [N001]
- evidence_boundary: <course material only|external research requested>
- status: <open|partially-resolved|resolved|needs-teacher>

### Course evidence

- <entry ID and concise evidence, or insufficient>

### Answer or teacher-ready wording

<concise grounded answer, labelled inference, or one teacher-ready question>
```

## `review.md`

For an empty close only (all three persisted counts are zero), use the same metadata fields with `empty_session: true`, `source_notes: []`, and explicit human-readable `no captured material` content in place of entry references. Do not include placeholders or invented entry IDs.

```markdown
# Lesson review — <course>

- session_id: <session id>
- generated_at: <ISO 8601 time or unknown>
- source_notes: [N001]

## Key ideas

- <course idea with entry ID>

## Learner reflections and applications

- <learner-authored reflection with entry ID>

## Questions

### Resolved

- <Q ID and answer>

### Unresolved

- <Q ID and teacher-ready next step>

## Uncertainty

- <unreadable or uncertain source, or none>

## Next actions

- <action>
```

## `mindmap.md`

For an empty close only (all three persisted counts are zero), use the same metadata fields with `empty_session: true`, `source_entries: []`, and explicit human-readable `no captured material` content in the editable outline. Do not include placeholders or invented entry IDs.

```markdown
# Mind map draft — <course>

- session_id: <session id>
- status: editable draft
- source_entries: [N001, Q001]

## Editable outline

- 课程内容：<central topic> [N001]
  - 课程内容：<supporting idea> [N002]
  - 学员想法：<reflection> [N003]
  - 未解决问题：<question> [Q001]
  - AI 建议连接：<connection requiring learner review>
  - 外部来源：<optional explicitly researched concept> [C001]

## Learner corrections

- <add, revise, or remove a draft link>
```

`mindmap.md` is required at close and must remain editable. It distinguishes course content, learner ideas, unresolved questions, external sources, and AI-suggested links.

## Optional study artifacts — create only after selection

### `quiz.md`

```markdown
# Quiz — <course>

- generated_at: <ISO 8601 time or unknown>
- source_entries: [N001]

## Q1

- prompt: <retrieval question>
- answer: <answer>
- evidence: [N001]
```

### `flashcards.md`

```markdown
# Flashcards — <course>

| Card ID | Front | Back | Evidence |
|---|---|---|---|
| F001 | <prompt> | <answer> | N001 |
```

### `feynman-practice.md`

```markdown
# Feynman practice — <course>

- topic: <selected concept>
- evidence: [C001, N001]

## Explain it simply

<learner draft>

## Gaps to revisit

- <gap or question>
```

## `visuals/*.md`

Use one unique, stable filename per visual: `visuals/V001-N001-flowchart.md`. The name is `<visual_id>-<primary-source-entry-id>-<type>.md`; never reuse a filename merely because the type and source entry match. Reserve/reuse `visual_id` through the state machine's single-writer and retry identity rules. Overwrite a visual only when the learner explicitly asks to update that same `visual_id`. Each file follows the portable visual shape in `visual-guide.md`: metadata (`visual_id`, `type`, `relationship`, `source_entries`, `created_at`, `status`), an editable Mermaid block when it fits, a text/table fallback, and a provenance table using `课程内容`, `学员想法`, `未解决问题`, `外部来源`, and `AI 建议连接`.
