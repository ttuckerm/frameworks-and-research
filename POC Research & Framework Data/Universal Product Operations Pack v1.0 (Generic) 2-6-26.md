Trendzo Product Operations Pack v2.0

Date: 3/3/26
Companion To: Trendzo Methodology Pack v2.0
Purpose: Document the PRODUCT (workflows, UX, acceptance, verification) with verified data
Source of Truth: prediction-audit.md (5 phases), system-registry.ts, bucket2-summary.md, live codebase

How These Two Documents Work Together

Methodology Pack = the engine (how VPS scoring, component analysis, and calibration work)
Product Ops Pack = the vehicle (what users do, what screens do, what "done" means, what actually works)

Use cases:
- Methodology: "Why is the VPS score wrong?" "How do we retrain?" "What does each component measure?"
- Product Ops: "What happens when I click Analyze?" "Is the prediction pipeline production-ready?" "What works vs what's mock?"

Part 1: Product Definition

1.1 What the Product IS

Trendzo is a viral video prediction platform that helps TikTok content creators predict how well their video will perform BEFORE publishing, understand WHY through component analysis, and receive specific recommendations to improve their content.

Platform: Next.js 14.2.x App Router + Supabase backend + Tailwind CSS
Primary workflow page: /admin/upload-test (uses /api/kai/predict)
Prediction pipeline entry point: src/lib/prediction/runPredictionPipeline.ts

1.2 Core Value Propositions

| Value Prop | Description | Status | Notes |
|-----------|------------|--------|-------|
| Predict | Upload transcript/video → VPS score + confidence + range | ✅ Working | 21 active components, 4 analysis paths, 2 calibration layers |
| Analyze | Breakdown by Pack 1 (9 attributes), Pack V (5 visual dimensions), Pack 2 (3 suggestions) | ⚠️ Partial | Requires GOOGLE_GEMINI_AI_API_KEY for Pack 1/2. Pack V works without API key for video predictions. |
| Recommend | Top 3 actionable improvements with estimated VPS lift | ⚠️ Partial | Pack 2 generates rule-based suggestions. Lift estimates are formula-derived, not empirically validated. |
| Track | Predicted VPS vs actual VPS learning loop | ⚠️ Infrastructure Only | Training pipeline automated. 27 labeled videos. Collection running but data volume is bottleneck. |
| Generate | Produce improved content based on analysis | ❌ Not Connected | Content generation (DPS-powered) exists in codebase but not integrated with prediction pipeline. |

1.3 User Personas

Primary: Content creator who wants to know "will this video go viral?" before publishing, and wants specific advice on what to change.

Secondary: Admin/operator who needs to monitor system health, manage training data, verify prediction accuracy across niches.

1.4 Product Boundaries

Does:
- Predict viral potential from transcript and/or video (VPS 0-100)
- Score 9 content quality attributes via AI (when API key available)
- Score 5 visual dimensions via rule-based + Gemini Vision analysis
- Provide 3 improvement suggestions with estimated VPS lift
- Track prediction runs in Supabase with full component-level results
- Auto-collect post-publication metrics for training data (via Apify cron)
- Show system health / pack status dashboard

Does not:
- Post content to TikTok or any social platform
- Process payments or manage subscriptions
- Edit or generate video files
- Guarantee prediction accuracy (27 labeled videos is insufficient for accuracy claims)
- Provide real-time cohort comparison (DPS infrastructure exists but not connected to pipeline)

Part 2: Page Inventory

2.1 All Pages (Verified)

| Page | URL | Purpose | Status | Notes |
|------|-----|---------|--------|-------|
| Upload & Test | /admin/upload-test | Primary prediction workflow | ✅ Active | The canonical prediction page. Uses /api/kai/predict. |
| Viral Studio | /admin/viral-studio | 9-phase content creation workflow | ⚠️ Mostly Mock | Gallery fetches real data. Calibration is real algorithm. Analysis/Lab phases are hardcoded. |
| The Studio | /admin/studio | Mega-page embedding viral studio + gallery + validation | ⚠️ Partial | ~159K chars single file. Embeds viral-studio as tab. |
| Training Readiness | /admin/operations/training | Training data management + milestone tracking | ✅ Active | Summary cards, readiness table, milestone tracker, automation health |
| Pack Health | /admin/operations/system-health | Component status grid, pack status, API keys | ✅ Active | Read-only dashboard from last 100 prediction runs |
| Operations | /admin/operations | Quick action links to sub-pages | ✅ Active | Links to training, system-health, etc. |
| Canvas | /admin/canvas/ | AI workflow canvas (projects, nodes, chat) | ✅ Active | Separate feature — not prediction-related |

