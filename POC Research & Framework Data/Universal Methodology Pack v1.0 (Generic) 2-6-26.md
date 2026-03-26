Trendzo Methodology Pack v2.0

Date: 3/3/26
Status: Audit-Verified (Buckets 1 & 2 Complete)
Version: 2.0 (Updated from generic template with verified system data)
Source of Truth: system-registry.ts (D11), prediction-audit.md (5 phases), bucket2-summary.md

Executive Summary

This system delivers two complementary capabilities:

VPS (Viral Prediction Score) — Predicts viral performance BEFORE a video is published. Uses multi-component analysis of transcript, video, and metadata to produce a 0-100 score with confidence and recommendations.

DPS (Dynamic Percentile System) — Measures actual viral performance AFTER publication relative to a cohort of similar-sized creators. Uses real view counts, engagement metrics, and z-score statistics to produce a percentile ranking.

TERMINOLOGY (ENFORCED — Decision D4, Bucket 1):
- VPS = the score name (both predicted and actual)
- DPS = the methodology name (the cohort-based percentile system), NOT a score name
- Correct: "predicted VPS vs actual VPS after publication"
- WRONG: "predicted VPS vs actual DPS" — DPS is the system, not the score
- The prediction pipeline produces a VPS. After publication, the Dynamic Percentile System measures actual performance.

Part 1: System Architecture

1.1 Two-System Design

| System | When Used | Input | Output | Method |
|--------|-----------|-------|--------|--------|
| VPS (Pre-Publication Prediction) | Before video is published | Transcript + video file + metadata (niche, goal, account size) | Score (0-100) + confidence (0-1) + range + tier + recommendations | Multi-component weighted aggregation across 4 analysis paths |
| DPS (Post-Publication Measurement) | After video is published | Actual metrics: view count, follower count, engagement (likes/comments/shares), hours since upload | Percentile rank within creator cohort + virality classification | Z-score statistics vs cohort of ±20% follower-count creators |

Design rule: VPS uses only pre-publication information. DPS uses only post-publication metrics. They must remain methodologically separated. The GAP between VPS (predicted) and DPS (actual) is the accuracy metric that drives model retraining.

1.2 Component Architecture

Components are organized into 4 signal paths. Source: system-registry.ts COMPONENT_REGISTRY (22 registered components).

Quantitative Path (15% base weight)
- Feature Extraction Service (152 features) — extracts numerical features from video/transcript
- XGBoost Virality ML Predictor v5 — real trained model via Python subprocess (41 features: FFmpeg(14) + Text(7) + Components(18) + LLM(2))

Qualitative Path (25% base weight)
- Text Analysis (GPT-4o-mini / Heuristic) — uses gpt-4o-mini when OPENAI_API_KEY available, heuristic fallback otherwise
- Gemini Analysis — real Gemini API call, disabled if no GOOGLE_GEMINI_AI_API_KEY
- Claude Analysis — real Claude API call, disabled if no ANTHROPIC_API_KEY

Pattern-Based Path (45% base weight)
- FFmpeg Visual Analysis — real video file analysis (motion, brightness, scenes, duration)
- Visual Scene Detection — scene changes, composition
- Thumbnail Analyzer — visual thumbnail quality
- Audio Analysis Engine — audio characteristics
- 7 Idea Legos Pattern Extraction — detects 7 viral content patterns
- 9 Attributes Scorer — scores on 9 content attributes
- 24 Video Styles Classifier — classifies into 1 of 24 style categories
- TikTok Virality Matrix — uses OpenAI GPT-4 API call
- Pattern Extraction Engine — identifies viral patterns
- Hook Strength Scorer — scores opening hook quality
- Virality Indicator Engine — proprietary 6-factor algorithm, runs locally
- Visual Rubric (Pack V) — rule-based visual analysis augmented with Gemini Vision (D13)
- Unified Grading Rubric (Pack 1) — LLM rubric via Gemini
- Editing Coach (Pack 2) — rule-based coaching from Pack 1 output
- Viral Mechanics (Pack 3) — rule-based signal synthesis

Historical Path (15% base weight)
- NOTE: ALL 4 components in this path are disabled (historical, niche-keywords, trend-timing-analyzer, posting-time-optimizer). Its weight is redistributed to surviving paths via graceful degradation. This is a known architectural gap. The path contributes nothing.

Constraint: Each path produces (a) a VPS prediction (0-100), (b) a confidence (0-1), (c) an explanation trace.

