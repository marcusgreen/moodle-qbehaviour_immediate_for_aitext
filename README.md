qbehaviour_immediate_for_aitext
================================

Question behaviour plugin for Moodle that extends immediate feedback to properly
persist AI-generated grading data via the question engine API.

Purpose
-------

Designed exclusively for use with `qtype_aitext`. Intercepts the grading steps to
write AI results (feedback, prompt, spellcheck) as cached behaviour variables on
the pending step — eliminating raw database writes from within the question type.

How it works
------------

On both submit and finish, `process_submit()` / `process_finish()` call the parent
immediate feedback grading flow, then read AI results cached on the question object
and write them to the step:

| Variable | Content |
| --- | --- |
| `_comment` | AI-generated feedback (HTML) |
| `_commentformat` | Format constant (`FORMAT_HTML`) |
| `_aiprompt` | Full prompt sent to the AI |
| `_spellcheckresponse` | Grammar/spelling correction (if enabled) |

Teacher manual comments (`comment`, `mark`) take priority over AI vars in the
renderer.

Spellcheck edits
----------------

When a teacher submits an edited version of the student's response via the AI
spellcheck dynamic form, the step carries a `spellcheckedit` behaviour variable.
`process_action()` routes this to `process_spellcheck_edit()`, which keeps the
step and preserves the current state and fraction (the grade does not change) so
the edited response persists for display in the renderer.

Requirements
------------

*   Moodle 4.5 or later
*   [`qtype_aitext`](https://github.com/marcusgreen/moodle-qtype_aitext) question type

Installation
------------

Place in `question/behaviour/immediate_for_aitext/`. Run Moodle upgrade.

This behaviour is not archetypal — it does not appear in quiz settings. It is
selected automatically by `qtype_aitext_question::make_behaviour()`.

Related
-------

*   [`qbehaviour_deferred_for_aitext`](https://github.com/marcusgreen/moodle-qbehaviour_deferred_for_aitext)
    — deferred feedback variant of this behaviour for `qtype_aitext`.

License
-------

GNU GPL v3 or later — http://www.gnu.org/copyleft/gpl.html

Copyright 2026 ISB Bayern
