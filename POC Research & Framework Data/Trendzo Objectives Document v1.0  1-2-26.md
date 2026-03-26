#Trendzo (also known as CleanCopy) Objectives Document v1.0
**Date:** December 24, 2025  
**Status:** APPROVED - Ready for Implementation  
**Owner:** Tommy

---

## Executive Summary

CleanCopy predicts TikTok video virality before posting, enabling creators and agencies to optimize content and reliably deliver results. This document defines the measurable objectives that determine product success.

---

## Target Users

| User Type | Primary Need |
|-----------|--------------|
| **Agencies & Brands** (PRIMARY) | "What videos should my clients create and can we reliably deliver results?" |
| **Individual Creators** (SECONDARY) | "What video should I create and will it perform well, or am I wasting my time?" |

---

## Success Vision

When CleanCopy works perfectly, users experience:
- **Unicorn UI/UX** — Intuitive like an iPhone, effortless navigation
- **5 Core Workflows** — From onboarding to publishing to iteration
- **Ultimate Signal** — Users say "Trendzo told me not to post that video, and I'm glad I listened" or "Trendzo said this would go viral, and it did."

---

## Objectives Hierarchy

### TIER 1: FOUNDATION (Must Prove First)

These are prerequisites. Without these proven, nothing else matters.

| ID | Objective | Success Metric | Measurement Method | Current State |
|----|-----------|----------------|-------------------|---------------|
| **OBJ-F1** | Prove prediction accuracy | ±5 DPS accuracy for 80%+ of predictions | Automated comparison: predicted vs actual on 100+ videos | ⚠️ ~35 manual tests, accuracy unverified |
| **OBJ-F2** | Identify what works | Per-component accuracy ranking with data-driven weights | Component isolation testing | ❌ Unknown which components help vs hurt |
| **OBJ-F3** | Close the learning loop | Weekly accuracy trend shows measurable improvement | Automated accuracy tracking dashboard | ❌ Manual process, rarely used |

**Definition of "Foundation Proven":**
- [ ] 100+ predictions with actual outcomes recorded
- [ ] Overall accuracy ≥80% within ±5 DPS
- [ ] Know which 5 components contribute most to accuracy
- [ ] Weekly automated accuracy report generated

---

### TIER 2: CORE PRODUCT (The Engine)

Once foundation is proven, these define what the product delivers.

| ID | Objective | Success Metric | Measurement Method | Current State |
|----|-----------|----------------|-------------------|---------------|
| **OBJ-C1** | Accurate predictions | ±5 DPS, 80%+ of time | Rolling 30-day accuracy calculation | ⚠️ ~35% at ±10 threshold |
| **OBJ-C2** | Fast predictions | <15 seconds end-to-end | Measure processing time per prediction | ⚠️ Currently ~55 seconds |
| **OBJ-C3** | Reliable system | 99%+ component success rate | Track component success/failure rates | ⚠️ Silent failures being fixed |
| **OBJ-C4** | Actionable guidance | User knows exactly what to change | User survey + specificity scoring | ❌ Vague recommendations |

**Definition of "Engine Ready":**
- [ ] Predictions complete in <15 seconds
- [ ] 99%+ uptime with graceful degradation
- [ ] Recommendations include specific, actionable changes

---

### TIER 3: USER EXPERIENCE (The Product)

Once engine works, these define how users experience it.

| ID | Objective | Success Metric | Measurement Method | Current State |
|----|-----------|----------------|-------------------|---------------|
| **OBJ-U1** | Real dashboards | Production UI for all workflows | Feature completeness audit | ⚠️ Multiple pages exist |
| **OBJ-U2** | Workflow 3 (Quick Win) | Zero → post-ready in <15 min | Time-to-completion tracking | ⚠️ Designed, not functioning |
| **OBJ-U3** | Workflow 4 (Analyze Existing) | Upload → prediction + recs in <15s | End-to-end timing | ⚠️ Works but slow (~55s) |
| **OBJ-U4** | User trust | Users post videos scored 70+ | Track: Did they post? Did they follow recs? | ❌ No tracking |
| **OBJ-U5** | User retention | Users return to test next video | Return usage tracking | ❌ No tracking |
| **OBJ-U6** | Templates ("UI within UI") | Users model viral videos via templates | Template usage + success correlation | ⚠️ UI exists, partial functionality |

---

## The 5 Core Workflows

