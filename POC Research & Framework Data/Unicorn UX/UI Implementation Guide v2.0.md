PRD Template Pack (Engineer Hand-Off Standard)
How to use this pack

Every new feature ships with: Feature PRD + Instrumentation Spec + QA Gate.

If the feature introduces a new reusable pattern: add a Blueprint PRD.

If the feature changes tokens/motion system: add a Token Change PRD.

If it’s uncertain/high-impact: add an Experiment PRD.

0) Unicorn Core Requirements (attach to every PRD)
The 5 Principles → System Requirements (must be explicitly satisfied)

1) Invisible Interface

Requirement: user can achieve goal without learning UI mechanics.

Implementation patterns: intent-first CTA, point-of-need actions, contextual affordances, “resume task.”

Tests: first-time completion w/o help; time-to-primary-action.

Instrumentation: time_to_primary_action, nav_depth, help_opened, resume_clicked.

2) Emotional Design

Requirement: the UI actively reduces anxiety and reinforces progress.

Patterns: micro-confirmations, friendly loading, non-blaming errors, small celebrations on meaningful wins.

Tests: abandonment dips, rage-click reduction, satisfaction pulse.

Instrumentation: rage_click, abandon_step, micro_success, celebration_shown, sentiment_pulse.

3) Contextual Intelligence

Requirement: UI adapts using signals (history, device, stage, competency, friction).

Patterns: smart defaults, adaptive nav, next-best-action, prefill + explainability.

Tests: reduced search usage; fewer backtracks; higher completion.

Instrumentation: context_snapshot, prefill_shown/accepted, nba_shown/accepted, backtrack.

4) Progressive Disclosure

Requirement: complexity staged into Default → Revealed → Expert.

Patterns: advanced drawer, contextual menus, expert mode toggle, mastery-based unlocks.

Tests: novices not overwhelmed; experts faster than baseline.

Instrumentation: surface_level, advanced_reveal, expert_mode_on, shortcut_usage.

5) Sensory Harmony

Requirement: consistent tokens, motion purpose, feedback language, and performance.

Patterns: semantic tokens only, motion tokens by purpose, coherent spacing/type ramp.

Tests: token lint pass; visual regression pass; reduce-motion pass; perf pass.

Instrumentation: token_version, motion_profile, fps_drops, visual diff snapshots.

1) Master PRD Template (Initiative / Epic)

Use when shipping a set of features tied to one outcome.

1.1 Overview

Initiative name:

Owner (PM/Design/Eng):

Problem statement (1–2 lines):

Primary user segment:

Primary JTBD (top 1):

Success definition (measurable):

1.2 Unicorn Outcomes (select targets)

TTFW target:

Cognitive Load Index target:

Perceived control proxy target: (undo usage, reduced errors, etc.)

Delight density target:

Error recovery confidence target:

Context appropriateness target:

Sensory cohesion target:

1.3 Scope

In scope:

Out of scope:

Non-goals:

1.4 Constraints & Tradeoffs

Platform constraints (Web/Mobile):

Accessibility constraints:

Performance budget: (interaction latency, animation fps, bundle impact)

Security/Privacy guardrails:

1.5 Journey Map (high level)

Entry points:

Core steps (bullet list):

Exit conditions / completion state:

1.6 Risk Register

UX risks:

Tech risks:

Data/ethics risks:

Adoption risks:

Mitigations:

1.7 Release Plan

Milestones: Prototype → Alpha → Beta → GA

Rollout: feature flag strategy, cohorts, rollback plan

Post-launch monitoring window + owner

2) Feature PRD Template (Required for every feature)

This is the “engineer-ready” PRD.

2.1 Feature Summary

Feature name:

One-line value:

User segment(s):

JTBD:

Primary workflow this attaches to:

Entry points:

2.2 Success Metrics (must include)

Primary KPI: (e.g., completion rate, activation, conversion)

Guardrail metrics: (error rate, abandon rate, rage clicks, perf)

UX Outcomes impacted: (choose from the 7 outcomes)

2.3 Default → Revealed → Expert (Progressive Disclosure Map)

Define what each surface contains.

Default surface (must be simple):

Primary CTA:

Required inputs (max 3–5):

Inline guidance:

Escape hatch:

Revealed surface (contextual):

What becomes available:

Trigger(s) to reveal:

Where it lives (drawer/menu/secondary panel):

Expert surface (power):

Expert actions:

Shortcuts/batch operations:

Unlock rules (mastery or toggle):

2.4 Contextual Intelligence Spec
Context signals required (minimum)

user_recency:

workflow_stage:

competency_level:

friction_signals:

device/input_mode:

has_data / draft_state:

Smart defaults / Prefill

What is prefilled:

Source of truth:

Explainability copy (“We prefilled X because…”):

User control (edit/reset):

Next Best Action (if applicable)

NBA candidates:

Ranking logic (rules):

“Why this” explanation:

Dismiss / don’t show again:

2.5 Interaction & Motion Spec (must be tokenized)

Primary interactions (tap/drag/swipe/keyboard):

Feedback within 100ms? (yes/no + how)

Loading strategy:

skeleton threshold:

progress messaging threshold:

Success feedback:

Error feedback:

Motion purposes used: (feedback/transition/orientation/attention)

Reduce-motion alternative:

2.6 States (must define all)

Empty state:

Loading state:

Partial data:

Error states (validation, network, permissions):

Success state:

Undo/rollback state:

2.7 Content/Copy Requirements

Tone rules (non-blaming, action-oriented):

Microcopy for:

primary CTA

error messages

empty state

loading reassurance

success confirmation

2.8 Accessibility Requirements

Keyboard navigation:

Focus order:

Screen reader labels:

Contrast compliance approach:

Motion reduction support:

