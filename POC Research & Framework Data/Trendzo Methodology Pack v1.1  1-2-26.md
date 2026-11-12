# Trendzo (aka CleanCopy) Methodology Pack v1.1

**Date:** December 25, 2025  
**Status:** Foundation Complete  
**Version:** 1.1

---

## Executive Summary

CleanCopy is a viral video prediction and analytics platform with two core capabilities:

1. **Viral Potential Score (VPS)** — Pre-posting prediction of how well content will perform
2. **Dynamic Percentile System (DPS)** — Post-posting measurement of actual performance relative to cohort

These serve different user needs and use different methodologies.

---

## Part 1: System Architecture

### 1.1 Two-System Design

| System | When Used | Input | Output | Method |
|--------|-----------|-------|--------|--------|
| Viral Potential Score | Before posting | Video features | 0-100 score | ML prediction |
| Cohort DPS | After posting | Actual metrics | Percentile rank | Formula calculation |

### 1.2 Component Architecture

CleanCopy uses 22 analytical components organized into four paths:

**Quantitative Path (35% weight)**
- FFmpeg Visual Analysis (23 features)
- Feature Extraction (119 text features)
- XGBoost ML Predictor
- Audio Analyzer
- Visual Scene Detector
- Thumbnail Analyzer

**Qualitative Path (25% weight)**
- GPT-4 Analysis
- Gemini Analysis
- Claude Analysis

**Pattern Path (25% weight)**
- Hook Scorer
- 7 Idea Legos
- 9 Attributes Framework
- 24 Video Styles
- Pattern Extraction
- Virality Matrix
- Niche Keywords

**Historical Path (15% weight)**
- Historical Comparison
- Trend Timing Analyzer
- Posting Time Optimizer

### 1.3 Data Flow

```
Video Input
    │
    ├── FFmpeg → 23 visual/audio features
    │
    ├── Whisper → Transcript
    │       │
    │       └── Feature Extraction → 119 text features
    │
    ├── LLM Analysis → GPT-4, Claude, Gemini scores
    │
    └── Pattern Analysis → Framework scores
            │
            ▼
    XGBoost Model (v5)
            │
            ▼
    Viral Potential Score (0-100)
```

---

## Part 2: Viral Potential Score (Pre-Posting Prediction)

### 2.1 Purpose

Predict how well a video will perform BEFORE it's posted, based on content characteristics.

### 2.2 Target Variable

**Engagement-Based Score (0-100)**

The training target is calculated from actual video performance:

```
engagement_rate = (likes + comments + shares + saves) / views

If engagement_rate >= 0.20: score = 80 + (engagement_rate - 0.20) × 100
If engagement_rate >= 0.10: score = 60 + (engagement_rate - 0.10) × 200
If engagement_rate >= 0.05: score = 40 + (engagement_rate - 0.05) × 400
If engagement_rate >= 0.03: score = 30 + (engagement_rate - 0.03) × 500
Else: score = engagement_rate × 1000
```

**Why not Cohort DPS?**  
Cohort DPS measures relative performance against other creators — information that's unknowable before posting. Engagement-based scoring measures content quality, which IS predictable from features.

### 2.3 Feature Set (v5 Model)

**Total: 41 features**

**FFmpeg Features (14):**
- duration_seconds
- resolution_width, resolution_height
- fps
- has_audio, has_music
- avg_volume
- motion_score
- has_faces, face_time_ratio
- scene_change_count
- hook_scene_changes
- visual_complexity
- brightness_avg

**Text Features (7):**
- word_count, char_count
- sentence_count
- avg_word_length, avg_sentence_length
- unique_word_count, unique_word_ratio

**Text Analysis Features (18):**
- question_count, exclamation_count, ellipsis_count
- first_person_count, second_person_count, third_person_count
- first_person_ratio, second_person_ratio
- positive_count, negative_count
- power_word_count, urgency_count, curiosity_count
- social_proof_count
- emotion_ratio
- all_caps_count, hashtag_count, mention_count

**LLM Features (2):**
- gpt4_score (GPT-4 viral potential assessment)
- claude_score (Claude viral potential assessment)

### 2.4 Model Performance (v5)