Active component count: 21 (22 registered minus 1 always-disabled niche-keywords). Source: getActiveComponentCount() in system-registry.ts.

Typical run with video + transcript + all API keys: ~18-19 components execute.
Typical run without some API keys or missing inputs: ~14-17 components execute.

1.3 Disabled Components

7 components are registered as disabled with documented reasons (source: DISABLED_COMPONENTS in system-registry.ts):

| ID | Name | Reason |
|----|------|--------|
| xgboost | Old XGBoost (fake) | FAKE heuristic placeholder. Real XGBoost is xgboost-virality-ml. |
| dps-engine | DPS Calculation Engine | Redundant — DPS calculated by orchestrator aggregation. |
| historical | Historical Comparison | Zero variance — just returns niche average VPS, not contributing to accuracy. |
| trend-timing-analyzer | Trend Timing | Content-independent — returns same score regardless of video quality. |
| posting-time-optimizer | Posting Time | Content-independent — recommendation tool, not prediction. |
| python-analysis | Python Enhanced Analysis | PySceneDetect, VADER — deprecated. |
| competitor-benchmark | Competitor Benchmark | Disabled at runtime via smart defaults. |

1.4 Data Flow (Verified from Code)

```
User Input (transcript + video file + niche + goal + account size)
    │
    ├── /api/kai/predict receives form data
    │       │
    │       ├── Video saved to disk, video_files row created in Supabase
    │       ├── prediction_runs row created (status='running', unique run_id)
    │       │
    │       ├── Transcript Resolution:
    │       │     ├── User-provided (if 10+ characters typed/pasted)
    │       │     ├── Whisper auto-transcription (if video file but no transcript)
    │       │     └── None (text-dependent components skip)
    │       │
    │       └── KaiOrchestrator runs 4 paths in parallel
    │               │
    │               ├── Phase 1 (parallel): All upstream components
    │               └── Phase 2 (sequential): Pack V → Pack 1 → Pack 2 → Pack 3
    │
    │   Path Aggregation
    │       │
    │       ├── Within each path: confidence-weighted average of component predictions
    │       ├── Suspicious default values (50, 62, 65, 68, 70) excluded
    │       ├── LLM Consensus Gate: if LLM spread > 10 VPS, LLM weights zeroed
    │       ├── Agreement Check:
    │       │     ├── High agreement (stddev < 5) → simple weighted average
    │       │     ├── Moderate agreement (stddev < 15) → context-aware consensus
    │       │     └── Low agreement (stddev > 15) → disagreement reconciliation
    │       └── Result = one raw VPS number
    │
    │   Calibration (two layers — known double-reduction, see Part 2.6)
    │       │
    │       ├── Orchestrator Calibration:
    │       │     ├── Niche difficulty factor (0.75 to 1.05)
    │       │     ├── Account size factor (+15% small to -32% mega)
    │       │     └── Conservative pull (>80 → -25%, >70 → -20%, >60 → -15%)
    │       │
    │       └── Pipeline Calibration (prediction-calibrator.ts):
    │             ├── Rule 1: No speech → confidence × 0.7
    │             ├── Rule 2: Silent video + low Pack V → VPS cap at 55 (65 for visual-first)
    │             └── Rule 4: High VPS scaling (15%/20%/25% reduction above 60/70/80)
    │
    └── Final Result
          ├── VPS score (0-100)
          ├── Confidence (0-1, clamped to [0.4, 0.95])
          ├── Range (symmetric: VPS ± uncertainty)
          ├── Tier label (from VPS_TIERS in registry)
          ├── Pack 1/2/3/V qualitative analysis results
          ├── Component execution details
          └── Stored to prediction_runs + run_component_results in Supabase
```

Part 2: Pre-Outcome Prediction (VPS)

2.1 Purpose

Predict expected viral performance before a video is published using only inputs available pre-publication: transcript text, video file (visual/audio properties), content metadata (niche, goal), and account size.

2.2 Target Variable

The VPS (Viral Prediction Score) is a composite 0-100 number representing the system's prediction of how likely this video is to perform well on TikTok relative to what's expected for this account size and niche.

VPS is NOT a percentile ranking. It is NOT derived from actual cohort data. It is a weighted average of ~18-19 component predictions, adjusted through calibration multipliers.