2.2 Primary Workflow: Upload & Test Page

The /admin/upload-test page is where predictions actually happen. Everything else is secondary.

Page layout:
- Header: "21 Components" (dynamic from registry), "GPT-4o-mini" model reference
- Form: Video file upload (optional), transcript textarea, niche dropdown (20 niches, flat list, trained niches get emoji), account size selector, pipeline mode (Standard/Validation), goal textarea
- Results: VPS gauge (circular SVG), tier badge, confidence range, prediction context panel, Pack V/1/2/3 panels, pipeline steps, component execution details

Part 3: Workflow Specifications

3.1 Workflow Overview

| # | Workflow | Steps | Primary Value | Status |
|---|---------|-------|--------------|--------|
| 1 | Prediction (Upload & Test) | 4 | VPS score + analysis + recommendations | ✅ Working |
| 2 | Signal Calibration (Viral Studio) | 2 | User preference profile | ⚠️ Working but not persisted to pipeline |
| 3 | Gallery Browse (Viral Studio) | 1 | Browse viral template videos | ✅ Working (real data from scraped_videos) |
| 4 | Training Pipeline | 4 | Automated training data collection | ✅ Automated |

3.2 Workflow 1: Prediction (Primary)

Workflow Name: Video Viral Prediction
Purpose: Analyze a video's content and predict its viral potential before publishing
Why it matters: This is the core product value — the only workflow that runs the real prediction pipeline

STEP 1: INPUT
- User action: Upload video file (MP4, optional) and/or paste transcript text
- Accepted inputs: Video file (MP4), transcript (text, min 10 chars for Pack 1), niche selection, account size, pipeline mode, goal
- System action: Form validates inputs, enables "Analyze Video" button
- Success state: Button enabled, all required fields populated
- Error states: No transcript AND no video → button remains disabled
- API called: None (client-side only)

STEP 2: CONFIGURE
- Required fields: At least one of (transcript, video file). Niche (defaults to side-hustles).
- Optional fields: Account size (affects calibration multiplier), pipeline mode (Standard/Validation), goal text
- Validation rules: Transcript must be ≥10 chars if provided. Video must be MP4.
- Success state: "Analyze Video" button clickable

STEP 3: RUN
- API called: POST /api/kai/predict (multipart form data)
- API input: { videoFile?, transcript?, niche, goal?, accountSize?, mode?, excludeLLMsFromAggregate? }
- System processing:
  1. Video saved to disk + video_files row created
  2. prediction_runs row created (status='running')
  3. Transcript resolved (user-provided, Whisper auto-transcription, or none)
  4. KaiOrchestrator runs Phase 1 (parallel) then Phase 2 (sequential packs)
  5. Path aggregation → LLM consensus gate → agreement check
  6. Orchestrator calibration → pipeline calibration
  7. Results stored to prediction_runs + run_component_results
- Target time: 30-90 seconds (depends on video length, API latencies, component count)
- Loading state behavior: Progress bar with component execution steps, real-time pipeline stage updates
- Failure handling: Component failures are graceful — failed components excluded from aggregation. Pack failures shown in UI. Full prediction still returns if ≥1 component succeeds.

STEP 4: RESULTS
Results include:
- VPS score (0-100) in circular gauge with animated count-up
- VPS tier badge (Viral Potential / Excellent / Good / Average / Needs Work)
- Confidence range: [VPS - uncertainty, VPS + uncertainty]
- Prediction Context panel: account size, niche factor, calibration adjustments
- Pack V panel: 5 visual dimension scores with evidence
- Pack 1 panel: 9 attribute scores, 7 idea legos, hook analysis (or "Not Available" if no API key)
- Pack 2 panel: Top 3 improvement suggestions with estimated VPS lift (or "Not Available")
- Pack 3 panel: Viral mechanics detected with strength bars and evidence (or "Coming Soon" if stub)
- Pipeline steps: component execution count, scheduled vs executed, timing
- XGBoost badge: Only shown for trained niches (currently only side-hustles)
- Debug panel: pack errors surfaced (pack1_error, pack2_error), raw scores, calibration details
- All results stored in Supabase (prediction_runs + run_component_results)

