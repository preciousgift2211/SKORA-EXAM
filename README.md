# SKORA Student Portal — Frontend Prototype & Backend Handover

**Version:** 1.0  
**Product:** SKORA student portal  
**Exam focus:** JAMB / UTME  
**Status:** Frontend prototype; not production exam software.

## 1. Purpose

This package defines the student-facing SKORA experience: overview dashboard, Examination Centre, Classroom, Statistics, Activity History, Motivation Centre, Settings and Help Centre. It includes responsive UI, navigation, demo exam flow, practice feedback, a basic calculator, chart rendering, subject status lines, theme/font preferences and a starter content catalogue.

The package is intended for the SKORA backend team as a functional and visual handover. It is not a connected production application. All demo scores, questions and topic outlines are illustrative. Authentication, secure question delivery, scoring, server timers, AI, dictionary, notifications and persistence across devices must be implemented by the backend.

## 2. Brand system

- Blue: `#1565C0`
- Green: `#00875A`
- White: `#FFFFFF`
- Dark navy: `#0F172A`
- Headings: Manrope; body: DM Sans.
- Logo: `assets/skora-logo.jpeg` (copied from the existing SKORA landing-page source).
- Visual direction: professional education SaaS, generous spacing, restrained colour, accessible contrast, subtle borders, responsive cards.
- Theme control is intentionally available **only inside Settings**. Do not add a theme switcher to the dashboard header or exam toolbar.

## 3. Project files and local run

- `index.html` — all page structure, navigation, dialogs and forms.
- `styles.css` — responsive styling, light/dark themes and component states.
- `script.js` — prototype navigation, sample question flow, charts and browser-local preferences.
- `assets/skora-logo.jpeg` — SKORA logo.
- `README.md` — this handover document.

No build step is required. Extract the ZIP and open `index.html` in a browser. Recommended local server:

```bash
python -m http.server 8000
```

Then visit `http://localhost:8000`.

## 4. Information architecture

1. Overview
2. Examination Centre
3. Classroom
4. My Statistics
5. Activity History
6. Motivation Centre
7. Settings
8. Help Centre

### Overview
Contains greeting, date, exam CTA, summary metrics, recent score trend, daily study prompt and quick links. Production metrics must come from authenticated backend records, not browser calculations.

### Examination Centre
JAMB is the only exam body in this first portal release. It contains Practice, Mock and Test.

**Practice mode**
- Student can choose random questions or a particular JAMB year.
- Student can choose a question count.
- No final score is shown.
- Immediate feedback follows each answer.
- Show correct option and a clear step-by-step explanation.
- AI and dictionary are permitted.
- Practice accuracy may be tracked separately but must not be silently merged into official Mock/Test score averages.

**Mock mode**
- Student can choose random questions or a specific year; mixed-year random is the default.
- Timed examination experience.
- On-screen calculator.
- AI and dictionary are disabled for the entire active attempt.
- Show final score and time-management feedback after submission/timeout.
- Do not show answer corrections, answer key, explanations or item-level review.
- The no-review rule must be enforced by backend authorization and response shaping, not just by hiding UI.

**Test mode**
- Student selects a particular JAMB year.
- Timed examination experience and on-screen calculator.
- AI and dictionary disabled during active test.
- On submission, show final score, answer corrections and explanations.
- AI/dictionary may be enabled in the post-submission review experience.

**Question source**
- `random`: backend selects from approved question pools according to the selected mode.
- `year`: backend must return questions belonging to exactly the selected year.
- Store the actual source year for every question in the attempt.
- Mock random mode should sample across approved years by default.
- Validate available years server-side; a year in a dropdown is not proof of content availability.

### Timer and JAMB format
The browser timer is a display only. Backend must create `started_at`, `expires_at`, allowed duration and attempt status; validate every answer save against server time; finalize timed-out attempts; prevent refresh from resetting time; and define reconnect/network-loss behavior. Confirm the current official JAMB question count, duration, subject distribution and calculator policy before configuring production. Do not assume the demo values are official.

### Calculator
A basic arithmetic calculator is included as a prototype. Confirm permitted operations and actual JAMB candidate guidance before production. It is not an official JAMB calculator emulator.

## 5. Classroom

The starter catalogue includes Mathematics, English Language, Physics, Chemistry, Biology, Economics, Government, Literature in English, Commerce, Agricultural Science, Accounting and Geography.

The displayed topic outlines are placeholders, not a complete approved syllabus. Each subject must be checked against the current official JAMB syllabus before publication.

Recommended content model:
- subject and syllabus version
- topic, subtopic and learning objectives
- note body (sanitized Markdown or rich text)
- definitions/key terms
- formulae where relevant
- worked examples
- revision questions
- author, reviewer, approval status and publication timestamp
- content version/history