The VPS serves as the predicted target. After publication, the actual VPS is computed from real metrics via the Dynamic Percentile System. The gap between predicted VPS and actual VPS is the accuracy metric.

Contamination rule: VPS uses zero post-publication data. Any feature dependent on real views, engagement, or follower growth is excluded from the prediction pipeline. Enforced by contamination-lock.ts.

2.3 VPS Tier Classification (System 1 — Single Source of Truth)

Source: VPS_TIERS in system-registry.ts. All UI, API, and pipeline code imports from this single definition. No hardcoded tier thresholds exist elsewhere (enforced by 37 integrity tests, D12).

| VPS Range | Tier Label | Color |
|-----------|-----------|-------|
| 90+ | Viral Potential | Purple |
| 75-89 | Excellent - Top 10% | Green |
| 60-74 | Good - Top 25% | Green |
| 40-59 | Average | Yellow |
| Below 40 | Needs Work | Red |

System 2 (the old orchestrator getViralPotential with labels like "mega-viral", "viral", "good", "average", "low") was FULLY RETIRED and deleted per Decision D4.

2.4 Feature Set (XGBoost v5)

The XGBoost ML Predictor uses 41 features organized in 4 groups:

FFmpeg features (14): duration_seconds, resolution_width, resolution_height, fps, motion_score, has_faces, face_time_ratio, has_music, avg_volume, brightness_avg, contrast_ratio, saturation_avg, visual_complexity, hook_scene_changes

Text features (7): text_word_count, text_char_count, text_sentence_count, text_avg_word_length, text_question_count, text_exclamation_count, text_hashtag_count

Component prediction features (18): hook_scorer_pred/conf, 7_legos_pred/conf, 9_attributes_pred/conf, 24_styles_pred/conf, niche_keywords_pred/conf, virality_matrix_pred/conf, pattern_extraction_pred/conf, trend_timing_pred/conf, posting_time_pred/conf

LLM features (2): gpt4_score, claude_score

IMPORTANT (Fixed 2026-03-02): The 18 component prediction features were previously computed via keyword-matching heuristics INSIDE the XGBoost component, not pulled from actual component results. This was fixed by moving xgboost-virality-ml to Phase 2 (DEPENDENT_COMPONENTS), where it now reads real prediction/confidence from input.componentResults. XGBoost is unblocked for v6 retrain pending 100+ labeled videos.

Feature Availability Matrix:

| Feature Group | Available Pre-Publication? | Used in VPS? | Notes |
|--------------|--------------------------|-------------|-------|
| FFmpeg (14) | Yes (from uploaded video) | Yes | Requires video file upload |
| Text (7) | Yes (from transcript) | Yes | Requires transcript |
| Component predictions (18) | Yes (from other components) | Yes | Now uses real component outputs (fixed 2026-03-02) |
| LLM scores (2) | Yes (from LLM components) | Yes | Requires API keys |
| View counts, likes, shares | No (post-publication only) | No | Excluded — contamination |
| Follower count, growth rate | No (changes post-publication) | No | Excluded — contamination |

2.5 Model Performance (Honest Assessment)

CRITICAL: The system does not yet have sufficient data to report meaningful accuracy metrics.

| Metric | Value | Context |
|--------|-------|---------|
| Training data | 27 labeled videos (21 train, 6 eval) | Side Hustles niche only |
| Trained niches | 1 (Side Hustles: hasTrainedModel=true, correlation=0.61) | Personal Finance planned but no separate model exists |
| Cross-validation R² | -164,093 | Catastrophically overfit — model memorized training data |
| Eval R² | 0.0 | Model explains none of the variance in unseen data |
| Train R² | 0.983 | Near-perfect on training data = severe overfitting |
| Feature importance | 4 of 41 features matter (text_avg_word_length 42.4%, text_char_count 33.5%, visual_complexity 20.7%, duration_seconds 3.2%) | 37 features at 0% importance due to insufficient training data |

Clearest defensible metric today: Spearman rank correlation (works with small N). Currently 0.61 for Side Hustles niche.

The v6 model cannot generalize beyond its 27-video training set. The XGBoost component still contributes to predictions as one of ~18-19 components, but its individual accuracy is not meaningful at this data volume. The system's prediction quality comes from the ensemble of all components, not from XGBoost alone.

2.6 Training Data