Acceptance Criteria:
[x] At least one input (transcript or video) required
[x] Button disabled until valid input provided
[x] Loading state with real-time pipeline progress
[x] Completes within 90 seconds for typical predictions
[x] Errors shown in UI (pack errors surfaced, not silently swallowed)
[x] Results include VPS + confidence + range + tier
[x] Recommendations are specific and actionable (when Pack 1 runs)
[x] Prediction run persisted to Supabase with full component results
[x] No console errors in normal operation
[ ] Pack 3 shows real mechanics analysis (connected but may show stub depending on upstream data)

3.3 Workflow 2: Signal Calibration

Workflow Name: Signal Calibration (Viral Studio Phases 3-4)
Purpose: Build a 6-dimension creator preference profile via TikTok-style swipe interface
Why it matters: Could improve predictions by providing creator context (not currently connected to pipeline)

STEP 1: SWIPE CALIBRATION
- User action: Swipe up (accept) or down (reject) on 8 video cards
- Video pool: 20 hardcoded videos across 5 niches (Fitness, Business, Beauty, Education, Real Estate). 8 shown per session: 4 niche-matched + 4 cross-niche.
- Scoring: Accept = +15 weight, Reject = -10 weight, across 6 dimensions (nicheAffinity, hookStylePreference, toneMatch, audiencePainAlignment, editingStyleFit, contentFormatPreference)
- UX: TikTok-style drag gesture, green glow (accept), red glow (reject), keyboard shortcuts

STEP 2: PROFILE REVIEW
- System shows: Inferred niche, audience (age range + description), content style, competitors
- User action: Can edit any of the 4 inferred values, add offer description, add exclusions
- Completion: "THIS LOOKS RIGHT" advances to Gallery

Known limitation: Calibration profile is saved to Supabase (implemented 2026-03-02, Prompt 4) but NOT passed to the prediction pipeline. The profile persists across sessions but does not influence VPS predictions. Connecting it to the pipeline is a deferred enhancement.

3.4 Workflow 3: Training Pipeline

Workflow Name: Automated Training Data Collection
Purpose: Continuously collect post-publication metrics to build training dataset for model retraining

Automated steps (cron):
1. Backfill schedules (01:00 UTC daily) — schedule metric collection for videos that need it
2. Collect metrics (00:30/12:30 UTC) — Apify fetches real TikTok metrics
3. Auto-label (03:30 UTC) — lenient auto-labeling with labeling_mode='auto_cron'
4. Spearman evaluation (Sunday 05:00 UTC) — compute rank correlation accuracy

Monitor via: /api/training/pipeline-status, /admin/operations/training (readiness tab)

Part 4: API Specifications

4.1 Critical APIs

| Endpoint | Method | Purpose | Status |
|----------|--------|---------|--------|
| /api/kai/predict | POST | Run full VPS prediction pipeline | ✅ Active |
| /api/cron/training-pipeline | GET | Automated training pipeline steps | ✅ Active |
| /api/training/pipeline-status | GET | Training pipeline automation health | ✅ Active |
| /api/admin/operations/system-health | GET | Pack health dashboard data | ✅ Active |
| /api/outcomes/ingest | POST | Post-publication metrics ingestion | ✅ Active |
| /api/canvas/projects | CRUD | Canvas feature project management | ✅ Active (separate feature) |

4.2 Primary Endpoint: POST /api/kai/predict

Request (multipart form data):
```
{
  videoFile: File (MP4, optional),
  transcript: string (optional, min 10 chars for Pack 1),
  niche: string (hyphenated key, e.g., "side-hustles"),
  goal: string (optional),
  accountSize: string (optional, e.g., "small", "medium", "large"),
  mode: "standard" | "validation",
  excludeLLMsFromAggregate: boolean (optional, validation mode only)
}
```

Response:
```
{
  success: boolean,
  predicted_dps: number (0-100, the VPS score),
  confidence: number (0-1),
  predicted_range: [number, number],
  predicted_tier_7d: string (System 1 tier label),
  viralPotential: string (System 1 tier label),
  qualitative_analysis: {
    pack1: UnifiedGradingResult | null,
    pack2: EditingCoachResult | null,
    pack3: ViralMechanicsResult | ViralMechanicsStub | null,
    packV: VisualRubricResult | null
  },
  components_used: string[],
  recommendations: string[],
  transcription: { source, confidence, text },
  debug: {
    executedComponentCount: number,
    pack1_error?: string,
    pack2_error?: string,
    rawScores: object,
    calibration: object
  },
  timing_ms: number
}
```

