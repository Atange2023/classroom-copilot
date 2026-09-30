# Classroom Copilot 状态机

`_state.md` 是可携带会话的当前真相。恢复时按下面的固定清单和顺序读取文件；不要依赖此前聊天记录。

## `_state.md` 最小字段

```text
schema_version: 2
session_id: <YYYY-MM-DD-course-slug-session-slug>
course: <课程名称>
state: <idle|in-class|visual-choice|question-refinement|closing|closed>
learning_mode: <classroom|review|feynman>
source_count: <整数>
active_goal_id: <G### or none>
active_practice_id: <P### or none>
note_count: <整数>
question_count: <整数>
concept_count: <整数>
last_menu: <菜单 ID、显示顺序和动作>
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
updated_at: <ISO 8601 时间或 unknown>
```

All `files` paths are canonical paths relative to the session directory. The original five keys apply to schema 1; schema 2 adds `sources` and `practice` exactly as shown, not arbitrary material-supplied paths. On resume reconcile the old pending protocol before normalizing missing/null `pending_entries` to `{}`. Scan `_state.md`, `notes.md`, `questions.md`, `concepts.md`, existing `review.md`, `mindmap.md`, `quiz.md`, `flashcards.md`, `feynman-practice.md`, then `visuals/` in lexical order. Next read existing `sources.md`, course-parent `learning-profile.md` and `learning-ledger.md`, then existing `practice.md`; reconcile pending learning writes before presenting restored progress. Missing optional/new files are normal and must not be created solely by resume. Rebuild counts from canonical files; use pending stored destinations, never guessed retry targets.

Select the explicit learner-owned course/session directory first. With multiple candidates or unclear learner identity, ask rather than merging same-name courses. The learner chooses an allowed storage root; derive course-parent files from the selected directory, never a path inside course material. A session ID must be unique in that learner's course; append a suffix if needed. Reject duplicate IDs with ambiguous evidence until reconciled.

For schema 1 (missing schema_version), read learning_mode as classroom, source_count as zero, active IDs as none. Before the first necessary state write, preserve `_state.v1-backup.md` without overwriting an existing backup, then add schema 2 fields. Do not mutate notes, fabricate sources, infer learning status or regenerate closed artifacts during migration. Old captures have excerpt/unknown coverage until actual material is supplied. Unsupported future schemas require clarification, not silent downgrade.

`last_menu` 只代表最近一次显示给学习者的菜单，并保留每个显示数字对应的动作。计数从已成功写入的文件重新计算或更新，不能从聊天猜测。`pending_entries` is a map, not a single slot: each key is an event-or-fingerprint match key and each value records the reserved entry ID, canonical destination, input fingerprint, and source reference. `writer_lease` records the current runtime-provided exclusive writer or coordinator; it is not a lock implementation.

For compatibility with an incoming fixture that uses `state: active`, normalize it to `in-class` on read and write back `in-class`; `active` is never a persisted Classroom Copilot state.

## 合法状态转换

| From | Intent / condition | To |
|---|---|---|
| `idle` | start a named session | `in-class` |
| `idle` | resume an active persisted session | its persisted state |
| `in-class` | request a visualization | `visual-choice` |
| `in-class` | question needs clarification or teacher-ready wording | `question-refinement` |
| `in-class` | continue capturing, save, or ordinary follow-up | `in-class` |
| `visual-choice` | select a visual type, decline, or return to class | `in-class` |
| `question-refinement` | answer, formulate, or return to class | `in-class` |
| `in-class`, `visual-choice`, `question-refinement` | explicit close intent | `closing` |
| `closing` | `review.md` and editable `mindmap.md` were newly generated or updated for this session and pass the close-artifact validity gate | `closed` |
| `closed` | create optional study artifact or export | `closed` |
| `closed` | start a new lesson | `in-class` in a new session |

### Close-artifact validity gate