A topic note should contain an introduction, explanations, definitions, formulas/key facts, worked examples, common mistakes and revision questions.

## 6. Statistics and subject performance

The Statistics page should show scored attempt count, average score, average completion time, recent trend, score history, four subject trend lines and attempt history.

Each student has their own four registered JAMB subjects. Do not assume the same four subjects for all candidates.

Proposed initial status bands:
- Green / Strong: 70–100%
- Yellow / Average: 50–69%
- Red / Needs improvement: 0–49%

These thresholds must be configurable. Use a meaningful rolling/selected-period average and a minimum sample size. Show “Not enough data” when the sample is insufficient. Do not label a subject based on a single answer. Line direction communicates trend; line/status colour communicates current performance band. A strong subject can trend down; a weak subject can be improving.

Only scored Mock/Test results should feed the official exam score history by default. Practice analytics should be a separate view/metric.

## 7. Time-management analytics

Record allowed duration, server start and submission timestamps, actual duration, manual submission vs timeout, question count, answered count, unanswered count and score/accuracy.

Suggested feedback:
- Completed with time remaining: positive pacing feedback, while reminding students to preserve accuracy.
- Completed within time: acknowledge completion.
- Incomplete/timed out: show unanswered count and suggest pacing practice.
- Improvement: compare similar modes and question counts only.

Do not call a student “fast” solely because they submit early. Consider accuracy and unanswered questions.

## 8. AI and dictionary access rules

| Context | AI | Dictionary |
|---|---:|---:|
| Practice | Allowed | Allowed |
| Classroom study | Allowed | Allowed |
| Active Mock | Blocked | Blocked |
| Active Test | Blocked | Blocked |
| Post-submission Test review | Allowed | Allowed |
| Mock result/review | No corrections | No corrections |

Backend must enforce this matrix. Do not rely on disabled buttons alone.

Suggested AI route: `POST /api/v1/learning/ai/explain`  
Suggested dictionary route: `GET /api/v1/learning/dictionary?term=...`

Keep AI/provider keys on the server. Apply authentication, rate limits, quotas, usage monitoring and safe context minimisation. AI should explain concepts step by step and ideally be grounded in SKORA-approved notes and solutions. Label AI-generated explanations and allow inaccurate responses to be reported.

## 9. Settings

Settings tabs:
- **Appearance:** Light, Dark and System themes.
- **Accessibility:** font scale (prototype range 85%–125%, step 5%), reduce motion.
- **Profile:** display name, email and four subject preferences.
- **Learning preferences:** daily study target and reminders.

Production preferences should be saved to the student's account, e.g. `theme`, `font_scale`, `reduce_motion`, `daily_study_target`, `reminders_enabled`, `updated_at`. The verified registered subject combination must remain authoritative; changing a display preference must not silently change official exam registration.

## 10. Motivation and activity

Motivation milestones may include first learning activity, study on three distinct days and improved comparable scored attempt. Only display achieved status when the underlying event exists. Messages must be supportive and non-shaming.

Suggested activity event types:
- `practice_started`, `practice_completed`
- `classroom_note_opened`, `classroom_topic_completed`
- `mock_started`, `mock_submitted`
- `test_started`, `test_submitted`, `test_reviewed`

Suggested fields: `id`, `student_id`, `event_type`, `entity_type`, `entity_id`, `metadata`, `occurred_at`, `duration_seconds`. Avoid storing unnecessary sensitive question/answer payloads in generic activity metadata.

## 11. Suggested backend data entities

Adapt these to the existing SKORA architecture rather than creating duplicate systems.

- `users`: id, email, auth reference, status, timestamps
- `student_profiles`: user_id, display_name, registered_subject_ids, preferences
- `subjects`: id, name, slug, active, syllabus_version_id
- `syllabus_versions`: id, exam_body, effective session, source reference, approval status
- `topics`: id, subject_id, syllabus_version_id, parent_topic_id, title, sort_order, status
- `study_notes`: id, topic_id, body, examples, revision questions, version, author/reviewer, status
- `questions`: id, subject_id, exam_body, exam_year, topic_id, prompt, options, correct answer, explanation, difficulty, source reference, approval status
- `exam_configurations`: id, mode, duration, question count, subject distribution, allowed tools, version
- `exam_attempts`: id, student_id, mode, source type, selected year, config id, started_at, expires_at, submitted_at, duration, status, timed_out, totals, score
- `exam_attempt_questions`: attempt_id, question_id, position, immutable question version/snapshot reference, selected answer, correctness, answered_at
- `attempt_subject_results`: attempt_id, subject_id, questions presented, correct count, score percent
- `student_activity`: id, student_id, event type, entity, metadata, occurred_at, duration
- `student_preferences`: student_id, theme, font scale, reduce motion, daily target, reminders, updated_at
- `ai_interactions`: id, student_id, context, usage metadata, response reference, created_at