Note: The response field is named `predicted_dps` for historical reasons but represents the VPS score. The DB column `predicted_tier_7d` has a CHECK constraint that only accepts old System 2 values — code uses `tierLabelToDbValue()` to map System 1 UI labels to DB-compatible values at write time.

Part 5: Integration Map

```
Upload Test Page (/admin/upload-test)
    │
    ├── POST /api/kai/predict
    │       │
    │       ├── runPredictionPipeline()
    │       │       │
    │       │       ├── KaiOrchestrator
    │       │       │       ├── Phase 1 (parallel): 17+ components
    │       │       │       └── Phase 2 (sequential): Pack V → Pack 1 → Pack 2 → Pack 3
    │       │       │
    │       │       ├── prediction-calibrator.ts (post-prediction adjustments)
    │       │       │
    │       │       ├── Supabase writes:
    │       │       │       ├── prediction_runs (run metadata, final VPS, tier)
    │       │       │       └── run_component_results (per-component results)
    │       │       │
    │       │       └── normalize-component-result.ts (QC flags, lane classification)
    │       │
    │       └── Response to UI
    │
    └── UI renders VPS gauge, Pack panels, pipeline steps

Training Pipeline (automated cron)
    │
    ├── /api/cron/training-pipeline?step=backfill
    ├── /api/cron/training-pipeline?step=collect
    ├── /api/cron/training-pipeline?step=label
    └── /api/cron/training-pipeline?step=evaluate

Pack Health Dashboard (/admin/operations/system-health)
    │
    └── GET /api/admin/operations/system-health
            └── Reads prediction_runs + run_component_results (last 100 runs)

Training Readiness (/admin/operations/training)
    │
    └── Reads training data tables, shows milestone tracker, automation health
```

Part 6: Verification Protocol

6.1 How To Verify the Prediction Pipeline

1. Navigate to /admin/upload-test
2. Paste a transcript (≥10 characters) and select a niche
3. Click "Analyze Video"
4. Verify these results appear:
   - VPS score (0-100) in gauge circle
   - Tier badge below gauge
   - Prediction context panel with calibration details
   - Pack V panel (scores or "no visual data" for text-only)
   - Pack 1 panel (9 attributes + 7 legos, or "Not Available" if no API key)
   - Pack 2 panel (3 suggestions, or "Not Available")
   - Pipeline steps with component count
5. Check Supabase: prediction_runs table should have new row with run_id
6. Check run_component_results: should have one row per executed component

6.2 Pack Verification Checklist

```
PACK VERIFICATION
=================
Date:
Tester:
Environment: dev / staging / production
API Keys Set: GOOGLE_GEMINI_AI_API_KEY=___ OPENAI_API_KEY=___ ANTHROPIC_API_KEY=___

Pack V:
[ ] PASS/FAIL - Shows 5 visual dimension scores for video predictions
[ ] PASS/FAIL - Shows "No visual data" stub for text-only predictions
[ ] PASS/FAIL - Gemini Vision blending active when API key set + video provided
[ ] PASS/FAIL - _meta.source correctly reflects 'real' vs 'mock'
Evidence:

Pack 1:
[ ] PASS/FAIL - Shows 9 attribute scores when GOOGLE_GEMINI_AI_API_KEY set
[ ] PASS/FAIL - Shows "Not Available" with error when API key missing
[ ] PASS/FAIL - Scores VARY between different transcripts (not identical)
[ ] PASS/FAIL - _meta.source = 'real' when using Gemini
Evidence:

Pack 2:
[ ] PASS/FAIL - Shows 3 improvement suggestions when Pack 1 succeeds
[ ] PASS/FAIL - Shows "Not Available" when Pack 1 fails
[ ] PASS/FAIL - Suggestions reference actual weak attributes from Pack 1
[ ] PASS/FAIL - VPS lift estimates are reasonable (not >50 per suggestion)
Evidence:

Pack 3:
[ ] PASS/FAIL - Shows detected viral mechanics with strength bars (when upstream data available)
[ ] PASS/FAIL - Shows "Coming Soon" only when genuinely no data available
[ ] PASS/FAIL - Mechanics reference real signals from other packs
Evidence:
```