| # | Workflow | Description | Status |
|---|----------|-------------|--------|
| **WF-1** | Full Build → Publish → Iterate | Complete end-to-end content production pipeline | ❌ Not built |
| **WF-2** | Onboarding → Guided Start | "Get me set up fast" flow, routes to WF-3 or WF-1 | ❌ Not built |
| **WF-3** | Quick Win | Fastest path to high-probability post | ⚠️ Designed, not functioning |
| **WF-4** | Analyze Existing | Upload prior video → analysis → recommendations | ⚠️ Partial (upload-test, bulk-download) |
| **WF-5** | Template Library | Browse trending templates → model → create | ⚠️ UI exists (The Studio) |

---

## Key Performance Indicators (KPIs)

| KPI | Target | How to Measure |
|-----|--------|----------------|
| Prediction Accuracy | ±5 DPS, 80%+ | Compare predictions to actual outcomes |
| User Trust | Users post videos Trendzo scored 70+ | Track: Did they post? Did they follow recommendations? |
| Time to Value | Prediction in <15 seconds | Measure processing time |
| System Reliability | 99%+ success rate | Track component success/failure rates |
| Learning Loop | Model accuracy improves weekly | Compare weekly accuracy trends |
| User Retention | Users return to test next video | Track return usage |

---

## Current Gaps (Honest Assessment)

### Gap 1: No Proof of Accuracy
- ~35 manual tests exist (10 upload-test, 25 bulk-download)
- Data storage location unclear
- No automated pipeline: Prediction → Actual Outcome → Model Update
- **Without this, we cannot claim CleanCopy "works"**

### Gap 2: Complexity Without Clarity
- 5 prediction paths, 23+ components
- Unknown which components contribute most to accuracy
- System feels like "throw everything at wall" not "surgical precision"

### Gap 3: Speed
- Current: ~55 seconds per prediction
- Target: <15 seconds
- Gap: 40+ seconds to eliminate

---

## Immediate Priorities (Next 7 Days)

### Priority 1: Find and Consolidate All Test Data
- [ ] Locate where upload-test predictions are stored
- [ ] Locate where bulk-download predictions are stored
- [ ] Consolidate into single accuracy tracking table
- [ ] Calculate current actual accuracy

### Priority 2: Build Automated Accuracy Tracking
- [ ] Auto-record every prediction with timestamp
- [ ] Mechanism to record actual outcome when known
- [ ] Dashboard showing accuracy over time
- [ ] Per-component accuracy breakdown

### Priority 3: Component Isolation Test
- [ ] Run 50+ videos through XGBoost ONLY
- [ ] Run 50+ videos through Qualitative ONLY
- [ ] Run 50+ videos through Pattern ONLY
- [ ] Compare: Which component is most accurate alone?

---

## Success Milestones

| Milestone | Criteria | Target Date |
|-----------|----------|-------------|
| **M1: Baseline Established** | Know actual current accuracy from 100+ tests | Week 1 |
| **M2: Component Ranking** | Know which components help vs hurt accuracy | Week 2 |
| **M3: Foundation Proven** | 80%+ accuracy at ±5 DPS documented | Week 4 |
| **M4: Speed Optimized** | <15 second predictions | Week 6 |
| **M5: User Experience Ready** | Workflow 3 & 4 fully functional | Week 8 |

---

## Appendix A: Component Inventory

Components that need Capability Sheets:

**Quantitative Path:**
- feature-extraction
- xgboost
- ffmpeg
- audio-analyzer
- visual-scene-detector

**Qualitative Path:**
- gpt4
- gemini
- claude (if enabled)

**Pattern Path:**
- thumbnail-analyzer
- 7-legos
- 9-attributes
- 24-styles
- virality-matrix
- pattern-extraction
- hook-scorer

**Historical Path:**
- historical
- niche-keywords
- trend-timing-analyzer
- posting-time-optimizer

---

## Appendix B: Data Requirements

To achieve OBJ-F1 (Prove Accuracy), we need:

| Field | Description | Source |
|-------|-------------|--------|
| video_id | Unique identifier | Auto-generated |
| predicted_dps | System prediction | Kai Orchestrator output |
| prediction_timestamp | When prediction was made | Auto-captured |
| actual_dps | Real performance after posting | Manual entry OR auto-scraped |
| actual_timestamp | When actual was recorded | Auto-captured |
| niche | Content category | User selection |
| account_size | Follower cohort | User selection |
| components_used | Which components contributed | Kai Orchestrator log |
| per_component_scores | Each component's individual prediction | Kai Orchestrator output |

---

## Document History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-24 | Initial objectives defined |

---

*This document serves as the authoritative source for CleanCopy product objectives. All development decisions should trace back to these objectives.*