| Metric | Overall | Personal Finance | Side Hustles |
|--------|---------|------------------|--------------|
| Within ±5 DPS | 43.1% | 50.5% | 35.7% |
| Within ±10 DPS | 66.7% | 74.3% | 59.2% |
| MAE | 8.26 | 7.11 | 8.41 |
| Correlation | — | 0.805 | 0.412 |
| Bias | -0.88 | -0.86 | -1.08 |

### 2.5 Training Data

- **Source:** Supabase scraped_videos + video_visual_analysis
- **Total Videos:** 995
- **Personal Finance:** 505 videos
- **Side Hustles:** 490 videos
- **Split:** 80% train / 20% test

### 2.6 What Was Removed (Data Contamination)

These features were REMOVED because they're unavailable before posting:
- views_count
- likes_count
- comments_count
- shares_count
- saves_count
- engagement_rate

Previous models used views_count (73.9% feature importance), which caused systematic +20 DPS over-prediction.

---

## Part 3: Dynamic Percentile System (Post-Posting Measurement)

### 3.1 Purpose

Measure how well a video performed AFTER posting, relative to similar creators.

### 3.2 Formula

```
Viral_Score = (View_Count / Cohort_Median) × Platform_Weight × Decay_Factor
```

**Where:**
- `Cohort_Median` = median views for accounts with ±20% follower count
- `Platform_Weight` = TikTok: 1.0, Instagram: 0.85, YouTube: 0.7
- `Decay_Factor` = e^(-λt), where λ = platform decay rate and t = hours since upload

**Platform Decay Rates (λ):**
- TikTok: 0.5 (steep decay)
- Instagram: 0.3 (moderate decay)
- YouTube: 0.1 (gradual decay)

### 3.3 Cohort Definition

Cohorts are defined by ±20% follower count:
- 10K follower account → cohort: 8K-12K accounts
- 50K follower account → cohort: 40K-60K accounts
- 1M follower account → cohort: 800K-1.2M accounts

### 3.4 DPS Calculation

1. Calculate Viral_Score for the video
2. Calculate Viral_Score for all videos in the cohort
3. Rank the video's Viral_Score within the cohort
4. DPS = (rank / cohort_size) × 100

### 3.5 Classification Thresholds

| Classification | DPS Range | Meaning |
|----------------|-----------|---------|
| Mega-viral | ≥ 99.9 | Top 0.1% of cohort |
| Hyper-viral | ≥ 99 | Top 1% of cohort |
| Viral | ≥ 95 | Top 5% of cohort |
| Above Average | ≥ 75 | Top 25% of cohort |
| Average | 25-75 | Middle 50% |
| Below Average | < 25 | Bottom 25% |

### 3.6 Why DPS Can't Be Predicted

Cohort DPS depends on:
1. Other creators' performance (unknown before posting)
2. Cohort median at time of posting (varies)
3. Timing factors (what else is trending)

These factors are external to the video's content. A perfect video could rank low if other creators also posted great content.

---

## Part 4: The Virality Indicator (Core Prediction Component)

### 4.1 Overview

The **Virality Indicator** is the core prediction component that produces the final Viral Potential Score. It's the culmination of all other components — it takes features from FFmpeg, text extraction, and LLM analysis, then runs them through a trained XGBoost model to output a prediction.

### 4.2 Component Details

| Attribute | Value |
|-----------|-------|
| Component ID | `xgboost-virality-ml` |
| Component Number | #27 in Kai Orchestrator |
| Model Version | 5.0.0 |
| Model Type | XGBoost Regressor |
| Total Features | 41 |

### 4.3 Feature Groups (41 Total)

| Group | Count | Features |
|-------|-------|----------|
| FFmpeg | 14 | duration_seconds, fps, hook_scene_changes, motion_score, has_faces, etc. |
| Text Basic | 7 | word_count, char_count, sentence_count, avg_word_length, etc. |
| Component Predictions | 9 | hook_scorer_pred, 7_legos_pred, 9_attributes_pred, etc. |
| Component Confidences | 9 | hook_scorer_conf, 7_legos_conf, 9_attributes_conf, etc. |
| LLM | 2 | gpt4_score, claude_score |

### 4.4 Top Contributing Features