Source: trendzo_side_hustles_training.csv
Total Samples: 27 (21 train, 6 eval)
Segments: Side Hustles niche only — 1 of 20 supported niches
Split: Time-based 80/20
Label definition: Actual VPS calculated from post-publication metrics (views, engagement) via the Dynamic Percentile System
DPS range in training data: 36.0 to 91.29 (mean: 54.7)

Training pipeline automation (implemented 2026-03-01):
- Backfill schedules → collect metrics (Apify) → auto-label (lenient) → Spearman evaluation
- Auto-labeler tracks labeling_mode='auto_cron'
- Cron jobs: backfill 01:00 UTC, collector 00:30/12:30 UTC, labeler 03:30 UTC, eval Sunday 05:00 UTC
- Pipeline status: /api/training/pipeline-status

Retrain milestones (D1):
- 100 labeled videos: Retrain XGBoost v6 (feature pipeline now fixed, unblocked)
- 300 labeled videos: Per-niche models
- 500+ labeled videos: Full multi-niche training

2.7 Calibration Rules (Verified from Code)

All calibration constants defined in CALIBRATION object in system-registry.ts.

Layer 1 — Orchestrator Calibration:

Niche difficulty factors (source: NICHE_REGISTRY + CALIBRATION_ONLY_NICHES):
| Niche | Factor | Effect |
|-------|--------|--------|
| Dropshipping | 0.75 | Hardest — 25% reduction |
| Make Money Online | 0.80 | Very competitive |
| Side Hustles | 0.85 | Competitive |
| Fitness/Weight Loss | 0.90 | Moderately competitive |
| Business/Entrepreneurship | 0.95 | Standard competitive |
| Food/Cooking/DIY | 1.00 | Neutral baseline |
| Pets/Gardening | 1.05 | Easiest — 5% boost |

Account size factors:
| Size | Estimated Followers | Multiplier |
|------|-------------------|-----------|
| Small (0-10K) | ~5,000 | 1.15 (+15%) |
| Growing (10K-50K) | ~35,000 | 1.08 (+8%) |
| Medium (50K-100K) | ~75,000 | 1.00 (baseline) |
| Large (100K-500K) | ~350,000 | 0.88 (-12%) |
| Very Large (500K-1M) | ~750,000 | 0.78 (-22%) |
| Mega (1M+) | ~2,000,000 | 0.68 (-32%) |

High-VPS scaling (CALIBRATION.HIGH_VPS_SCALING in registry):
| Range | Factor | Reduction |
|-------|--------|-----------|
| VPS 60-70 | 0.85 | 15% |
| VPS 70-80 | 0.80 | 20% |
| VPS 80+ | 0.75 | 25% |

Layer 2 — Pipeline Calibration (prediction-calibrator.ts):

Rule 1: No speech detected → confidence × 0.7 (VPS unchanged)
Rule 2: Silent video + no transcript + Pack V visual score < 50 → VPS capped at 55 (or 65 for visual-first styles)
Rule 4: High VPS scaling — same factors as Layer 1 (this is the known double-reduction, see Known Issues)

Visual-first styles (exempt from strict silent cap): meme_edit, satisfying, asmr, cooking_montage, product_demo, timelapse, cinematic, tutorial_silent, art_process, transformation

Confidence bounds: Floor = 0.4, Ceiling = 0.95

KNOWN ISSUE — Double High-VPS Reduction:
The orchestrator and calibrator both apply progressive reductions above VPS 60. These compound multiplicatively. Example: raw prediction of 85 → after orchestrator (×0.75) = 63.75 → after calibrator (×0.85) = 54.2. Total: 36% reduction. This was identified in the audit (OQ-3.1) and is scheduled for consolidation into a single calibration pass (D10 — merge steps 6+7 into single pass; deferred until test coverage exists).

Part 3: Post-Outcome Benchmarking (DPS — Dynamic Percentile System)

3.1 Purpose

Measure actual viral performance after publication relative to a cohort of similar-sized creators on the same platform.

3.2 Implementation Status

The Dynamic Percentile System is FULLY IMPLEMENTED in src/lib/services/viral-prediction/dynamic-percentile-system.ts but is NOT connected to the prediction pipeline. It is used only in:
- src/lib/services/dps-powered-content-generator.ts (content generation)
- src/app/api/outcomes/ingest/route.ts (post-publication outcomes tracking)
- src/lib/services/viral-prediction/main-prediction-engine.ts (separate prediction engine, not the canonical pipeline)

3.3 DPS Methodology (from Framework Document)

