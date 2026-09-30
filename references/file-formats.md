# Portable session file formats

Session paths are canonical and relative to one session directory; the two learning files live in its course parent. At start create only `_state.md`, `notes.md`, `concepts.md`, `questions.md`, and `visuals/`. Registering the first input creates `sources.md`; selection of practice creates `practice.md`. Setting a goal or starting training creates course learning files. Create `review.md` and `mindmap.md` only during `closing`; other study artifacts only after selection. Preserve original captures and answers with append-only corrections. Follow `state-machine.md` for coordination, pending/retry and migration. Reading never creates optional files solely to fill gaps.

```text
Classroom-Copilot/<course-slug>/
├── learning-profile.md        # goal or training selected
├── learning-ledger.md         # goal or training selected
└── <YYYY-MM-DD-session-slug>/
├── _state.md
├── notes.md
├── concepts.md
├── questions.md
├── sources.md                # first source registered
├── practice.md               # selected interactive training
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
schema_version: 2
session_id: <YYYY-MM-DD-course-slug-session-slug>
course: <课程名称>
state: <idle|in-class|visual-choice|question-refinement|closing|closed>
learning_mode: <classroom|review|feynman>
source_count: <integer>
active_goal_id: <G### or none>
active_practice_id: <P### or none>
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
  sources: sources.md
  practice: practice.md
pending_entries:
  <event-or-fingerprint key>: <entry_id> | <destination path> | <input fingerprint> | <source reference>
writer_lease: <runtime lease/coordinator owner or none>
legacy_pending_entry_id: <empty or migrated old pending_entry_id>
updated_at: <ISO 8601 time or unknown>
```

`active` is an incoming legacy value only: normalize to `in-class`. The schema 1 mapping has the original five files; schema 2 adds sources/practice as shown. `last_menu` must match the latest displayed menu. Compound pending operations include fixed target paths, reserved IDs and original payload for every target, per state-machine.md.

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
- source_id: <S### or unknown for unmigrated captures>
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

For new schema 2 captures add required source_id pointing to a valid S record with a reciprocal N link. Only unmigrated/legacy notes may have unknown. A reference-only note must declare unreadability explicitly in original_capture; it is a record of a reference, not evidence of course claims.

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
- coverage: <我的已捕获重点|已读取课件范围|无可读课程材料>
- source_scope: <actual readable sections/pages and S IDs, or unknown>

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
- coverage: <actual material scope>
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

For an interactive Quiz present prompts first and do not print this answer field until requested or the learner responds; actual attempts go in practice.md. A requested static quiz is a reference artifact, not evidence of answering it. Keep its answer key separate from the question section. Source scope limits question coverage.

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

For interactive Feynman practice use practice.md as the canonical prompt/attempt record. This optional feynman-practice.md is a selected readable teaching transcript/summary with session_id and practice_id; rounds carry original learner explanation, student question, response and gap evidence. It cannot replace unanswered-turn persistence.

## `visuals/*.md`

Use one unique, stable filename per visual: `visuals/V001-N001-flowchart.md`. The name is `<visual_id>-<primary-source-entry-id>-<type>.md`; never reuse a filename merely because the type and source entry match. Reserve/reuse `visual_id` through the state machine's single-writer and retry identity rules. Overwrite a visual only when the learner explicitly asks to update that same `visual_id`. Each file follows the portable visual shape in `visual-guide.md`: metadata (`visual_id`, `type`, `relationship`, `source_entries`, `created_at`, `status`), an editable Mermaid block when it fits, a text/table fallback, and a provenance table using `课程内容`, `学员想法`, `未解决问题`, `外部来源`, and `AI 建议连接`.

## Sources — `sources.md`

Each S### entry requires source_id, session_id, match_key, kind (`完整课件|摘录|个人输入|外部来源|AI补充`), source_reference, acquired_scope (exact sections/pages/visible text or unknown), confidence (`high|medium|low|unknown`), permission_status (`authorized|ordinary-course-material|redacted|uncertain`), captured_at, source_entries and readability (`read|partial|unreadable`). Only read content may be course evidence. For external material also store title, URL and access date; permission uncertainty follows the capture boundary before persistence of sensitive text/pixels. Do not persist sensitive data merely to fill the registry. Old notes retain original provenance and need not receive invented S IDs.

ordinary-course-material means ordinary slides supplied for authorized local study (learner-confirmed authorization or clearly self-authored/permitted test material), not blanket permission for copyrighted content. If retention authority is unknown, ask once for the relevant course material before saving raw content; uncertain is not an authorization. Sharing rights are separate from local retention.

## Course learning files