**Side Hustles:**
| Rank | Feature | Importance |
|------|---------|------------|
| 1 | hook_scene_changes | 10.0% |
| 2 | duration_seconds | 9.1% |
| 3 | motion_score | 5.5% |
| 4 | 7_legos_pred | 4.8% |
| 5 | text_hashtag_count | 2.9% |

**Personal Finance:**
| Rank | Feature | Importance |
|------|---------|------------|
| 1 | fps | 14.4% |
| 2 | text_sentence_count | 5.9% |
| 3 | duration_seconds | 5.8% |
| 4 | text_exclamation_count | 4.5% |
| 5 | text_hashtag_count | 3.8% |

### 4.5 Model Performance

| Metric | Side Hustles | Personal Finance |
|--------|--------------|------------------|
| Within ±5 DPS | 35.7% | 50.5% |
| Within ±10 DPS | 59.2% | 74.3% |
| MAE | 8.41 | 7.11 |
| Correlation | 0.412 | 0.805 |
| Bias | -0.94 DPS | -1.05 DPS |

### 4.6 Files

| File | Purpose |
|------|---------|
| `scripts/predict-virality.py` | Python script that loads XGBoost models and runs predictions |
| `src/lib/services/virality-indicator/xgboost-virality-service.ts` | TypeScript service that calls Python script |
| `src/lib/ml/models/side_hustles_virality_model_v5.json` | Trained model for Side Hustles |
| `src/lib/ml/models/personal_finance_virality_model_v5.json` | Trained model for Personal Finance |

### 4.7 Execution Flow

```
Kai Orchestrator receives video
        │
        ▼
Quantitative Path (sequential):
  1. feature-extraction
  2. xgboost (legacy)
  3. ffmpeg ──────────────────────────┐
  4. audio-analyzer                   │
  5. visual-scene-detector            │
  6. thumbnail-analyzer               ▼
  7. xgboost-virality-ml ◄── Uses FFmpeg data
           │
           ├── Extracts text features from transcript
           ├── Computes component predictions (heuristic)
           ├── Uses LLM scores if pre-computed
           │
           ▼
     Calls predict-virality.py (v5)
           │
           ▼
     Returns: DPS prediction + confidence
```

### 4.8 Architecture Note: Parallel Execution Limitation

The Kai Orchestrator runs 4 prediction paths in **parallel**:
- Quantitative (contains Virality Indicator)
- Qualitative (contains GPT-4, Claude, Gemini)
- Pattern-based
- Historical

**Implication:** Real-time LLM scores from the qualitative path are NOT available to the Virality Indicator during live predictions because they run simultaneously.

| Scenario | LLM Scores Available? | Features Used |
|----------|----------------------|---------------|
| Batch processing | ✅ Yes (pre-computed) | 41/41 |
| Real-time prediction | ⚠️ No | 39/41 |

**Workaround:** Component predictions (heuristic) provide similar signal, so real-time predictions remain effective with 39 features.

---

## Part 5: Other Component Details

### 5.1 FFmpeg Analysis

**Purpose:** Extract visual and audio features from video file

**23 Features Extracted:**
1. duration_seconds
2. resolution_width
3. resolution_height
4. fps
5. has_audio (boolean)
6. has_music (boolean)
7. avg_volume
8. speech_rate_wpm
9. motion_score
10. has_faces (boolean)
11. face_time_ratio
12. scene_change_count
13. brightness_avg
14. avg_brightness
15. contrast_ratio
16. saturation_avg
17. visual_complexity
18. quality_score
19. hook_scene_changes
20. hook_has_faces
21. hook_has_text
22. hook_motion_level
23. motion_intensity

### 4.2 Text Feature Extraction

**Purpose:** Analyze transcript for linguistic patterns

**119 Features in 10 Groups:**
- GROUP A: Basic Text Metrics (15)
- GROUP B: Punctuation Analysis (10)
- GROUP C: Pronoun Perspective (8)
- GROUP D: Emotional Power Words (19)
- GROUP E: Viral Pattern Words (15)
- GROUP F: Capitalization Formatting (5)
- GROUP G: Linguistic Complexity (10)
- GROUP H: Dialogue Interaction (5)
- GROUP I: Content Structure Signals (8)
- GROUP J: Timestamp Pacing (4)

### 4.3 LLM Analysis Components