Do not transition to `closed` until both required close artifacts were newly generated or updated during this session's current `closing` operation. For a non-empty session, each must contain this session's exact non-empty `session_id`, non-empty source-entry evidence (`source_notes` in `review.md`; `source_entries` in `mindmap.md`) referencing canonical entry IDs, and no template placeholders such as `<...>`, `<fill at close>`, or empty evidence lists. The sole empty-session exception applies only when all three `_state.md` counts are zero: both artifacts must use the exact `session_id`, declare `empty_session: true`, keep their source lists empty, include explicit human-readable `no captured material` content, and contain no placeholders. Never invent entry IDs for an empty session. Existing start-time stubs, stale files from another session, or files that merely exist fail this gate. If a write or validation fails, retain the previous state (or `closing` during close) and use the no-write fallback rather than claiming closure.

## Menu and input resolution

A bare number resolves only when it exactly matches an item in `last_menu`; otherwise ask for a choice and do not infer an action. Any natural-language intent, including text that contains a number, overrides numeric resolution. A chosen visualization action enters `visual-choice` and offers two or three relationship-appropriate diagram types before generating one.

## Independent learning mode

`learning_mode` is not `state`. A selected review/Feynman action sets review/feynman and replaces last_menu; lifecycle remains unchanged. A classroom diversion saves the active practice as interrupted, then uses classroom mode. Visual/question detours from a closed review do not run in-class transitions: offer the same relationship options as a learning action and keep lifecycle closed. For an open lesson, use the existing visual-choice/question-refinement transitions.

“停止练习” saves interruption/end explicitly and returns to review without reopening class. “下课” during practice first preserves the unanswered turn and interrupts it, then applies the close gate to an open lesson. If already closed, do not regenerate artifacts solely to stop practice. “继续听课” from closed offers a new session; never reopens old records. Restore original active P and unresolved Feynman follow-up from files; ask whether to continue if mode was switched away.

## Multi-file learning writes

Only a sole coordinator or real runtime course-level lock may update shared course files. Session leases alone do not protect two sessions writing one course ledger. Without that coordination, defer shared writes and disclose that the evidence has not been fully saved.

Apply event/fingerprint reservation and highest-ID allocation to S (sources), P (practice), G (course profile), E (course ledger) and Feynman rounds. Store original payload and the complete fixed list of write targets/IDs in pending_entries before writing: sources+note for capture; practice+ledger+state for an answer. G/E sequences are course-wide; S/P sequences are session-wide. All targets must be the canonical session files or the two derived course-parent files.

Use the schema 2 composite map in file-formats.md for new multi-target reservations. Schema 1 pipe records remain readable for old single-target operations only; never invent missing targets for an incomplete old record. Reconcile legacy single-target operations before converting to a schema 2 composite operation. Validate canonical path ownership and actual payload at each target; a completion flag alone is insufficient.

For a source-only attachment that cannot be read, register S with unknown coverage and no N content claim. A readable whole document produces one N capture containing faithful read text (or a durable copied source plus exact read scope), not a guessed summary. A learner-requested attachment-reference note can exist but is not substantive course evidence. source_count is not included in the existing empty-close criterion.

An answered attempt has a stable match_key, original response, assistance, criteria-based result and reserved E ID. Repair partial entries under the same IDs, validate every target including payload, then clear pending. If practice is written but ledger fails, preserve pending; do not claim the learning judgment is durably updated. On retry find the existing E by match_key and session_id/P/attempt, never append a duplicate. A new answer or correction is a new attempt with new E; history remains. No Markdown pseudo-lock or multi-file atomicity is implied.

After a review answer is fully persisted, mark its round and P 已答 and clear active_practice_id to none in the same coordinated reconciliation. An answered Feynman round is 已答; if a next unanswered follow-up exists, P remains 未答 and active ID remains that P. On interruption the unresolved round/P becomes 中断 and the active ID remains resumable. Completed P is never re-presented as an unresolved question; explicit re-answer/retest creates a new attempt/round under reserved identities, not a tool retry. Clear pending only after these state expectations validate.

## Entry IDs and retries