`learning-profile.md` requires course, learner_scope (learner-confirmed identity or single-user selected directory; never guess), goals and confirmed_preferences/confirmed_connections. Each G### requires goal_id, goal (learner's words), application, acceptance_criteria, status (`draft|confirmed|completed`), updated_at. Missing application/criteria use unknown and ask one focused question without blocking capture. Completed only with relevant observed evidence and learner confirmation. A proposed knowledge link remains `AI建议` until accepted; edit/forget requests target the learner-specified preference or link, not unrelated records. Evidence-removal requests must disclose affected learning judgments and recompute them.

`learning-ledger.md` has an append-only Evidence section and rebuildable Current learning status, Review plans sections. Each E### requires evidence_id, match_key, session_id, practice_id, attempt_id, topic, source_refs (session-qualified), original_response_ref, assistance (`none|hint|answer-shown|AI-authored|unknown`), task_type (`recall|application|feynman|retest`), result (`pass|partial|incorrect|unverifiable`), reason (criteria plus response evidence), observed_at. The referenced response must be preserved in practice.md; never a non-existent ID.

Current status rows require topic, learning_status, evidence_refs, updated_at and uncertainty. Allowed learning_status values: `已接触|待验证|需澄清|独立解释通过|应用通过`. Do not reuse concept/question statuses. Earlier success remains in Evidence if current mistakes require clarification. Review plans require plan_id, topic, due_at/timezone (or unknown), learner_confirmed, status (`planned|due|completed|cancelled`), linked_practice; completion needs an actual attempt, not a generated reminder. Retention is a dated retest observation, not a permanent status.

## Interactive practice — `practice.md`

Each P### entry requires practice_id, session_id, goal_id (or none), topic, mode (`review|feynman`), task_type, source_refs, prompt, criteria (saved before prompt is sent), student_role (Feynman, otherwise none), status (`未答|已答|中断`), created_at and rounds. Source refs store both session_id and entry_id; files outside this session cannot be cited by bare N001. Empty evidence or insufficient criteria means ask for material or narrow the exercise rather than grade a guessed answer.

Each round requires round_id (R### within P), prompt, criteria, status, attempts, and optional next_prompt. Each attempt requires attempt_id (A### within P), match_key, original_response (verbatim), assistance, assessed_result, reasoning, observed_at and evidence_id (E###). No answer uses `attempts: []`; unanswered is not incorrect. A new answer appends a new attempt; no silent replacement. Assistance unknown does not qualify as independent. Showing criteria/answer or providing a hint records assistance before sending it. Corrections preserve previous versions.

Before interruption persist the current unanswered round including pending student question; active_practice_id identifies the P on resume. Feynman summary links P/R/A/E rather than inventing a second scoring system. Temporary records carry the same goals, scope, unresolved prompt and observed attempts, but explicitly lack durable persistence.

## Schema 2 composite `pending_entries` record

Single-target legacy pipe records are retained until reconciled; all new multi-file operations use this YAML-shaped map (replace sample values with real payload, never placeholders). `phase` is reserved/writing/validating; target status is pending/written/validated and must be checked against actual contents, not trusted blindly.

```yaml
pending_entries:
  "event:answer-request-17":
    operation: practice-answer
    session_id: 2026-09-30-skill-session-a
    fingerprint: normalized-response-and-practice-identity
    original_payload:
      practice_id: P001
      attempt_id: A001
      response: 交互层负责反馈，状态层保存阶段和菜单。
      assistance: answer-shown
    reserved_ids: {practice: P001, round: R001, attempt: A001, evidence: E001}
    phase: writing
    targets:
      - {path: practice.md, entry_id: P001/R001/A001, status: written, expected_payload: "original response, assistance, criteria result and E001 reference"}
      - {path: ../learning-ledger.md, entry_id: E001, status: pending, expected_payload: "session_id, P001, A001, response ref, assistance, result and reason"}
      - {path: _state.md, entry_id: session-state, status: pending, expected_payload: "active_practice_id=none after answered review; pending cleared only after all targets validate"}
```

The example uses short illustrative expected_payload descriptions; live reservations must contain the full exact fields/text to validate (not these descriptions or a digest alone). Record criteria/result/observed_at before first write so retries reuse them. For capture use operation source-capture and S/N reserved IDs; goal setup uses goal-update/G; sources/practice/profile/ledger/state are the only composite target kinds. Derive the two ../ learning paths from the selected course directory and check containment; all other ../ or absolute material-supplied paths are invalid. Expected state fields exclude transient lease/pending metadata; reconcile those separately after content validation. On any failure retain original payload, IDs and all target statuses; repair under coordinator with same IDs, then validate all targets and clear pending.