6.3 System Integrity Verification

37 integrity tests exist (src/lib/prediction/__tests__/system-integrity.test.ts) and enforce:
- Component registry validity (unique IDs, valid types, reliability 0-1, ≥20 components)
- Path weight sums = 1.0
- Niche uniqueness (hyphenated, difficulty 0.5-1.5)
- Trained niche requirements (must have xgboostNicheKey)
- Tier ordering (descending)
- Tier function coverage (boundary scores)
- LLM ID validation
- Context weight sums
- Pack component existence
- Calibration constants validity
- Pipeline modes

Run: `npm run test:integrity` — all 37 must pass.

Part 7: Current State Summary (Honest Assessment)

| System | Status | Blocking Issue |
|--------|--------|---------------|
| VPS Prediction Pipeline | ✅ Functional | Double calibration compounds reductions. Training data insufficient for accuracy claims. |
| Pack 1 (Unified Grading) | ⚠️ Conditional | Requires GOOGLE_GEMINI_AI_API_KEY. Returns error when missing (no longer returns fake data). |
| Pack 2 (Editing Coach) | ⚠️ Conditional | Depends on Pack 1. Uses rule-based templates, not AI. "AI COACH" label is misleading. |
| Pack V (Visual Rubric) | ✅ Functional | Returns neutral stub for text-only (appropriate — no visuals to analyze). Gemini Vision enhanced for video. |
| Pack 3 (Viral Mechanics) | ⚠️ Connected | Full implementation connected. May show stub when upstream data unavailable. |
| XGBoost ML | ⚠️ Heuristic Active | v5 (heuristic) runs by default. v6 (real ML) overfit on 27 videos. Need 100+ labeled videos for meaningful retrain. |
| Training Pipeline | ✅ Automated | Cron jobs running. Data collection active. Volume is the bottleneck (27 labeled, need 100+). |
| DPS (Post-Publication) | ✅ Implemented, ❌ Not Connected | Full DPS class exists. Used only in outcomes ingestion, not integrated with prediction feedback loop at scale. |
| Pack Health Dashboard | ✅ Active | Read-only. Shows real data from last 100 runs. |
| Viral Studio | ⚠️ Mostly Mock | Gallery = real data. Calibration = real algorithm. Analysis/Lab phases = hardcoded fake data. Dashboard metrics = fabricated. |

7.1 What Actually Works (Verified)

1. The prediction pipeline runs and produces real VPS scores from real component analysis
2. Pack V analyzes real video files using FFmpeg data + Gemini Vision
3. Pack 1 produces real Gemini AI analysis when API key is set
4. Pack 2 generates relevant improvement suggestions based on Pack 1's actual scores
5. All results are stored in Supabase with full component-level traceability
6. The system registry (system-registry.ts) is the single source of truth, enforced by 37 integrity tests
7. Training pipeline automates metric collection and labeling via cron jobs
8. Pack Health Dashboard shows real operational data from recent prediction runs

7.2 What Does NOT Work or Is Misleading (Verified)

1. ViralPredictionDashboard shows fabricated metrics: "92.3% accuracy", "24,891 videos analyzed", "12-module pipeline" — all hardcoded with fake auto-incrementing counters
2. Viral Studio Analysis/Lab phases show hardcoded "predictions" as if computed by AI — no prediction pipeline is called
3. Lab Phase 3 "Viral Score" is a character-counting formula (counts string length, question marks, keywords) presented as AI prediction
4. System Banner "System Accuracy: 91.3%" and "Videos Analyzed: 24,891" with pulsing "Live Analysis Active" — all fabricated
5. XGBoost v5 is a hand-coded heuristic with hardcoded feature weights pretending to be ML
6. Pack 2's "AI COACH" label implies AI but uses pre-written templates
7. Lab Phase 2 retention curve is hardcoded [95%, 78%, 65%, 52%, 41%, 33%] — same for every video

Part 8: Fix Priority Order (From Audit)

Priority 0 — Data Labeling (Ongoing, Automated):
- Training pipeline running. Target: 100 labeled videos (current: 27).
- Retrain XGBoost v6 at 100 labeled videos (feature pipeline fixed, unblocked).
- Per-niche models at 300 labeled videos.
- Component efficacy evaluation at 100+ labeled videos.