The Dynamic Percentile System works by:
1. Cohort grouping — creators compared to others with ±20% follower count
2. Z-score calculation — statistical performance vs cohort median
3. Time decay — platform-specific decay (TikTok decays fastest)
4. Engagement weighting — likes, comments, shares weighted by platform
5. Percentile ranking — where this video falls within its cohort

3.4 DPS Percentile Tiers (System 3)

Source: DPS_PERCENTILE_TIERS in system-registry.ts.

| Percentile | Z-Score | Category |
|-----------|---------|----------|
| 99.9+ | ≥ 3.0 | mega-viral |
| 99+ | ≥ 2.5 | hyper-viral |
| 95+ | ≥ 2.0 | viral |
| 90+ | ≥ 1.5 | trending |
| Below 90 | < 1.5 | normal |

3.5 Why VPS Cannot Perfectly Predict DPS

The VPS (pre-publication prediction) cannot perfectly predict the DPS (post-publication measurement) because DPS depends on:
- External competition (other creators posting at the same time)
- Peer cohort performance (what "normal" looks like for this account size)
- Platform algorithm changes (TikTok's recommendation system evolves)
- Timing and trending topics (unpredictable external factors)
- Actual viewer behavior (engagement patterns differ from content quality signals)

The feedback loop: VPS predicts → video published → actual metrics collected → DPS measured → gap calculated → model retrained → VPS improves. This loop is how the system achieves increasing accuracy over time.

Part 4: Pack System (Supporting Components)

4.1 Pack V — Visual Rubric

Purpose: Score visual quality of video content independently of transcript.
Provider: Rule-based visual analysis augmented with Gemini Vision (D13, implemented 2026-03-02).
Component ID: visual-rubric
API Dependency: GOOGLE_GEMINI_AI_API_KEY (optional — falls back to 100% rule-based)
Requires Transcript: No

5 Visual Dimensions (each scored 1-10):
| # | Dimension | Weight | What It Measures |
|---|-----------|--------|-----------------|
| 1 | Visual Hook | 25% | Thumbnail quality, face detection, text overlay, opening frame quality |
| 2 | Pacing | 20% | Scene frequency (optimal: 1 cut/2-4 sec), motion intensity, FPS, beat alignment |
| 3 | Pattern Interrupts | 20% | Scene transitions, visual variety, color diversity |
| 4 | Visual Clarity | 15% | Resolution, brightness, contrast, portrait mode, composition |
| 5 | Style Fit | 20% | Detected style + niche compatibility, music presence, audio-visual sync |

When Gemini Vision is available: Blends rule-based (40%) + Gemini Vision (60%) per dimension. Extracts 5 key frames (first, 25%, 50%, 75%, last) and sends to Gemini for visual analysis.
When no API key or no video: Falls back to 100% rule-based. For text-only predictions (no video file), returns stub with all scores = 5/10 (neutral, no signal).
Overall score: Weighted average of 5 sub-scores × 10 (scale: 0-100).

4.2 Pack 1 — Unified Grading Rubric

Purpose: AI-powered content analysis scoring 9 quality attributes and 7 structural elements.
Provider: Google Gemini (gemini-2.5-flash) via @google/genai SDK.
Component ID: unified-grading
API Dependency: GOOGLE_GEMINI_AI_API_KEY
Requires Transcript: Yes (minimum 10 characters)

9 Attributes Scored (each 1-10):
| # | Attribute | What It Measures | Weight in Pack 2 |
|---|-----------|-----------------|-----------------|
| 1 | tam_resonance | How well content matches target audience | 11% |
| 2 | shareability | Likelihood viewers share the video | 12% |
| 3 | value_density | Value delivered per second of video | 10% |
| 4 | emotional_journey | Strength of emotional arc | 8% |
| 5 | hook_strength | Power of the opening | 18% |
| 6 | format_innovation | Creative use of video format | 5% |
| 7 | pacing_rhythm | Flow and timing of content | 9% |
| 8 | curiosity_gaps | Open loops that keep viewer watching | 14% |
| 9 | clear_payoff | Satisfying conclusion/takeaway | 8% |

7 Idea Legos (boolean checks):
| # | Lego | What It Checks |
|---|------|---------------|
| 1 | lego_1 | Clear topic identified |
| 2 | lego_2 | Relevant to target audience |
| 3 | lego_3 | Unique angle presented |
| 4 | lego_4 | Intriguing hook present |
| 5 | lego_5 | Story structure exists |
| 6 | lego_6 | Visual format matches content |
| 7 | lego_7 | Call-to-action present |

When API key is set: Sends transcript to Gemini with structured JSON rubric prompt. Validates response against Zod schema. Retries up to 3 times on invalid JSON. Returns _meta.source = 'real'.
When API key is missing: Returns graceful failure with clear error message "Pack 1 requires GOOGLE_GEMINI_AI_API_KEY" (updated from hardcoded mock per D1/audit fixes). UI shows "Pack 1 Analysis Not Available."

VPS contribution: Average of 9 attribute scores × 10 → fed as one component's prediction into the pattern_based path.

4.3 Pack 2 — Editing Coach

Purpose: Generate top 3 improvement suggestions based on Pack 1's analysis.
Provider: Rule-based templates (NOT LLM — the AI-powered version exists in code but is not activated).
Component ID: editing-coach
API Dependency: GOOGLE_GEMINI_AI_API_KEY (for Pack 1 dependency)
Requires Transcript: Yes (via Pack 1 dependency)
Depends On: Pack 1 (unified-grading)

How it works:
1. Sorts Pack 1's 9 attribute scores from lowest to highest
2. For the 3 lowest-scoring attributes, looks up hardcoded improvement templates
3. Calculates estimated VPS lift: (10 - currentScore) × weight × 100 × 0.5 × 0.75
4. Returns _meta.source = 'real', _meta.provider = 'rule-based'

VPS contribution: None. Pack 2 is purely advisory. Its predicted_after_estimate is stored but not used in the VPS calculation.

Known limitation: The "AI COACH" label in the UI is technically misleading since Pack 2 uses pre-written suggestion templates, not AI. The LLM-powered version (runEditingCoach() calling Gemini) exists as dead code.

4.4 Pack 3 — Viral Mechanics

Purpose: Synthesize signals from all other packs and components to identify specific viral mechanics at play.
Provider: Rule-based signal aggregation (no LLM calls).
Component ID: viral-mechanics
Requires Transcript: No
Depends On: Pack 1

Status: Registered and functioning. A full 580-line implementation exists that detects 9 viral mechanics:
| # | Mechanic | Signals Used |
|---|----------|-------------|
| 1 | Visual Hook | Pack V visual scores, hook-scorer |
| 2 | Curiosity Gap | Pack 1 hook analysis, hook-scorer, 7-legos |
| 3 | Style-Platform Fit | 24-styles, niche |
| 4 | Optimal Pacing | Pack 1 pacing, Pack V visual pacing |
| 5 | Audio-Visual Sync | audio-analyzer, Pack V audio signals |
| 6 | Trend Alignment | trend-timing, historical-analyzer, virality-matrix |
| 7 | Pattern Interrupt | Pack V novelty, Pack 1 novelty |
| 8 | Emotional Trigger | Pack 1 emotional attributes, 9-attributes |
| 9 | Timing Advantage | posting-optimizer |

Pack 3 was previously disconnected (barrel export missing + pipeline always using stub). Fixed as part of Bucket 2 fixes — barrel export added, format mismatch resolved, pipeline extraction implemented.

VPS contribution: None directly — Pack 3 is the "why it works" explainer, not a predictor.

Part 5: Pack Execution Truth Table

This table shows actual runtime behavior under different conditions (verified from code):

| Condition | Pack V | Pack 1 | Pack 2 | Pack 3 |
|-----------|--------|--------|--------|--------|
| API key + transcript + video | REAL (rule-based + Gemini Vision) | REAL (Gemini LLM) | REAL (rule-based templates) | REAL (signal synthesis) |
| API key + transcript only | STUB (all 5s, no visual data) | REAL (Gemini LLM) | REAL (rule-based templates) | LIMITED (no visual signals) |
| No API key + transcript + video | REAL (rule-based only, no Vision) | FAIL (error message shown) | FAIL (needs Pack 1) | LIMITED (no Pack 1 signals) |
| No API key + transcript only | STUB (all 5s) | FAIL (error message shown) | FAIL (needs Pack 1) | LIMITED (no Pack 1/visual signals) |
| No transcript (video only) | REAL (rule-based + Gemini Vision) | FAIL (min 10 chars required) | FAIL (needs Pack 1) | LIMITED (no Pack 1 signals) |

Part 6: Training Process

6.1 Data Pipeline (Automated — Implemented 2026-03-01)

```
Backfill Schedules (01:00 UTC daily)
    → Collect Metrics via Apify (00:30/12:30 UTC)
        → Auto-Label with lenient rules (03:30 UTC)
            → Spearman Evaluation (Sunday 05:00 UTC)
                → Version & Retrain (manual, at data milestones)
```

Key files:
- schedule-backfill.ts — schedules collection of post-publication metrics
- auto-labeler.ts — automatic labeling with labeling_mode='auto_cron'
- spearman-evaluator.ts — computes Spearman rank correlation for accuracy assessment
- metric-scheduler.ts — cron orchestration with anchorTime for backfilling

6.2 Training Configuration

| Parameter | Value |
|-----------|-------|
| Model | XGBoost (gradient boosted trees) |
| Features | 41 (14 FFmpeg + 7 text + 18 component + 2 LLM) |
| Active model | v5 (heuristic weights, pending retrain to v6 at 100+ labeled videos) |
| Training split | Time-based 80/20 |
| Evaluation metric | Spearman rank correlation |
| Python subprocess | scripts/predict-xgboost.py |
| Model file | .json model + .pkl scaler |

6.3 Current Limitations (Honest Assessment)

- Only 27 labeled videos exist (all Side Hustles niche, 1 of 20 niches)
- v6 model is catastrophically overfit (R² = -164,093 on cross-validation)
- Only 4 of 41 features have non-zero importance at current data volume
- Per-niche models require 300+ labeled videos (currently at 27 total)
- Training pipeline is automated but data volume is the bottleneck
- Retrain milestones: 100 (v6 retrain) → 300 (per-niche) → 500+ (full multi-niche)

6.4 Versioning

| Version | Date | Change | Result |
|---------|------|--------|--------|
| v5-simplified | Pre-audit | Heuristic fallback with 14 hand-tuned feature weights | Active by default — not real ML |
| v6 | Pre-audit | Real trained XGBoost on 27 Side Hustles videos | Available but not active by default (requires MODEL_VERSION=xgb_v6). Overfit. |
| v6 (planned) | Post 100 labeled videos | Retrain with fixed feature pipeline (real component outputs, not keyword heuristics) | Unblocked on pipeline side (2026-03-02 fix). Blocked on data volume. |

Part 7: Niche System

Source: NICHE_REGISTRY in system-registry.ts. 20 niches, flat list, hyphenated canonical keys.

| Key | Label | Difficulty Factor | Trained Model? | XGBoost Correlation |
|-----|-------|------------------|---------------|-------------------|
| side-hustles | Side Hustles/Making Money Online | 0.85 | Yes | 0.61 |
| personal-finance | Personal Finance/Investing | 0.90 | No | — |
| fitness | Fitness/Weight Loss | 0.90 | No | — |
| business | Business/Entrepreneurship | 0.95 | No | — |
| food-nutrition | Food/Nutrition Comparisons | 1.00 | No | — |
| beauty | Beauty/Skincare | 0.95 | No | — |
| real-estate | Real Estate/Property | 0.95 | No | — |
| self-improvement | Self-Improvement/Productivity | 0.92 | No | — |
| dating | Dating/Relationships | 0.95 | No | — |
| education | Education/Study Tips | 0.95 | No | — |
| career | Career/Job Advice | 0.95 | No | — |
| parenting | Parenting/Family | 1.00 | No | — |
| tech | Tech Reviews/Tutorials | 0.92 | No | — |
| fashion | Fashion/Style | 0.95 | No | — |
| health | Health/Medical Education | 0.95 | No | — |
| cooking | Cooking/Recipes | 1.00 | No | — |
| psychology | Psychology/Mental Health | 0.95 | No | — |
| travel | Travel/Lifestyle | 0.95 | No | — |
| diy | DIY/Home Improvement | 1.00 | No | — |
| language | Language Learning | 0.95 | No | — |

Key format: Canonical keys use hyphens (side-hustles). XGBoost Python service gets underscore format via xgboostNicheKey field. UI shows trained niches with emoji prefix.

When a non-trained niche is selected: XGBoost still runs but force-maps to the side_hustles model as default. The XGBoost badge is hidden for non-trained niches (D14).

Part 8: API Endpoints (Actual)

8.1 Prediction Endpoints

| Endpoint | Method | Purpose | Status |
|----------|--------|---------|--------|
| /api/kai/predict | POST | Run full prediction pipeline (canonical entry point) | Active |
| /api/cron/training-pipeline?step=all | GET | Run automated training pipeline (backfill/collect/label/eval) | Active |
| /api/training/pipeline-status | GET | Check training pipeline automation status | Active |

8.2 Supporting Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| /api/outcomes/ingest | POST | Ingest post-publication metrics for DPS calculation |
| /api/admin/operations/system-health | GET | Pack Health Dashboard — component status, API key status, pack source distribution |

Part 9: Pipeline Modes

Source: PIPELINE_MODES in system-registry.ts = ['standard', 'validation']

| Mode | Behavior |
|------|----------|
| standard | Default. All components run normally. LLMs use default temperature. |
| validation | Sets isDeterministic=true. Forces LLM temperature → 0, A/B variant → deterministic hash. Enables "Exclude LLMs from VPS" checkbox. Purpose: repeatable predictions for QC testing. |

"Fast" and "Admin" modes were removed per Decision D8 — they had no implementation (identical to Standard).

Part 10: LLM Consensus Gate

Source: LLM_COMPONENT_IDS, LLM_SPREAD_THRESHOLD, LLM_CONSENSUS_WEIGHT_CAP in system-registry.ts.

LLM component IDs: gpt4, gemini, claude, unified-grading, editing-coach, 9-attributes, 7-legos

If the spread (max LLM prediction - min LLM prediction) exceeds 10 VPS → ALL LLM weights are zeroed (predictions excluded from VPS calculation but still run for qualitative analysis).

If spread is within threshold → LLM component weights capped at 0.15 each.

Part 11: Confidence Calculation

Confidence is assembled through 5 layers:
1. Component-level: Each component returns its own confidence (0-1). Heuristic components typically hardcode 0.7-0.8. LLM components set based on model reliability.
2. Path-level: Weighted average of component confidences in that path.
3. Synthesis-level: If paths agree well, confidence gets +0.1 boost (capped at 0.98).
4. Orchestrator calibration: Competitive niche (factor < 0.9) → -0.05. No video file → -0.1. Clamped to [0.4, 0.95].
5. Pipeline calibration: No speech detected → ×0.7.

Current limitation: Confidence is NOT displayed prominently to users. It only affects range width. The "Grader Confidence" in Pack 1 is that pack's own confidence, not overall prediction confidence. Subtle opacity encoding added to VPS gauge per D6 (lower confidence = more muted gauge).

Part 12: Known Gaps & Honest Limitations

1. Training data volume: 27 labeled videos across 1 niche. Need 100+ to retrain, 300+ for per-niche models.
2. Historical path dead: 15% weight path has all components disabled. Weight redistributes but no historical signal exists.
3. Double calibration: Two layers independently reduce high predictions, compounding up to 36% reduction.
4. XGBoost model quality: v5 is a heuristic pretending to be ML. v6 is overfit on 27 videos.
5. Component variability: 14-17 components execute depending on inputs and API keys (not deterministic).
6. Confidence underutilized: Computed through 5 layers but only affects range width.
7. DPS not connected to pipeline: The Dynamic Percentile System is fully implemented but only used for post-publication measurement, not integrated into the prediction feedback loop at scale.
8. Pack 2 not AI-powered: Uses rule-based templates despite "AI COACH" label.

Part 13: Glossary

| Term | Definition |
|------|-----------|
| VPS | Viral Prediction Score — the 0-100 prediction produced by the pipeline before publication |
| DPS | Dynamic Percentile System — the cohort-based percentile methodology for post-publication measurement |
| Pack | A higher-order component that provides structured qualitative analysis (Pack 1, 2, 3, V) |
| Path | One of 4 analysis paths (quantitative, qualitative, pattern-based, historical) that components are organized into |
| Niche | Content category (e.g., side-hustles, fitness) with associated difficulty factor |
| Confidence | 0-1 value representing system certainty in its prediction |
| Tier | VPS classification label (Viral Potential, Excellent, Good, Average, Needs Work) |
| Component | An individual analysis module that produces a VPS prediction and confidence |
| Calibration | Post-prediction adjustment for niche difficulty, account size, and over-prediction correction |
| Contamination | Use of post-publication data in pre-publication prediction (strictly forbidden) |
| Spearman | Spearman rank correlation — primary accuracy metric (works with small N) |
| System Registry | system-registry.ts — single source of truth for all components, packs, tiers, niches, paths, and constants (D11) |
