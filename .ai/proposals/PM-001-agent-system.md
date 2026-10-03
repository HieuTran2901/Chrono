# PM-001 — Agent system baseline

author: prompt-master
task_id: PM-BOOT-001
status: ACCEPTED
updated_at: 2026-10-03 (Asia/Saigon)
source_branch: none (local bootstrap)
references: GOV-001; original CHRONOS MULTI-AGENT DEVELOPMENT SYSTEM; Prompt Master audit delivered in prior chat turn

## Problem and current design

Original workflow lacks Prompt Master ownership and some path owners, has branch-local stale context, unpinned review approvals, ambiguous multi-branch testing, Coordinator develop exceptions, two incompatible review approval forms and no evidence-based writer replacement.

## Accepted changes

P1: scoped Prompt Master, explicit document owners and Source of Truth; Architect + Owner architecture/API authority; Release-only develop integration; Reviewer never edits production.
P2: nine required .ai groups, short role prompts and shared rules; author-owned files published/read at branch/SHA; separate code checkouts and one writer; recovery against evidence.
P3: immutable reviewed candidates including dependencies/tests/base; explicit severity disposition; Release may stage isolated unreviewed combinations before review, while actual develop integration remains gated; main requires specific Owner approval.

## Alternatives and trade-offs

A shared multi-writer branch was rejected as the baseline approach because it weakens branch ownership.
Reading author-published files avoids cross-branch writes but requires fetch/revision tracking.
Candidate rechecks add effort when actual code/base changes, in return for approvals applying to the tested result.
Event files are created only when an event happens; summaries link existing evidence instead of repeating it.

## Impact

Affects all roles and their communication/review behavior.
No production migration, new dependency, module creation, architecture selection or SDK signature change.
Unmapped implementation paths remain pending Owner approval.
Owner acceptance is recorded at [GOV-001](../context/DECISIONS.md); approval in chat is not itself a Git publication.