Priority 1 — Integrity (Completed — Buckets 1 & 2):
- [x] System registry created (D11) — single source of truth
- [x] 37 integrity tests (D12) — enforce registry as contract
- [x] VPS terminology applied throughout pipeline and UI
- [x] System 2 tiers retired (D4)
- [x] Honest labels (D5) — "Prediction Context" not "Cohort Context"
- [x] Fake pipeline modes removed (D8) — only Standard and Validation
- [x] Component count dynamic from registry (D11)
- [x] XGBoost badge gated on hasTrainedModel (D14)
- [x] Flat niche dropdown, no duplicates (D15)
- [x] Pack 1/2 error messages surfaced in UI
- [x] Pack V enhanced with Gemini Vision (D13)
- [x] XGBoost fake features fixed — reads real component outputs (2026-03-02)
- [x] Pack Health Dashboard built (2026-03-03)

Priority 2 — Connection (Partially Complete):
- [x] Pack 3 barrel export + format fix (Bucket 2)
- [ ] Consolidate double calibration into single pass (D10 — deferred until test coverage exists)
- [ ] Persist calibration profile to pipeline context (profile saved to DB but not passed to prediction)
- [ ] Connect viral studio Analysis/Lab phases to real prediction pipeline

Priority 3 — Enhancement (Future):
- [ ] Pack 2 LLM activation (use existing runEditingCoach() or fix label)
- [ ] Calibration video pool expansion (currently 20 hardcoded, 5 niches)
- [ ] Viral studio dashboard: connect to real system metrics or remove fabricated data
- [ ] Asymmetric prediction range (D7 — deferred until confidence data meaningful)

Priority 4 — Validation (Triggered by Data Milestones):
- [ ] Retrain XGBoost v6 (TRIGGER: 100+ labeled videos)
- [ ] Recalibrate Pack 2 RUBRIC_WEIGHTS with real training data
- [ ] Build component efficacy evaluation / ablation testing (TRIGGER: 100+ labeled videos)
- [ ] Cohort-aware calibration baselines (D3 — TRIGGER: 50+ labeled videos across 3+ niches)

Part 9: Environment Requirements

| Variable | Purpose | Required For |
|----------|---------|-------------|
| NEXT_PUBLIC_SUPABASE_URL | Supabase project URL | All DB operations |
| SUPABASE_SERVICE_KEY | Supabase service key (writes) | Pipeline result storage |
| GOOGLE_GEMINI_AI_API_KEY | Google Gemini AI | Pack 1, Pack 2, Pack V (Gemini Vision), Gemini qualitative analysis |
| GOOGLE_AI_API_KEY | Alternative Google AI key | Checked as fallback for Pack 1/2 |
| OPENAI_API_KEY | OpenAI API | GPT-4o-mini text analysis, TikTok Virality Matrix |
| ANTHROPIC_API_KEY | Anthropic API | Claude qualitative analysis |
| MODEL_VERSION | XGBoost model selection | Set to 'xgb_v6' to use real trained model (default: v5 heuristic) |
| NEXT_PUBLIC_ADMIN_EMAIL | Admin auth email | Google OAuth admin access |

If GOOGLE_GEMINI_AI_API_KEY is missing: Pack 1 returns error (not mock data), Pack 2 fails (depends on Pack 1), Pack V falls back to rule-based only. This is the single most impactful environment variable for prediction quality.

Part 10: Database Schema (Key Tables)

| Table | Purpose | Written By |
|-------|---------|-----------|
| prediction_runs | One row per prediction — VPS score, tier, confidence, timing, mode | runPredictionPipeline.ts |
| run_component_results | One row per component per run — prediction, confidence, features JSONB | runPredictionPipeline.ts |
| video_files | Uploaded video file metadata | /api/kai/predict route |
| scraped_videos | Scraped viral videos for gallery | Apify collection pipeline |
| training_features_packv | Pack V features logged for model retraining | prediction-calibrator.ts |
| vps_evaluation | Spearman evaluation results | spearman-evaluator.ts |
| calibration_profiles | User calibration profiles from signal calibration | calibration-db.ts |
| model_versions | Deployed model version tracking | Manual / training pipeline |

Known DB constraint: `predicted_tier_7d` column has a CHECK constraint in live Supabase allowing only old System 2 values (poor/average/good/excellent/viral). Code maps System 1 labels via `tierLabelToDbValue()` at write time. Migration to update constraint documented but requires individual SQL statements in Supabase editor.