2.9 Data / Privacy / Ethics Guardrails

Data used for personalization:

“Why am I seeing this?” surface:

Reset personalization:

No sensitive inference statement:

2.10 Implementation Notes (Engineering)

Feature flags:

Dependencies:

API changes:

Telemetry dependencies:

Performance considerations:

2.11 Acceptance Criteria (Definition of Done)

A feature is “done” only if ALL apply:

Blueprint used or created (link it)

Default/Revealed/Expert implemented as specified

Instrumentation implemented + validated

Empty/loading/error/success states implemented

Token compliance passes + visual regression passes

Reduce-motion + accessibility checks pass

QA test matrix pass (attach checklist)

3) Blueprint PRD Template (Reusable Pattern Spec)

Use when creating a pattern you expect to reuse (canvas, smart form, onboarding, etc.).

3.1 Blueprint Identity

Blueprint name:

When to use:

When NOT to use:

Closest existing blueprint (if any):

3.2 Anatomy

Required components:

Optional components:

Layout rules (responsive):

3.3 State Machine

States list:

Transitions:

Guardrails (what must never happen):

3.4 Progressive Disclosure Rules

Default surface contents:

Revealed triggers + contents:

Expert unlock rules + contents:

3.5 Microinteraction Spec

Input feedback:

Motion purposes + token references:

Loading model:

Error recovery + undo:

3.6 Instrumentation Contract

Required events:

Required properties:

Success metrics for the blueprint:

3.7 Test Pack

First-time usability test:

Expert speed test:

Sensory cohesion checks:

Regression checks:

4) Instrumentation Spec Template (Required)

This is the analytics contract engineers implement.

4.1 Event Dictionary (must include naming conventions)

Naming: snake_case, verb-first, consistent across platforms.

For each event:

Event name:

When it fires:

Properties: (key/type/example)

PII check: (allowed/not allowed)

Owner:

Required baseline events for Unicorn UX

screen_view

primary_action_click

time_to_primary_action_start/stop

surface_level (default/revealed/expert)

advanced_reveal

form_error (with field + reason)

loading_over_threshold

error_shown

undo_used

rage_click (heuristic)

sentiment_pulse (optional 1-tap)

4.2 Metrics Computation

Define exactly how to compute:

TTFW:

Completion rate:

CLI (your friction index):

Error recovery time:

NBA acceptance rate (if NBA exists):

4.3 Dashboard Requirements

Dashboard name:

Top tiles:

Alerts (thresholds + who gets pinged):

5) UX QA Gate Template (Required)

Attach this to every Feature PRD.

5.1 Standard Unicorn QA Checklist

Invisible Interface

 Primary CTA is visible in the first viewport

 First-time path has no “dead ends”

 Navigation depth does not exceed target

Emotional Design

 Tap feedback within 100ms

 Loading has reassurance (skeleton + copy if > threshold)

 Errors provide fix + retry/undo and never blame the user

Contextual Intelligence

 Prefills are correct + editable + explainable

 UI doesn’t “jump” mid-task due to context changes

 Personalization controls exist (why/reset)

Progressive Disclosure

 Default surface minimal (no advanced clutter)

 Revealed surface triggers are implemented

 Expert surface gated/unlocked correctly

Sensory Harmony

 Tokens only (no one-offs)

 Motion uses purpose tokens

 Reduce-motion verified

 Visual regression pass + contrast pass

 Performance pass (interaction latency + fps)

5.2 Test Scripts (paste-ready)

First-time completion test: “Complete X without help.”

Error recovery test: “Trigger validation + network error; recover without leaving flow.”

Progressive disclosure test: “Novice sees default; expert sees speed tools; no overwhelm.”

Reduce-motion test: “All motion replaced with non-animated state changes.”

6) Token & Motion Change PRD Template (When design system changes)

Use for any token taxonomy updates or motion/easing updates.

6.1 Change Summary

What tokens are added/changed/deprecated:

Why (problem + impact):

Compatibility plan:

6.2 Semantic Mapping

Old → new mapping:

Any meaning changes (must be explicit):

6.3 Motion Policy Change

Duration/easing changes:

Purpose mapping changes:

Reduce-motion implications:

6.4 Rollout Plan

Versioning:

Migration steps:

Lint rules updates:

Visual regression plan:

7) Experiment PRD Template (Optional, but recommended for uncertain bets)
7.1 Hypothesis

If we ______, then ______ will increase because ______.

7.2 Variants

Control:

Variant A:

Variant B (optional):

7.3 Success + Guardrails

Primary:

Guardrails (perf/errors/complaints):

7.4 Segmentation

Who is included/excluded:

Cohort freezing rules:

7.5 Instrumentation

Events needed:

Analysis plan:

7.6 Stop Conditions

Win threshold:

Loss threshold:

Duration cap:

8) Release Checklist (Required for launch)

 Feature flag is live + rollback tested

 Instrumentation verified in staging + prod

 QA Gate passed

 Accessibility pass + reduce-motion pass

 Visual regressions reviewed

 Dashboard + alerts configured

 Post-launch owner assigned (48–72h)

 Support doc/tooltip updates done (if needed)

Appendices (Copy/Paste Standards)
A) Context Snapshot (minimum schema)

context_snapshot = { user_recency, workflow_stage, competency_level, friction_signals, device, input_mode, has_data, draft_state, plan_tier }

B) Progressive Disclosure Policy (default rules)

Default surface for novice OR high friction OR new workflow stage

Reveal surface after 2–3 successful completions OR explicit request OR stall trigger

Expert surface after expert mode OR mastery threshold OR power-user signals

C) Motion Purpose Rubric (enforcement)

If an animation does not satisfy one of these, it gets removed:

feedback, causality, orientation, priority