**GPT-4 Analysis:**
- Evaluates hook strength, content quality, viral potential
- Returns score (0-100) and reasoning
- Average score in training: 65.9

**Claude Analysis:**
- Evaluates emotional resonance, shareability, engagement potential
- Returns score (0-100) and reasoning
- Average score in training: 78.4

**Gemini Analysis:**
- Status: API integration needs fix
- Intended: Third perspective for consensus

### 4.4 Pattern Components

**7 Idea Legos:**
- Topic, Angle, Hook Structure, Story Structure
- Visual Format, Key Visuals, Audio

**9 Attributes Framework:**
- Bucket 1 (Getting Attention): TAM Resonance, Shareability, Hook Strength, Format Innovation
- Bucket 2 (Holding Attention): Value Density, Pacing, Curiosity Gaps, Emotional Journey, Clear Payoff

**24 Video Styles:**
- Talking-Head Explainer, Green-Screen Commentary, PIP Screen Walkthrough
- Voiceover + B-Roll, Whiteboard Teach, Top-Down Demo
- And 18 more specialized formats

---

## Part 6: Training Process

### 6.1 Data Pipeline

```
1. Scrape TikTok videos → scraped_videos table
2. Download video files → local storage
3. Run FFmpeg analysis → video_visual_analysis table
4. Run Whisper transcription → transcript_text field
5. Extract text features → 119 features
6. Run LLM analysis → GPT-4, Claude scores
7. Calculate engagement-based score → training target
8. Combine all features → unified training dataset
9. Train XGBoost → niche-specific models
```

### 6.2 Training Configuration

```python
XGBRegressor(
    n_estimators=100,
    max_depth=6,
    learning_rate=0.1,
    subsample=0.8,
    colsample_bytree=0.8,
    random_state=42
)
```

### 6.3 Validation

- 80/20 train/test split
- Stratified by niche
- Metrics: MAE, correlation, % within ±5 DPS, % within ±10 DPS

### 6.4 Model Versioning

| Version | Date | Key Changes | Accuracy (±5 DPS) |
|---------|------|-------------|-------------------|
| v1 | Pre-Dec 2024 | Contaminated with views/likes | ~15% (broken) |
| v2 | Dec 25, 2024 | Removed data contamination | ~20% |
| v3 | Dec 25, 2024 | Added text features | ~37% |
| v4 | Dec 25, 2024 | Added heuristic components | ~35% |
| v5 | Dec 25, 2024 | Added real LLM features | **43.1%** |
| v6 | Dec 25, 2024 | Cohort DPS target (experimental) | 17.1% |

**Production Model:** v5

---

## Part 7: Inference Process

### 7.1 Pre-Posting Prediction Flow

```
User uploads video
        │
        ▼
FFmpeg extracts 23 features
        │
        ▼
Whisper generates transcript
        │
        ▼
Feature extraction (27 text features)
        │
        ▼
LLM analysis (GPT-4 + Claude)
        │
        ▼
XGBoost v5 prediction
        │
        ▼
Viral Potential Score (0-100)
```

### 7.2 Post-Posting Analysis Flow

```
User provides video URL or metrics
        │
        ▼
Fetch actual views, engagement
        │
        ▼
Identify creator follower count
        │
        ▼
Build cohort (±20% followers)
        │
        ▼
Calculate cohort median
        │
        ▼
Apply DPS formula
        │
        ▼
Cohort DPS (percentile rank)
```

---

## Part 8: API Endpoints

### 8.1 Prediction Endpoints

| Endpoint | Purpose | Input | Output |
|----------|---------|-------|--------|
| POST /api/predict | Pre-posting prediction | Video file or URL | VPS score |
| POST /api/analyze | Post-posting analysis | Video metrics | Cohort DPS |
| POST /api/bulk-analyze | Batch analysis | Multiple videos | Array of results |

### 8.2 Component Endpoints

| Endpoint | Purpose |
|----------|---------|
| GET /api/components | List all components |
| POST /api/components/:id/run | Run single component |
| GET /api/components/:id/status | Check component health |

---

## Part 9: Objectives & Success Metrics

### 9.1 Tier 1: Foundation (Current Focus)