Part 11: Execution Prompts (For Engineers/Agents)

11.1 Bug Fix Protocol (Mandatory — from CLAUDE.md Rule 0)

```
EVERY BUG FIX MUST FOLLOW THIS SEQUENCE:

1. FULL CHAIN READ — Read EVERY file in the affected chain before editing anything
2. DIAGNOSE — Add diagnostic logging, ask user to reproduce, state root cause with evidence
3. STATE THE FIX — "Root cause is X. I will change Y in file Z. This will fix because..."
4. WAIT FOR APPROVAL — Do not edit until user confirms
5. MAKE THE EDIT — Surgical, minimal. No scope creep.
6. PROVIDE TEST STEPS — Exact page, button, input, expected result
7. WAIT FOR CONFIRMATION — Say "Please test and let me know what you see." NEVER say "fixed."
```

11.2 Prediction Pipeline Task Template

```
TASK: {single task}

CHAIN FILES:
- UI: src/app/admin/upload-test/page.tsx
- API: src/app/api/kai/predict/route.ts
- Pipeline: src/lib/prediction/runPredictionPipeline.ts
- Orchestrator: src/lib/orchestration/kai-orchestrator.ts
- Calibrator: src/lib/prediction/prediction-calibrator.ts
- Registry: src/lib/prediction/system-registry.ts
- Pack runners: src/lib/rubric-engine/*.ts

ACCEPTANCE CRITERIA:
- npm run test:integrity passes (37/37)
- npx tsc --noEmit produces no NEW errors
- UI verified on /admin/upload-test
- VPS score still appears in gauge
- Pack panels still render
- No console errors

DO NOT:
- Change system-registry.ts without updating integrity tests
- Hardcode component counts, tier thresholds, or niche lists
- Add features not explicitly requested
- Claim "fixed" without user verification
```

Part 12: Sign-Off Checklist (Production Readiness)

```
PRODUCTION READINESS — Trendzo VPS Prediction System
====================================================

CORE PIPELINE:
[x] /api/kai/predict returns VPS score for transcript input
[x] /api/kai/predict returns VPS score for video input
[x] All component results stored in run_component_results
[x] Pipeline handles component failures gracefully (excluded from aggregation)
[x] Pack errors surfaced in UI (not silently swallowed)
[x] Tier labels from single source (VPS_TIERS in registry)
[x] VPS terminology used throughout (no stale DPS labels in pipeline)

PACK SYSTEM:
[x] Pack V produces real visual analysis for video predictions
[x] Pack 1 produces real Gemini analysis when API key set
[x] Pack 1 returns clear error (not mock data) when API key missing
[x] Pack 2 generates relevant suggestions based on Pack 1 data
[x] Pack 3 connected to pipeline (barrel export + format fix)
[x] Pack source badges (_meta.source) accurately reflect what ran

SYSTEM INTEGRITY:
[x] system-registry.ts is single source of truth (D11)
[x] 37 integrity tests pass (D12)
[x] No hardcoded component counts in UI (uses getActiveComponentCount())
[x] No hardcoded tier thresholds outside registry
[x] Flat niche dropdown from NICHE_REGISTRY (no duplicates)
[x] XGBoost badge gated on hasTrainedModel

TRAINING INFRASTRUCTURE:
[x] Automated training pipeline (cron jobs running)
[x] Auto-labeler tracks labeling_mode
[x] Spearman evaluation scheduled
[ ] 100+ labeled videos (CURRENT: 27 — BLOCKING XGBoost retrain)

KNOWN LIMITATIONS (not blocking, documented):
[ ] Double calibration not yet consolidated (D10)
[ ] XGBoost v5 heuristic active by default (v6 overfit, need more data)
[ ] Pack 2 uses templates not AI (label discrepancy)
[ ] Viral studio Analysis/Lab phases show hardcoded data
[ ] Historical path contributes nothing (all components disabled)
[ ] Calibration profile not passed to prediction pipeline

SIGN-OFF:
Date: 3/3/26
Status: CONDITIONALLY READY — Core pipeline functional. Training data volume is primary blocker for accuracy claims. Viral studio mock data should not be presented as real analysis.
Blocking issues: Data volume (27/100 labeled videos), double calibration consolidation, viral studio honesty
```