Concurrent capture is supported only when the runtime provides an atomic exclusive session lock/lease or routes all session writes through one coordinator. A single agent that is the only agent currently handling writes for this interaction may act as that sole coordinator. `writer_lease: none` means no writer currently holds the lease; it does **not** mean that the session is unwritable. Before any reservation, an atomic-lock holder or sole coordinator must write its own runtime owner identifier to `writer_lease`, then re-read state while holding that role; only that owner may reserve or append. If the re-read state shows another holder, reload and defer. Release or clear the lease only after the append/reconciliation and state update complete. Do not invent a lock, lease, atomic operation, or coordinator role in Markdown.

If neither runtime primitive is available, concurrent capture is unsupported: detect that before reservation, leave the input unreserved and unappended, and tell the learner that the capture is queued/not yet saved. Resume it only when a single-writer capability is available; never claim it was durably saved.

For a new capture, prefer a runtime-supplied stable event/request ID and preserve it as the `event:<id>` map key. If none exists, before reservation persist a deterministic `fingerprint:<value>` made from normalized input plus its attachment/source reference; also retain that normalized payload/reference in the pending record. The fingerprint is a retry-matching aid, not a cryptographic guarantee. A retry must reuse its event ID or fingerprint; an identical input that the learner intends as a separate capture needs explicit learner confirmation before a new reservation.

Immediately before reserving, re-read `_state.md` and the canonical destination file. Derive the prefix from the destination (`N` for a note, `Q` for a question, `C` for a concept, `V` for a visual), find the highest existing ID with that prefix, and store the next zero-padded sequence (for example, `N004` or `V004`) together with the destination, fingerprint, and source reference under the match key in `pending_entries`. Use the same ID in the entry and derived-artifact references.

Immediately before appending, re-read `_state.md` and the stored destination file. First search that destination for the match key, input fingerprint, or reserved `entry_id`. A match is complete only after validating the entire reserved note or question payload: all required schema fields are present; `original_capture` and `learner_annotation`, plus the attachment/source reference, match the persisted pending normalized payload/fingerprint contract; and derived fields and status are structurally complete. For a note, this includes its required metadata and `derived_artifacts`; for a question, this includes `source_entries`, `evidence_boundary`, `status`, course evidence, and answer or teacher-ready wording. If the matching entry is partial, repair it under the writer lease using the same reserved ID, revalidate it, and only then clear pending; never allocate another ID. If it is complete, remove that key from `pending_entries`, refresh counts, and do not append a duplicate. If none exists, append once with the reserved ID, then remove the key and release `writer_lease` only after the append and state update succeed. Generate a new ID only for a new learner input, never merely because a tool call was retried. If re-reading reveals a changed lease or reservation, restart from the re-read state instead of appending.

For every new schema 2 N entry, source_id is required and must reference a complete existing S in this session; validate the reciprocal source_entries link before clearing its capture pending operation. Legacy N entries may retain unknown without fabricated provenance. A note pointing to an unreadable S must identify itself as reference-only, not readable course evidence.

### Visual IDs and retries

For a selected visual, use the same runtime lease and event/fingerprint retry identity rules as captures. Reserve the next `V###` ID under its match key before creating the file, with a canonical destination `visuals/<visual-id>-<primary-source-entry-id>-<type>.md` (for example, `visuals/V001-N001-flowchart.md`). A retry reuses that reservation, `visual_id`, and destination; it must not allocate a second visual. A separate request, including another visual of the same type for the same entry, receives a new `V###` ID. Overwrite an existing visual only after the learner explicitly requests an update to that same `visual_id`; then retain the ID and append revision metadata rather than overwriting provenance silently.

## Legacy `pending_entry_id` recovery

When resuming the old pending protocol with `pending_entry_id`, copy it to `legacy_pending_entry_id` and retain it until reconciliation; do not overwrite or append from it. Scan the canonical targets in the documented order for that ID. If exactly one destination contains it, convert it to a `pending_entries` record keyed `legacy:<entry-id>` with that destination and status `requires reconciliation`; retain `legacy_pending_entry_id` until the existing entry is reconciled and counts are refreshed. If no destination, or more than one destination, matches, retain the legacy ID, make no append, and ask the learner to reconcile the intended capture/destination. Only after that learner reconciliation may a new event/fingerprint-backed reservation be created.