| Objective | Target | Current | Status |
|-----------|--------|---------|--------|
| OBJ-F1: Prediction accuracy | ±5 DPS for 80%+ | 43.1% overall, 50.5% PF | ⏳ In Progress |
| OBJ-F2: Per-component accuracy | Track each component | Measured | ✅ Done |
| OBJ-F3: Learning loop | Weekly improvement | Established | ✅ Done |

### 9.2 Tier 2: Core Product

| Objective | Target | Current | Status |
|-----------|--------|---------|--------|
| OBJ-C1: Accurate predictions | ±5 DPS, 80%+ | 43.1% | ⏳ In Progress |
| OBJ-C2: Fast predictions | <15 seconds | ~55 seconds | ❌ Needs Work |
| OBJ-C3: Reliable system | 99%+ success | ~95% | ⏳ In Progress |
| OBJ-C4: Actionable guidance | Specific fixes | Frameworks exist | ✅ Done |

### 9.3 Tier 3: User Experience

| Objective | Target | Status |
|-----------|--------|--------|
| OBJ-U1: Real dashboards | All workflows | Partial |
| OBJ-U2: Quick Win workflow | <15 min to post-ready | Not started |
| OBJ-U3: Analyze workflow | <15 sec | ~55 sec |
| OBJ-U4: User trust tracking | Implement | Not started |
| OBJ-U5: User retention | Implement | Not started |
| OBJ-U6: Templates | UI within UI | Partial |

---

## Part 10: Known Gaps & Roadmap

### 10.1 Accuracy Gap

**Current:** 43.1% within ±5 DPS  
**Target:** 80% within ±5 DPS  
**Gap:** 36.9%

**Path to close:**
| Action | Expected Impact | Status |
|--------|-----------------|--------|
| Fix Gemini integration | +2-5% | Pending |
| Scale to 5,000+ training videos | +5-10% | Pending |
| Hyperparameter tuning | +1-3% | Pending |
| Historical similarity features | +5-10% | Pending |
| Ensemble methods | +3-5% | Pending |

### 10.2 Speed Gap

**Current:** ~55 seconds  
**Target:** <15 seconds

**Bottlenecks:**
- LLM API calls (GPT-4, Claude): ~15-20 seconds each
- FFmpeg processing: ~10-15 seconds
- Feature extraction: ~5 seconds

**Solutions:**
- Parallel LLM calls
- Cached model loading
- Optimized FFmpeg settings

### 10.3 Data Gaps

| Gap | Impact | Resolution |
|-----|--------|------------|
| Gemini not integrated | Missing 3rd LLM perspective | Fix API |
| Only 995 training videos | Limited model generalization | Scale data |
| No historical features | Missing creator context | Implement |

---

## Part 11: Glossary

| Term | Definition |
|------|------------|
| VPS | Viral Potential Score — pre-posting prediction (0-100) |
| DPS | Dynamic Percentile System — post-posting measurement |
| Virality Indicator | Core prediction component (#27) that produces final VPS using XGBoost v5 |
| Cohort | Group of creators with ±20% similar follower count |
| MAE | Mean Absolute Error — average prediction error |
| Engagement Rate | (likes + comments + shares + saves) / views |
| FFmpeg Features | Visual/audio characteristics extracted from video file |
| Text Features | Linguistic patterns extracted from transcript |
| Kai Orchestrator | Central system that coordinates all 22+ components |

---

## Appendix A: File Locations

### Models
- `src/lib/ml/models/side_hustles_virality_model_v5.json`
- `src/lib/ml/models/personal_finance_virality_model_v5.json`

### Virality Indicator (Core Prediction Component)
- `src/lib/services/virality-indicator/xgboost-virality-service.ts`
- `scripts/predict-virality.py`

### Services
- `src/lib/services/feature-extraction/feature-extraction-service.ts`
- `src/lib/orchestration/kai-orchestrator.ts`

### Training Data
- `data/unified_training_dataset_v5_with_llm.json`

### Scripts
- `scripts/train-xgboost-v5-with-llm.py`
- `scripts/generate-llm-features-for-training.ts`
- `scripts/calculate-correct-dps.ts`

---

## Appendix B: Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Dec 25, 2024 | Initial methodology pack |
| 1.1 | Dec 25, 2024 | Added Virality Indicator section (Part 4), architecture notes on parallel execution |

---

*This document serves as the single source of truth for CleanCopy's viral prediction system.*