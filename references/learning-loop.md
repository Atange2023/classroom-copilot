# Learning loop: goals, recall, Feynman and resumption

Read with file-formats.md when writing learning files and state-machine.md when switching modes or reserving/retrying writes. All learner-facing feedback follows interaction-contract.md. This file defines behavior, not a scheduler.

## Goals and evidence boundaries

Setting a goal records the learner's words in a course G entry. Ask one focused question for an unknown application or completion criterion; offer a suggested criterion as draft, not a confirmed learner choice. Capture never waits for goal setup. An explicit goal request can remain draft while practice proceeds with source-based criteria.

For learning status, inspect actual sources, practice attempts and ledger. Report topic + observed status + evidence + next useful check, not a percentage or permanent mastery. If no learning files exist, say “已接触，尚未验证” from the capture, without creating files solely for status display. A diagram alone is not an attempt. If the course evidence cannot adjudicate the proposed question, narrow it or ask for material/teacher clarification; do not grade invented criteria.

## Start review

1. Resolve the intended course/session. Resume any existing unresolved P before creating a new one, or honor explicit learner intent to start a different exercise and interrupt the old P.
2. Read actual captured sources. Ask one recall/application question targeted at the learner's goal, unresolved confusion or selected concept. Explain its purpose briefly.
3. Under the coordinator, reserve P and store prompt, source_refs, criteria and an empty unanswered round, then persist active_practice_id, learning_mode: review and current last_menu. Only now show the question; do not print criteria or answer.
4. Wait for the student's answer. Offer actions such as hint, show answer, pause and change topic; mark hint/answer exposure durably before providing it. A bare displayed menu number chooses an action. For a numeric answer request “答案：2” (free text); never silently treat the same number as both menu action and response.
5. Preserve the original response as A. Log assistance: none only when the independent exercise actually occurred without revealed criteria/answer, hints or AI authorship. If prior exposure is uncertain, use unknown and request a fresh independent exercise. The student can always ask for the answer; this changes evidence quality, not permission to learn.
6. Assess against saved criteria and actual material, explain one concrete strength/gap, and append one E linked to P/A/source. Use the multi-file pending procedure; after a single review answer mark P/round 已答 and active_practice_id none, validate all targets, then confirm saved status. In Feynman, retain active P only for an actual saved unanswered follow-up. No response means no performance evidence.
7. Offer a targeted next exercise, Feynman practice, learner-drawn map check, or pause. Do not launch the next test without selection.

## Learning-status decisions

| Observation | Current status / treatment |
|---|---|
| source captured or artifact generated, no attempt | 已接触; no E for generated artifact |
| independent check planned, no answer / unverifiable / correct assisted repetition | 待验证; assistance and uncertainty retained |
| actual explanation/application has substantive error or confusion | 需澄清; cite the exact response and criterion |
| unassisted explanation passes supported criteria | 独立解释通过; include dated evidence |
| unassisted new application passes supported criteria | 应用通过; no claim of long-term retention |

Assisted wrong answers may expose confusion; assisted correct answers cannot supersede a previous independent success with stronger mastery. Keep earlier observations and describe what remains uncertain. Unknown assistance never qualifies as independent. Rebuild current status from the ordered valid E records, not stale cached rows; unexplained conflicting timestamps use recorded entry order and disclose uncertainty. A later error marks current status 需澄清 without deleting prior success. A later dated retest records actual retention observation and task type; no automatic forgetting curve or guaranteed retention.

## Feynman: AI as a student

Select a topic from available material and student role: default 初学者, or learner-selected 实践者. Create a P with mode feynman and a first prompt asking the learner to explain; criteria stay private until requested. Do not supply the explanation first.

When the learner explains, persist their exact wording and assistance, assess a round using source-based criteria, then behave as the selected student: ask one specific question caused by their explanation or documented confusion. Save that follow-up as the next unanswered round before showing it. Examples and analogies may be AI suggestions; they are not course facts.

Example: learner says “状态层就是反馈菜单，交互层记住上课下课。” A novice can ask: “同样输入2，第一次选图、第二次选层级图，谁记住了菜单变化？” Wait for their teaching response, rather than listing every error or lecturing on their behalf. If the learner asks for instructor feedback, leave the student role and explain the gap explicitly.

“结束费曼” saves the last round and summarizes demonstrated explanations, exact gaps and one next check in selected feynman-practice.md; unanswered round stays 中断 and resumable. Do not label a blank turn incorrect. “停止/暂停” preserves the unresolved turn without forcing a summary. “下课” interrupts training then runs normal closing only if lifecycle is open. A closed class remains closed in review/feynman.

## Resume and review plans

Read the state-machine sequence from actual files. Check active P exists and belongs to session; if missing/inconsistent, explain and ask to reconcile instead of fabricating a question or success. For an unresolved round show its original prompt, not criteria/answer, and carry exposure marks across Agents. If interrupted or mode changed, offer resuming it without losing new mode intent. Repair pending practice/ledger writes first; incomplete ledger evidence does not establish a saved status.

Confirm review dates/timezone from learner intent; relative dates need a known current date/timezone, otherwise clarify. “2天后复习” is a plan request, not menu action 2. Store plan and say it is checked when manually awakened, not an automatic reminder. At wake-up list due tasks briefly and let learner choose; no automatic test or completion. Link a completed plan to the actual attempt. User can edit/cancel plans; stored learning history remains separate.

## Local shareable output and forgetting

Create a new local shareable copy after selection. Include course-grounded concepts, public examples and selected teaching material only; exclude learner-private annotations, profile, ledger, original answers, raw screenshots containing personal information and private identifying metadata. Review the generated copy for semantic leaks as well as marker strings. Do not overwrite the private source. Suggestion links stay labelled AI建议 unless learner accepts them; cross-course links are suggestions, not automatic merges.

Read/edit learner-confirmed preferences and links on request. For “忘掉这条” identify the exact record; when the target is ambiguous ask one question before deletion. Removing underlying learning evidence needs explicit scope, a recoverable backup where permitted and recomputation of dependent judgments. Do not imply deletion from host chat history/cloud logs. No upload, public repository write or third-party sync follows from a local export request.

## No-write branch

Disclose which capture/attempt/ledger/state writes failed and what succeeded. Continue in chat with one temporary Markdown record containing source scope, goal, original answer with assistance, unanswered prompt and next action. Do not present saved counts or status for failed writes. The copyable record is a manual handoff, not a guarantee of cross-Agent resume. Never leak private data in a shareable fallback.