Use immutable question versions/snapshots so historical attempts are not altered when content is edited.

## 12. Suggested REST API contract

**Profile/preferences**
- `GET /api/v1/student/profile`
- `PATCH /api/v1/student/profile`
- `GET /api/v1/student/preferences`
- `PATCH /api/v1/student/preferences`

**Classroom**
- `GET /api/v1/subjects?exam_body=jamb`
- `GET /api/v1/subjects/{subjectId}/topics`
- `GET /api/v1/topics/{topicId}/notes`
- `GET /api/v1/notes/{noteId}`
- `POST /api/v1/topics/{topicId}/progress`

**Exam**
- `GET /api/v1/exams/configurations?exam_body=jamb`
- `GET /api/v1/exams/years?exam_body=jamb&subject_id=...`
- `POST /api/v1/exam-attempts`
- `GET /api/v1/exam-attempts/{attemptId}`
- `PUT /api/v1/exam-attempts/{attemptId}/answers/{attemptQuestionId}`
- `POST /api/v1/exam-attempts/{attemptId}/submit`
- `GET /api/v1/exam-attempts/{attemptId}/result`
- `GET /api/v1/exam-attempts/{attemptId}/review` (Test only; never Mock)

**Analytics**
- `GET /api/v1/student/statistics/overview?period=...`
- `GET /api/v1/student/statistics/scores?period=...`
- `GET /api/v1/student/statistics/subjects?period=...`
- `GET /api/v1/student/statistics/time-management?period=...`
- `GET /api/v1/student/activity?type=...&cursor=...`

**Learning tools**
- `POST /api/v1/learning/ai/explain`
- `GET /api/v1/learning/dictionary?term=...`

## 13. Critical backend security requirements

1. Authenticate all student API calls.
2. Authorize attempt ownership against the authenticated student.
3. Enforce mode, state and expiry on the server.
4. Never trust client timer, score, question count or correctness.
5. Do not send correct answers/explanations during active Mock/Test.
6. Never expose Mock review endpoint/data.
7. Block AI/dictionary calls during active Mock/Test, even if called directly.
8. Prevent attempt-ID tampering and cross-student access.
9. Use rate limits, validation, idempotent submission and transactional finalization.
10. Sanitize study-note content and use role-based content authoring/review permissions.
11. Keep provider/API secrets server-side.
12. Use HTTPS, secure session handling, CSRF protection where relevant and strict CORS.
13. Audit security events without logging passwords, secrets or unnecessary personal data.

## 14. Accessibility and responsive quality

Use semantic headings/landmarks, keyboard-accessible controls, visible focus, accessible labels for icon controls, sufficient contrast in both themes, reduced-motion support and mobile/tablet/desktop testing. Do not use colour alone for subject status; always include text labels.

## 15. Known prototype limitations

- Demo question set only; it is not a JAMB past-question bank.
- The demo repeats sample questions if a larger count is selected.
- Selecting a year does not load a year-specific database.
- Scores/activity/preferences are local to the current browser.
- Clearing browser storage clears demo history.
- No login, account sync or secure backend is connected.
- AI and dictionary show integration placeholders only.
- Calculator is basic arithmetic, not an official JAMB emulator.
- Subject/topic catalogue is not a complete approved syllabus.
- The browser timer is not secure and must never run a live high-stakes exam.
- Charts show demo/local data only.

## 16. Production readiness checklist

- [ ] Confirm current official JAMB duration, question distribution, exam tool rules and calculator policy.
- [ ] Confirm available past-question years and subject coverage.
- [ ] Review and approve all questions, answer keys and explanations.
- [ ] Verify notes against current JAMB syllabus.
- [ ] Connect authentication, student profile and registered subject combination.
- [ ] Implement server-authoritative attempts, answer saving, timer expiry and scoring.
- [ ] Enforce Mock no-review and active-exam AI/dictionary restrictions on the server.
- [ ] Implement Test correction/review endpoint.
- [ ] Implement subject-level analytics and minimum sample policy.
- [ ] Implement time-management feedback.
- [ ] Persist settings and preferences to student accounts.
- [ ] Connect secure AI and dictionary providers.
- [ ] Add loading, error, offline, timeout and empty states.
- [ ] Perform accessibility, security and load testing.
- [ ] Add privacy policy, terms, student support and monitoring.
- [ ] Complete staging acceptance test and rollback plan.

## 17. Handover principle

The frontend controls presentation. The backend controls identity, approved content, question selection, timer integrity, scoring, review permissions, analytics and student data. Connect the APIs without weakening the product rules documented above.
