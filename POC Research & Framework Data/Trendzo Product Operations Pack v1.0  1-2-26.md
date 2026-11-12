# Trendzo (aka CleanCopy) Product Operations Pack v1.0

**Date:** December 28, 2025  
**Companion To:** CleanCopy Methodology Pack v1.1  
**Purpose:** Document the PRODUCT (what Methodology Pack doesn't cover)

---

## How These Two Documents Work Together

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CLEANCOPY DOCUMENTATION                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   METHODOLOGY PACK (The Engine)    │    PRODUCT OPS PACK (The Car)          │
│   ────────────────────────────────  │    ──────────────────────────────      │
│                                     │                                        │
│   • How DPS/VPS calculations work   │    • What the product IS               │
│   • What components exist           │    • What users can DO                 │
│   • Training process                │    • What workflows exist              │
│   • API structure                   │    • How users interact                │
│   • Feature importance              │    • UI/UX specifications              │
│   • Model architecture              │    • Verification & testing            │
│                                     │                                        │
│   USE FOR:                          │    USE FOR:                            │
│   "Why is prediction wrong?"        │    "Why doesn't this workflow work?"   │
│   "How do I train a new niche?"     │    "What should happen when I click X?"│
│   "What features matter?"           │    "Is this feature production ready?" │
│                                     │                                        │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

# PART 1: PRODUCT DEFINITION

## 1.1 What CleanCopy IS

**One-Line Description:**  
CleanCopy predicts viral video performance BEFORE posting and helps creators generate viral content using proven patterns.

**Core Value Propositions:**

| Value Prop | Description | Status |
|------------|-------------|--------|
| Predict Virality | Upload video/script → Get DPS prediction | ⚠️ Works for average content, not extremes |
| Pattern Intelligence | Analyze viral videos → Extract winning patterns | ⚠️ Partially implemented |
| Script Generation | Select template → Generate viral script | ❌ Not working |
| Content Creation | Script → Video with AI assistance | ❌ Not working |

## 1.2 User Personas

**Primary User: Content Creator**
- Creates TikTok/short-form video content
- Wants to know if content will perform before posting
- Wants to learn from viral content patterns
- Wants to generate scripts based on proven formulas

**Secondary User: Agency/Manager**
- Manages multiple creators
- Needs batch analysis
- Wants consistent content quality
- Needs reporting and analytics

## 1.3 Product Boundaries

**CleanCopy DOES:**
- Predict viral potential (DPS/VPS scores)
- Analyze video content (transcript, visual, audio)
- Extract patterns from viral content
- Generate scripts based on templates
- Provide recommendations for improvement

**CleanCopy DOES NOT (currently):**
- Edit videos
- Post to social platforms
- Manage social accounts
- Provide real-time analytics
- A/B test live content

---

# PART 2: PAGE INVENTORY

## 2.1 All Pages

| Page | URL | Purpose | Status |
|------|-----|---------|--------|
| Studio | /admin/studio | Main workspace, entry point | ✅ Renders |
| Upload Test | /admin/upload-test | Test prediction system | ⚠️ Works, slow |
| Bulk Download | /admin/bulk-download | Batch processing | ⚠️ Partial |
| Algorithm IQ | /admin/algorithm-iq | System diagnostics | ✅ Works |
| Template Library | /admin/studio (tab) | Browse viral templates | ⚠️ UI works, script gen broken |
| Instant Analysis | /admin/studio (tab) | Quick video analysis | ⚠️ Same as Upload Test |
| Creator | /admin/studio (tab) | 6-phase workflow | ❌ Not functional |
| The Armory | /admin/studio (tab) | Tools/resources | ❓ Unknown |
| The Laboratory | /admin/studio (tab) | Experiments | ❓ Unknown |
| Accuracy Validation | /admin/studio (tab) | Test accuracy | ❓ Unknown |

## 2.2 Page Dependencies

```
/admin/studio (Entry Point)
├── Template Library Tab
│   └── Requires: Template data, Script generation API
├── Instant Analysis Tab
│   └── Requires: /api/kai/predict, Video upload
├── Creator Tab (6-Phase)
│   └── Requires: All systems (prediction, patterns, scripts)
├── The Armory Tab
│   └── Requires: [Unknown]
├── The Laboratory Tab
│   └── Requires: [Unknown]
└── Accuracy Validation Tab
    └── Requires: Prediction tracking data
```

---

# PART 3: WORKFLOW SPECIFICATIONS

## 3.1 Workflow Overview

| # | Workflow | Steps | Primary Value | Status |
|---|----------|-------|---------------|--------|
| 1 | Viral Content Creator | 6 phases | Full creation pipeline | ❌ Broken |
| 2 | Entry Point | 1 screen | Navigation/onboarding | ✅ Works |
| 3 | Quick Win | 3 steps | Fast script generation | ❌ Broken |
| 4 | Instant Analysis | 4 steps | Quick prediction | ⚠️ Partial |
| 5 | Template Library | 3 steps | Pattern-based creation | ❌ Broken |

---

## 3.2 WORKFLOW 4: Instant Analysis (PRIORITY)

**Purpose:** User uploads video → Gets prediction → Sees recommendations

**Why Priority:** This is your DEMO workflow. It must work perfectly.

### Step-by-Step Specification

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 1: UPLOAD                                                              │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Click upload button, select video file                     │
│ Accepted Input:  MP4 file (max 100MB), OR paste transcript                  │
│ System Action:   Store file temporarily, extract metadata                   │
│ Success State:   File appears in upload area, "Configure" section enabled   │
│ Error States:    File too large, wrong format, upload failed                │
│ API Called:      None (client-side only)                                    │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 2: CONFIGURE                                                           │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Select niche, account size, goal                           │
│ Required Fields: Niche (dropdown), Account Size (dropdown)                  │
│ Optional Fields: Goal, Transcript (if not in video)                         │
│ System Action:   Validate inputs, enable prediction button                  │
│ Success State:   All required fields filled, "Get Prediction" enabled       │
│ Error States:    Missing required field                                     │
│ API Called:      None (client-side only)                                    │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 3: PREDICT                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Click "Get Prediction" button                              │
│ System Action:   Call prediction API, show loading state                    │
│ API Called:      POST /api/kai/predict                                      │
│ API Input:       { videoFile OR transcript, niche, accountSize, goal }      │
│ API Output:      { predicted_dps, confidence, range, recommendations, ... } │
│ Success State:   Loading completes, results section appears                 │
│ Error States:    API timeout, server error, invalid response                │
│ Target Time:     < 25 seconds (with video), < 15 seconds (transcript only)  │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 4: VIEW RESULTS                                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Sees:                                                                  │
│   • Predicted DPS (large, prominent)                                        │
│   • Confidence percentage                                                   │
│   • Range (min - max)                                                       │
│   • Viral Potential label (poor/average/good/excellent)                     │
│   • XGBoost v5 prediction details                                           │
│   • Component scores breakdown                                              │
│   • Specific, actionable recommendations                                    │
│                                                                             │
│ Recommendation Format:                                                      │
│   ✅ GOOD: "Add a question in the first 3 seconds. Example: 'Did you        │
│            know that 90% of people...' This increases hook retention by    │
│            23% based on your niche data."                                   │
│   ❌ BAD:  "Improve your hook."                                             │
│                                                                             │
│ Data Persistence:                                                           │
│   • Save to prediction_events table                                         │
│   • Save to prediction_tracking table                                       │
│   • Enable later comparison to actual performance                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Acceptance Criteria

```
WORKFLOW 4 ACCEPTANCE CRITERIA
==============================

[ ] User can upload MP4 file up to 100MB
[ ] User can paste transcript as alternative to video
[ ] User can select niche from dropdown (personal_finance, side_hustles)
[ ] User can select account size from dropdown
[ ] "Get Prediction" button is disabled until required fields filled
[ ] Clicking "Get Prediction" shows loading indicator
[ ] Prediction completes in < 25 seconds (with video)
[ ] Results display predicted DPS prominently
[ ] Results show confidence and range
[ ] Results show XGBoost v5 model was used
[ ] Results show at least 3 specific recommendations
[ ] Recommendations are actionable (not vague)
[ ] Prediction is saved to database
[ ] No console errors during entire flow
[ ] No silent failures (all errors shown to user)
```

---

## 3.3 WORKFLOW 3: Quick Win

**Purpose:** User picks viral template → Generates script → Creates video

### Step-by-Step Specification

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 1: PICK TEMPLATE                                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Browse templates, select one                               │
│ Display Shows:   Grid of viral video templates with:                        │
│                  - Thumbnail                                                │
│                  - View count                                               │
│                  - DPS score                                                │
│                  - Niche tag                                                │
│                  - Creator name                                             │
│ Filter Options:  By niche, by DPS range, by recency                         │
│ System Action:   Load templates from database, enable selection             │
│ Success State:   User clicks template, Step 2 opens                         │
│ Data Source:     scraped_videos table (high DPS videos)                     │
│ API Called:      GET /api/templates (or query scraped_videos)               │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 2: GENERATE SCRIPT                                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Configure options, click "Generate Viral Script"           │
│ Configuration:                                                              │
│   - Platform (TikTok, Instagram, YouTube)                                   │
│   - Video length (15s, 30s, 60s)                                            │
│   - Tone (optional)                                                         │
│   - Custom instructions (optional)                                          │
│ Context Shown:   Selected template's patterns (hook, structure, CTA)        │
│                                                                             │
│ System Action:                                                              │
│   1. Retrieve template's transcript and patterns                            │
│   2. Extract 7 Idea Legos from template                                     │
│   3. Call LLM to generate new script using patterns                         │
│   4. Return structured script with sections                                 │
│                                                                             │
│ API Called:      POST /api/scripts/generate                                 │
│ API Input:       { templateId, platform, length, tone, instructions }       │
│ API Output:      { hook, body, cta, fullScript, predictedDps }              │
│                                                                             │
│ Success State:   Generated script appears with sections labeled             │
│ Error States:    Generation failed, API timeout, no template found          │
│ Target Time:     < 10 seconds                                               │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 3: CREATE VIDEO (Future - Not MVP)                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Use script to create video                                 │
│ MVP Scope:       Copy script button, instructions for recording             │
│ Future Scope:    AI avatar generation, limited editor                       │
│ Current Status:  NOT IN MVP - skip for now                                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Acceptance Criteria

```
WORKFLOW 3 ACCEPTANCE CRITERIA
==============================

STEP 1 - PICK TEMPLATE:
[ ] Templates load from database
[ ] Each template shows thumbnail, views, DPS
[ ] User can filter by niche
[ ] Clicking template opens Step 2

STEP 2 - GENERATE SCRIPT:
[ ] Selected template context is shown
[ ] Platform dropdown works
[ ] Video length dropdown works
[ ] "Generate Viral Script" button works
[ ] Script generates in < 10 seconds
[ ] Generated script has clear sections (hook, body, CTA)
[ ] Generated script is based on template patterns (not generic)
[ ] User can copy script to clipboard

STEP 3 - CREATE VIDEO:
[ ] MVP: Show instructions for recording
[ ] MVP: Provide copy button for script
[ ] Future: AI avatar integration
```

---

## 3.4 WORKFLOW 5: Template Library

**Purpose:** User browses viral videos → Selects template → Generates script

### Step-by-Step Specification

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 1: BROWSE TEMPLATES                                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     View template library, browse/search                       │
│ Display Shows:   Grid/list of viral videos with:                            │
│                  - Video player (click to play)                             │
│                  - Title/description                                        │
│                  - View count, likes, comments                              │
│                  - DPS score (prominent)                                    │
│                  - Niche tag                                                │
│                  - "Create with this template" button                       │
│ Filter Options:  By niche, DPS range, date range, creator                   │
│ Search:          By keyword in transcript                                   │
│ Sort Options:    By DPS (high to low), by views, by date                    │
│ Data Source:     scraped_videos table                                       │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 2: SELECT TEMPLATE                                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│ User Action:     Click "Create with this template"                          │
│ System Action:   Open script generator modal with template context          │
│ Modal Shows:     Template preview, extracted patterns, generation options   │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│ STEP 3: GENERATE SCRIPT                                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│ [Same as Workflow 3, Step 2]                                                │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3.5 WORKFLOW 1: Viral Content Creator (6-Phase)

**Purpose:** Complete content creation pipeline for power users

**Status:** NOT MVP PRIORITY - Fix Workflows 3, 4, 5 first

### Phase Overview

```
Phase 1: RESEARCH
├── Define niche
├── Select demographics  
├── Set goals/KPIs
└── Find exemplar content (Swoop)

Phase 2: PLAN
├── Review research
├── Create content plan
└── Set timeline

Phase 3: CREATE
├── Generate scripts (uses Workflow 3 script gen)
├── AI assistance
└── Content production

Phase 4: OPTIMIZE
├── Get prediction (uses Workflow 4)
├── View recommendations
└── Iterate

Phase 5: PUBLISH
├── Platform selection
├── Scheduling
└── Final review

Phase 6: ENGAGE & LEARN
├── Track performance
├── Compare predicted vs actual
└── Feed learnings back
```

---

# PART 4: API SPECIFICATIONS

## 4.1 Critical APIs

| Endpoint | Method | Purpose | Status |
|----------|--------|---------|--------|
| /api/kai/predict | POST | Main prediction | ✅ Works |
| /api/scripts/generate | POST | Script generation | ❌ Broken/Missing |
| /api/templates | GET | Get templates | ❓ Unknown |
| /api/patterns/extract | POST | Extract 7 Legos | ❓ Unknown |

## 4.2 /api/kai/predict (Working)

```
POST /api/kai/predict

Request (FormData):
- videoFile: File (optional)
- transcript: string (optional, required if no video)
- niche: "personal_finance" | "side_hustles"
- accountSize: string
- goal: string
- workflow: string

Response:
{
  success: boolean,
  predicted_dps: number,
  confidence: number,
  predicted_range: [number, number],
  viral_potential: "poor" | "average" | "good" | "excellent",
  components_used: string[],
  component_scores: Record<string, number>,
  recommendations: string[],
  paths: PathResult[],
  features: Record<string, any>
}
```

## 4.3 /api/scripts/generate (NEEDS IMPLEMENTATION)

### CRITICAL PREREQUISITE

**Script generation CANNOT work properly until:**
1. The model can distinguish viral (DPS 80+) from average (DPS 40-60) from underperforming (DPS <40)
2. Videos in the database are categorized by performance tier within their cohort
3. The system can identify WHAT specifically makes a video viral vs average vs failure

**Without this, script generation would just produce generic content, not proven viral patterns.**

### How Script Generation Should Work (Two Modes)

#### MODE 1: Full Template Replication
User selects a viral template → System generates script that replicates ALL viral elements

```
INPUT: User selects viral video template (DPS 85+)
OUTPUT: Script that includes:
  - The EXACT verbal hook from that template (proven to work)
  - The EXACT text hook from that template (proven to work)
  - Core content that is a VARIATION of the template (same structure, different specifics)
  - The EXACT CTA from that template (proven to work)

The user gets a script that models the proven viral formula entirely.
```

#### MODE 2: Hybrid (User Custom Content)
User selects a viral template → Gets proven hook/CTA → Adds their own context/value

```
INPUT: User selects viral video template (DPS 85+) + provides their business context
OUTPUT: Script that includes:
  - The EXACT verbal hook from that template (proven)
  - The EXACT text hook from that template (proven)  
  - User's custom CONTEXT section (their business/topic)
  - User's custom VALUE section (their specific offer/insight)
  - The EXACT CTA from that template (proven)

┌─────────────────────────────────────────────────────────────────────┐
│ SCRIPT STRUCTURE (Mode 2)                                          │
├─────────────────────────────────────────────────────────────────────┤
│ 🪝 Hook (0-3s)          │ FROM TEMPLATE (proven viral)             │
│   - Verbal hook         │ "Do you understand this about money?"    │
│   - Text hook           │ [On-screen text from viral video]        │
├─────────────────────────────────────────────────────────────────────┤
│ 📋 Context (3-8s)       │ USER'S CUSTOM CONTENT                    │
│                         │ User provides their specific context     │
├─────────────────────────────────────────────────────────────────────┤
│ 💎 Value (8-10s)        │ USER'S CUSTOM CONTENT                    │
│                         │ User provides their specific value prop  │
├─────────────────────────────────────────────────────────────────────┤
│ 🔔 CTA (10-15s)         │ FROM TEMPLATE (proven viral)             │
│                         │ "Follow me for more tips on..."          │
└─────────────────────────────────────────────────────────────────────┘
```

### Why This Matters

The script generation is NOT generic AI writing. It is:
- Taking PROVEN hooks from videos that actually went viral
- Taking PROVEN CTAs from videos that actually went viral
- Preserving the STRUCTURE that made those videos viral
- Allowing user customization only in the middle sections

This requires the system to KNOW which videos are viral (which requires accurate prediction/classification).

### API Specification

```
POST /api/scripts/generate

Request:
{
  templateId: string,           // Video ID of viral template (must be DPS 80+)
  mode: "full" | "hybrid",      // Full replication vs user custom content
  platform: "tiktok" | "instagram" | "youtube",
  length: "15s" | "30s" | "60s",
  
  // For hybrid mode only:
  customContext?: string,       // User's context section
  customValue?: string,         // User's value section
  
  // Optional:
  tone?: string,
  customInstructions?: string
}

Response:
{
  success: boolean,
  script: {
    verbalHook: string,         // Spoken hook (from template)
    textHook: string,           // On-screen text hook (from template)
    context: string,            // Context section (template or user)
    value: string,              // Value section (template or user)
    cta: string,                // Call to action (from template)
    fullScript: string,         // Complete assembled script
    timings: {                  // Suggested timing for each section
      hook: "0-3s",
      context: "3-8s", 
      value: "8-12s",
      cta: "12-15s"
    }
  },
  basedOn: {
    templateId: string,
    templateDps: number,
    templateViews: number,
    templateNiche: string,
    elementsUsed: {
      verbalHook: boolean,      // true = from template
      textHook: boolean,
      context: boolean,
      value: boolean,
      cta: boolean
    }
  },
  predictedDps: number,
  generationTime: number
}
```

---

# PART 5: INTEGRATION MAP

## 5.1 How Engine Connects to Product

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    ENGINE → PRODUCT INTEGRATION                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  METHODOLOGY PACK                    PRODUCT OPS PACK                        │
│  (Engine)                            (Car)                                   │
│                                                                              │
│  ┌─────────────────┐                 ┌─────────────────┐                    │
│  │ Kai Orchestrator │ ──────────────▶│ Workflow 4      │                    │
│  │ 13 components    │    /api/kai/   │ Instant Analysis│                    │
│  │ v5 XGBoost       │    predict     │ Upload → Result │                    │
│  └─────────────────┘                 └─────────────────┘                    │
│                                                                              │
│  ┌─────────────────┐                 ┌─────────────────┐                    │
│  │ Pattern Extract  │ ──────────────▶│ Workflow 5      │                    │
│  │ 7 Legos          │   /api/        │ Template Library│                    │
│  │ 9 Attributes     │   patterns/    │ Browse → Select │                    │
│  └─────────────────┘                 └─────────────────┘                    │
│                                                                              │
│  ┌─────────────────┐                 ┌─────────────────┐                    │
│  │ Script Gen       │ ──────────────▶│ Workflow 3      │                    │
│  │ LLM + Templates  │   /api/        │ Quick Win       │                    │
│  │                  │   scripts/     │ Template→Script │                    │
│  └─────────────────┘   generate      └─────────────────┘                    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 5.2 Data Flow

```
USER ACTION                    API CALL                 ENGINE COMPONENTS
───────────────────────────────────────────────────────────────────────────────
Upload video            →    /api/kai/predict    →    FFmpeg, Whisper, 
                                                       XGBoost v5, LLMs,
                                                       Pattern components
                                                       
Select template         →    /api/templates      →    Database query
                                                       
Generate script         →    /api/scripts/       →    Pattern extraction,
                             generate                  LLM generation
                                                       
Browse templates        →    /api/templates      →    Database query,
                             ?niche=X                  DPS filtering
```

---

# PART 6: VERIFICATION PROTOCOL

## 6.1 How To Verify ANY Feature

```
VERIFICATION PROCESS
====================

1. DEFINE SUCCESS CRITERIA
   - What EXACTLY should happen?
   - What is the expected output?
   - What is the target time?

2. EXECUTE TEST
   - Perform the user action
   - Observe what actually happens
   - Capture evidence (screenshot, console, response)

3. COMPARE
   - Does actual match expected?
   - If no, what specifically failed?

4. DOCUMENT
   - Record result: PASS or FAIL
   - If FAIL, record exactly what went wrong
   - Create issue for fix

5. NEVER ACCEPT "IT WORKS" WITHOUT EVIDENCE
```

## 6.2 Verification Checklists

### Workflow 4 Verification

```
WORKFLOW 4: INSTANT ANALYSIS - VERIFICATION
===========================================
Date: ___________
Tester: ___________

STEP 1: UPLOAD
[ ] PASS / FAIL - File upload accepts MP4
[ ] PASS / FAIL - File preview shows after upload
[ ] PASS / FAIL - Transcript paste field works
Evidence: ___________

STEP 2: CONFIGURE  
[ ] PASS / FAIL - Niche dropdown has options
[ ] PASS / FAIL - Account size dropdown has options
[ ] PASS / FAIL - Button disabled until fields filled
Evidence: ___________

STEP 3: PREDICT
[ ] PASS / FAIL - Loading indicator shows
[ ] PASS / FAIL - Completes in < 25 seconds
[ ] PASS / FAIL - No errors in console
Time taken: ___ seconds
Evidence: ___________

STEP 4: RESULTS
[ ] PASS / FAIL - DPS score displays
[ ] PASS / FAIL - Confidence displays
[ ] PASS / FAIL - Range displays
[ ] PASS / FAIL - XGBoost v5 shown
[ ] PASS / FAIL - Recommendations shown
[ ] PASS / FAIL - Recommendations are specific
Sample recommendation: ___________
Evidence: ___________

OVERALL: [ ] PASS / [ ] FAIL
Blocking issues: ___________
```

### Workflow 3 Verification

```
WORKFLOW 3: QUICK WIN - VERIFICATION
====================================
Date: ___________
Tester: ___________

STEP 1: PICK TEMPLATE
[ ] PASS / FAIL - Templates load
[ ] PASS / FAIL - DPS scores shown
[ ] PASS / FAIL - Can select template
Evidence: ___________

STEP 2: GENERATE SCRIPT
[ ] PASS / FAIL - Generator modal opens
[ ] PASS / FAIL - Platform dropdown works
[ ] PASS / FAIL - Length dropdown works
[ ] PASS / FAIL - Generate button works
[ ] PASS / FAIL - Script appears
[ ] PASS / FAIL - Script has hook/body/cta
[ ] PASS / FAIL - Script is niche-appropriate
Time taken: ___ seconds
Generated script preview: ___________
Evidence: ___________

OVERALL: [ ] PASS / [ ] FAIL
Blocking issues: ___________
```

---

# PART 7: PRIORITY ROADMAP

## 7.1 Current State Summary

| System | Status | Blocking Issues |
|--------|--------|-----------------|
| Prediction Engine | ⚠️ Integration Works | Cannot distinguish viral from average from failure |
| Model Accuracy | ❌ Broken | Predicts ~60 for everything (regression to mean) |
| Video Categorization | ❌ Missing | No viral/average/underperforming labels |
| Script Generation API | ❌ Blocked | Depends on model understanding virality |
| Workflow 4 (Instant Analysis) | ⚠️ Partial | Works but predictions not meaningful for extremes |
| Workflow 3 (Quick Win) | ❌ Blocked | Depends on script generation |
| Workflow 5 (Template Library) | ❌ Blocked | Depends on script generation |

## 7.2 REVISED Fix Priority Order

**KEY INSIGHT:** Script generation quality depends on the model understanding what makes content viral. Without that, script generation just produces generic content.

```
PRIORITY 0: VIDEO CATEGORIZATION (Foundation for Everything)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PREREQUISITE: This must happen before model retraining or script generation.

Task: Categorize all scraped videos into performance tiers WITHIN THEIR COHORT
- VIRAL: Top 20% of creator's videos (or DPS 80+)
- AVERAGE: Middle 60% of creator's videos (or DPS 40-79)
- UNDERPERFORMING: Bottom 20% of creator's videos (or DPS <40)

Data Available:
- Scraped videos from specific creators
- Same creator has videos across all performance tiers
- This provides the contrast needed for learning

Output: 
- performance_tier column added to scraped_videos table
- Clear labels for model training
- Viral templates identified for script generation

Verification: 
- Count of videos per tier
- Distribution across niches
- Examples from each tier


PRIORITY 1: MODEL RETRAINING (Learn What Makes Content Viral)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPENDS ON: Priority 0 (video categorization)

Task: Retrain XGBoost to actually distinguish viral from average from failure

Current Problem:
- Model predicts ~55-65 for everything
- Cannot identify what will go viral
- Cannot identify what will fail

Training Approach:
1. Use categorized videos from Priority 0
2. Include CONTRASTING examples from same creators:
   - Creator X's viral video (DPS 95) vs Creator X's failure (DPS 25)
   - This teaches the model what CHANGED, not just what EXISTS
3. Feature importance analysis: What features differ between viral and failure?

Success Criteria:
- Within ±10 DPS accuracy: 70%+ (up from current 40%)
- Can distinguish HIGH (80+) from LOW (<40): 80%+ accuracy
- Feature importance shows meaningful patterns (not just "word count")

Verification:
- Run accuracy test on 10 diverse videos (same test we just ran)
- Separate accuracy for HIGH/MEDIUM/LOW tiers


PRIORITY 2: SCRIPT GENERATION API (Now Based on Real Viral Patterns)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPENDS ON: Priority 1 (model can identify viral content)

Task: Implement /api/scripts/generate using PROVEN viral templates

Now this can work because:
- We know which videos are actually viral (from Priority 0)
- We know what makes them viral (from Priority 1)
- Script generation pulls from PROVEN viral hooks/CTAs

Implementation: See Section 4.3 for full specification

Verification:
- API returns structured script
- Script includes actual hooks/CTAs from viral templates
- Generated scripts differ based on template selected


PRIORITY 3: WORKFLOW 4 POLISH (Demo-Ready)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPENDS ON: Priority 1 (accurate predictions)

Task: Polish Instant Analysis now that predictions are meaningful

Verification: Complete verification checklist


PRIORITY 4: WORKFLOW 3 (Quick Win)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPENDS ON: Priority 2 (script generation)

Task: Connect UI to script generation API

Verification: Complete verification checklist


PRIORITY 5: WORKFLOW 5 (Template Library)  
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPENDS ON: Priority 2 (script generation)

Task: Template browsing + script generation integration

Verification: Complete verification checklist


PRIORITY 6: WORKFLOW 1 (6-Phase Creator)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
DEPENDS ON: All above

Task: Full creation pipeline

Verification: Complete verification checklist for all 6 phases
```

## 7.3 Dependency Chain Visualization

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DEPENDENCY CHAIN                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  PRIORITY 0                                                                  │
│  Video Categorization ─────────────────────────────────────────────────┐    │
│  (Label viral/average/underperforming)                                 │    │
│                                                                         │    │
│                    │                                                    │    │
│                    ▼                                                    │    │
│                                                                         │    │
│  PRIORITY 1                                                             │    │
│  Model Retraining ─────────────────────────────────────────────────┐   │    │
│  (Learn what makes content viral)                                  │   │    │
│                                                                     │   │    │
│         │                           │                               │   │    │
│         ▼                           ▼                               │   │    │
│                                                                     │   │    │
│  PRIORITY 2                    PRIORITY 3                           │   │    │
│  Script Generation API         Workflow 4 Polish                    │   │    │
│  (Use proven viral patterns)   (Meaningful predictions)            │   │    │
│                                                                     │   │    │
│         │                                                           │   │    │
│         ├───────────────┬───────────────┐                          │   │    │
│         ▼               ▼               ▼                          │   │    │
│                                                                     │   │    │
│  PRIORITY 4        PRIORITY 5        PRIORITY 6                    │   │    │
│  Workflow 3        Workflow 5        Workflow 1                    │   │    │
│  (Quick Win)       (Templates)       (6-Phase)                     │   │    │
│                                                                     │   │    │
│                                                                     │   │    │
│  ═══════════════════════════════════════════════════════════════   │   │    │
│                              │                                       │   │    │
│                              ▼                                       │   │    │
│                                                                      │   │    │
│                     PRODUCTION READY                                 │   │    │
│                              │                                       │   │    │
│                              ▼                                       │   │    │
│                                                                      │   │    │
│                     AI CONTENT FACTORY                               │   │    │
│                     (Avatar + Editor + Live DPS)                     │   │    │
│                                                                      │   │    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

# PART 8: EXECUTION PLAN - PRIORITY 0 & 1

## 8.1 PRIORITY 0: Video Categorization

### The Data You Already Have

Based on your scraping of specific creators, you have:
- Videos from the SAME creator at different performance levels
- Creator A: Some videos went viral, some average, some underperformed
- This is THE IDEAL training data because it controls for creator quality

### Categorization Logic

```
FOR EACH VIDEO:

Option A: Cohort-Based (Preferred)
─────────────────────────────────────
Compare video to OTHER videos from the SAME creator:
- VIRAL: Top 20% of that creator's videos by engagement/views
- AVERAGE: Middle 60% of that creator's videos
- UNDERPERFORMING: Bottom 20% of that creator's videos

Why this is better:
- Controls for creator's baseline audience size
- A video with 50K views might be viral for a small creator but average for a big creator
- This is exactly what DPS is supposed to measure

Option B: Absolute DPS (Simpler)
─────────────────────────────────────
Use existing DPS scores:
- VIRAL: DPS >= 80
- AVERAGE: DPS 40-79
- UNDERPERFORMING: DPS < 40

Why this works:
- DPS already accounts for cohort
- Simpler to implement
- May miss nuance within cohorts
```

### SQL Query for Categorization

```sql
-- Add performance_tier column
ALTER TABLE scraped_videos 
ADD COLUMN IF NOT EXISTS performance_tier VARCHAR(20);

-- Option B: Absolute DPS categorization
UPDATE scraped_videos
SET performance_tier = CASE
  WHEN dps_score >= 80 THEN 'viral'
  WHEN dps_score >= 40 THEN 'average'
  WHEN dps_score < 40 THEN 'underperforming'
  ELSE 'unknown'
END
WHERE dps_score IS NOT NULL;

-- Verify distribution
SELECT 
  niche,
  performance_tier,
  COUNT(*) as count,
  AVG(dps_score) as avg_dps,
  MIN(dps_score) as min_dps,
  MAX(dps_score) as max_dps
FROM scraped_videos
WHERE performance_tier IS NOT NULL
GROUP BY niche, performance_tier
ORDER BY niche, performance_tier;
```

### Expected Output

```
CATEGORIZATION RESULTS
======================

Niche: personal_finance
├── VIRAL (DPS 80+):          [X] videos
├── AVERAGE (DPS 40-79):      [X] videos  
├── UNDERPERFORMING (DPS <40): [X] videos
└── Total:                     [X] videos

Niche: side_hustles
├── VIRAL (DPS 80+):          [X] videos
├── AVERAGE (DPS 40-79):      [X] videos
├── UNDERPERFORMING (DPS <40): [X] videos
└── Total:                     [X] videos
```

### Verification Checklist

```
PRIORITY 0 VERIFICATION
=======================

[ ] performance_tier column added to scraped_videos
[ ] All videos with dps_score have a performance_tier
[ ] At least 50 VIRAL videos per niche (for training)
[ ] At least 50 UNDERPERFORMING videos per niche (for contrast)
[ ] Distribution query shows reasonable spread
[ ] Sample videos from each tier make sense (manual check)
```

---

## 8.2 PRIORITY 1: Model Retraining

### The Problem With Current Training

Current model learned to predict "average" (~60) for everything because:
1. Training data may not have emphasized the CONTRAST between viral and failure
2. Model minimized error by predicting the mean
3. Features may not capture what DIFFERENTIATES viral from average

### Retraining Approach

```
STEP 1: Create Training Dataset with Contrast
─────────────────────────────────────────────────────────────────────
Query: Videos with extreme contrast from SAME creator

SELECT 
  v1.video_id as viral_video_id,
  v1.dps_score as viral_dps,
  v1.transcript_text as viral_transcript,
  v2.video_id as failure_video_id,
  v2.dps_score as failure_dps,
  v2.transcript_text as failure_transcript,
  v1.creator_unique_id
FROM scraped_videos v1
JOIN scraped_videos v2 
  ON v1.creator_unique_id = v2.creator_unique_id
WHERE v1.performance_tier = 'viral'
  AND v2.performance_tier = 'underperforming'
  AND v1.niche = v2.niche
  AND v1.transcript_text IS NOT NULL
  AND v2.transcript_text IS NOT NULL;

This gives pairs: Creator X's viral video vs Creator X's failure
The model can learn what CHANGED, not just what exists.


STEP 2: Balanced Training Set
─────────────────────────────────────────────────────────────────────
Include equal representation:
- 33% VIRAL videos
- 34% AVERAGE videos
- 33% UNDERPERFORMING videos

Current problem: Training set may be skewed toward average.


STEP 3: Feature Analysis
─────────────────────────────────────────────────────────────────────
Before retraining, analyze which features ACTUALLY differ:

For VIRAL videos, what's different?
- Hook word count?
- Question in first 3 seconds?
- Specific emotional words?
- Pacing?

For UNDERPERFORMING videos, what's different?
- Missing hook?
- Too long/short?
- Missing CTA?

This tells you if current 41 features can even capture virality.


STEP 4: Retrain with Balanced Data
─────────────────────────────────────────────────────────────────────
Use the categorized videos with emphasis on contrast:
- Weight viral and underperforming examples higher
- Or use classification (viral/not viral) instead of regression
```

### Alternative: Classification vs Regression

```
CURRENT APPROACH: Regression
────────────────────────────
Predict exact DPS score (0-100)
Problem: Model hedges by predicting middle values

ALTERNATIVE: Classification
────────────────────────────
Predict category: VIRAL / AVERAGE / UNDERPERFORMING
Then estimate DPS range based on category

Benefits:
- Model must commit to a category
- Can't hedge by predicting middle
- May be more useful for users anyway ("Is this viral potential?")

Could do BOTH:
- Classification model for category
- Regression model for fine-grained DPS within category
```

### Retraining Script Outline

```python
# scripts/retrain-with-contrast.py

"""
Retrain XGBoost with proper contrast between viral and underperforming.
"""

import pandas as pd
from supabase import create_client
import xgboost as xgb
from sklearn.model_selection import train_test_split

def load_categorized_videos(niche: str):
    """Load videos with performance tiers."""
    # Query videos with tiers
    viral = supabase.table('scraped_videos').select('*').eq('niche', niche).eq('performance_tier', 'viral').execute()
    average = supabase.table('scraped_videos').select('*').eq('niche', niche).eq('performance_tier', 'average').execute()
    underperforming = supabase.table('scraped_videos').select('*').eq('niche', niche).eq('performance_tier', 'underperforming').execute()
    
    return viral.data, average.data, underperforming.data

def create_balanced_dataset(viral, average, underperforming):
    """Create balanced training set with equal representation."""
    # Take minimum count to balance
    min_count = min(len(viral), len(average), len(underperforming))
    
    # Sample equally from each tier
    balanced = (
        viral[:min_count] + 
        average[:min_count] + 
        underperforming[:min_count]
    )
    
    return balanced

def analyze_feature_differences(viral_features, failure_features):
    """Identify which features actually differ between viral and failure."""
    differences = {}
    for feature in viral_features.columns:
        viral_mean = viral_features[feature].mean()
        failure_mean = failure_features[feature].mean()
        diff_pct = ((viral_mean - failure_mean) / failure_mean) * 100 if failure_mean != 0 else 0
        differences[feature] = {
            'viral_mean': viral_mean,
            'failure_mean': failure_mean,
            'diff_pct': diff_pct
        }
    
    # Sort by absolute difference
    sorted_diff = sorted(differences.items(), key=lambda x: abs(x[1]['diff_pct']), reverse=True)
    return sorted_diff

def train_model(X_train, y_train, X_test, y_test):
    """Train XGBoost with proper parameters."""
    model = xgb.XGBRegressor(
        n_estimators=100,
        max_depth=6,
        learning_rate=0.1,
        objective='reg:squarederror'
    )
    
    model.fit(X_train, y_train)
    
    # Evaluate
    predictions = model.predict(X_test)
    
    # Calculate accuracy by tier
    # ... 
    
    return model

if __name__ == '__main__':
    for niche in ['personal_finance', 'side_hustles']:
        print(f"\n{'='*60}")
        print(f"RETRAINING: {niche}")
        print(f"{'='*60}")
        
        # Load categorized videos
        viral, average, underperforming = load_categorized_videos(niche)
        print(f"Viral: {len(viral)}, Average: {len(average)}, Underperforming: {len(underperforming)}")
        
        # Create balanced dataset
        balanced = create_balanced_dataset(viral, average, underperforming)
        print(f"Balanced training set: {len(balanced)} videos")
        
        # Extract features
        # ... (use existing feature extraction)
        
        # Analyze what differs
        # ... 
        
        # Train
        # ...
        
        # Save as v6 model
        model.save_model(f'{niche}_virality_model_v6.json')
```

### Success Criteria for Retraining

```
RETRAINING SUCCESS CRITERIA
===========================

Accuracy on 10-video diverse test:
├── HIGH videos (DPS 80+):     Predict 70+ (not 55-60)
├── MEDIUM videos (DPS 40-60): Predict 50-70 (current works)
├── LOW videos (DPS <40):      Predict <50 (not 55-60)

Metrics:
├── Within ±10 DPS: 70%+ (up from 40%)
├── Within ±15 DPS: 85%+
├── Can distinguish HIGH from LOW: 80%+ accuracy

Feature Importance:
├── Top features should be interpretable
├── Not just "word_count" dominating
├── Emotional/hook features should appear
```

---

## 8.3 Cursor Prompts for Execution

### Prompt for Priority 0 (Video Categorization)

```
PRIORITY 0: VIDEO CATEGORIZATION

We need to categorize all scraped videos by performance tier before
retraining the model. This is FOUNDATIONAL - everything else depends on it.

TASK 1: Add performance_tier column to scraped_videos table
- Column type: VARCHAR(20)
- Values: 'viral', 'average', 'underperforming', 'unknown'

TASK 2: Categorize all videos with existing DPS scores
- VIRAL: dps_score >= 80
- AVERAGE: dps_score >= 40 AND dps_score < 80
- UNDERPERFORMING: dps_score < 40

TASK 3: Run distribution query and show me results
- Count per niche per tier
- Verify we have enough VIRAL and UNDERPERFORMING for training

TASK 4: Show me 3 sample videos from each tier
- I want to manually verify the categorization makes sense

DO NOT proceed to retraining until I verify these results.
Show me actual query output, not summaries.
```

### Prompt for Priority 1 (Retraining)

```
PRIORITY 1: MODEL RETRAINING WITH CONTRAST

The categorization is complete. Now retrain the model to actually 
distinguish viral from average from underperforming.

CURRENT PROBLEM:
- Model predicts ~55-65 for EVERYTHING
- Cannot identify viral content (predicts 35-55 for DPS 100 videos)
- Cannot identify failures (predicts 59-62 for DPS <40 videos)

REQUIREMENTS:

1. Create balanced training set:
   - Equal representation of viral/average/underperforming
   - Include pairs from SAME creator (viral vs failure) for contrast
   - Minimum 50 videos per tier per niche

2. Analyze feature differences BEFORE training:
   - What features are higher in viral vs failure?
   - Show me top 10 differentiating features
   - If no features differentiate, we have a feature engineering problem

3. Train new model (v6):
   - Use balanced dataset
   - Save as {niche}_virality_model_v6.json
   - Do NOT overwrite v5

4. Test new model on same 10 diverse videos:
   - Show predicted vs actual for all 10
   - Show accuracy by tier (HIGH/MEDIUM/LOW)
   - Compare to v5 results

SUCCESS CRITERIA:
- HIGH videos: Predict 70+ (not 55-60)
- LOW videos: Predict <50 (not 55-60)
- Within ±10 DPS: 70%+ (up from 40%)

Show me intermediate results at each step. DO NOT skip to "done."
```

## 8.1 Working With Cursor

```
THE CURSOR PROTOCOL
===================

1. ONE TASK AT A TIME
   Don't ask Cursor to "fix all the workflows."
   Ask Cursor to "implement /api/scripts/generate endpoint."

2. SHOW THE SPEC FIRST
   Copy the relevant specification from this document.
   "Here is what this API should do: [paste spec]"

3. REQUIRE EVIDENCE
   "Show me the actual request and response, not just 'it works.'"
   "Run this specific test and show me the output."

4. VERIFY BEFORE PROCEEDING
   Don't let Cursor move to the next task until current task is verified.
   Use the verification checklists.

5. USE ASK MODE FOR DIAGNOSIS
   When something is broken, use Ask mode to understand WHY.
   Don't let Cursor "fix" things it doesn't understand.

6. NEVER TRUST "DONE"
   Cursor will say "done" when it has written code.
   "Done" means "code exists" not "feature works."
   YOU verify. YOU sign off.
```

## 8.2 Working With Claude (Me)

```
HOW TO USE ME EFFECTIVELY
=========================

1. SHARE CONTEXT
   Show me error messages, screenshots, actual outputs.
   The more specific, the more I can help.

2. ASK FOR SPECIFIC PROMPTS
   "Give me a prompt to give to Cursor that will [specific task]."
   I'll make it forceful and specific.

3. VERIFY CURSOR'S CLAIMS WITH ME
   "Cursor says this is fixed. Here's the output. Is it actually fixed?"
   I'll tell you honestly.

4. STRATEGIC DECISIONS
   "Should I fix X before Y? What's the dependency order?"
   I'll help you prioritize.

5. DON'T ASK ME TO RUN CODE
   I can't execute code in your environment.
   I can tell you what to run and how to interpret results.
```

## 8.3 Document Maintenance

```
KEEPING DOCUMENTS CURRENT
=========================

When a workflow is FIXED:
- Update status from ❌ to ✅
- Add verification date
- Note any limitations

When a workflow is CHANGED:
- Update the specification
- Update acceptance criteria
- Update verification checklist

When a NEW workflow is added:
- Create full specification following template
- Create acceptance criteria
- Create verification checklist

Weekly review:
- Check all statuses are accurate
- Update priority order if needed
- Document any new issues discovered
```

---

# PART 9: SIGN-OFF CHECKLIST

## Production Readiness Checklist

```
CLEANCOPY PRODUCTION READINESS
==============================

PREDICTION ENGINE:
[ ] XGBoost v5 model loads correctly
[ ] Python executes (not TypeScript fallback)
[ ] Fake "xgboost" component disabled
[ ] Predictions complete in < 25 seconds
[ ] Recommendations are specific and actionable
[ ] Results save to database

WORKFLOW 4 (INSTANT ANALYSIS):
[ ] All acceptance criteria pass
[ ] Verification checklist complete
[ ] Demo-ready for users

WORKFLOW 3 (QUICK WIN):
[ ] Script generation API works
[ ] All acceptance criteria pass
[ ] Verification checklist complete

WORKFLOW 5 (TEMPLATE LIBRARY):
[ ] Templates display correctly
[ ] Script generation works
[ ] All acceptance criteria pass

CROSS-CUTTING:
[ ] No silent failures anywhere
[ ] All errors shown to user
[ ] No console errors in normal operation
[ ] Data persists correctly

SIGN-OFF:
Date: ___________
Verified by: ___________
Status: [ ] PRODUCTION READY / [ ] NOT READY
Blocking issues: ___________
```

---

## 🎯 LONG-TERM VISION REMINDER

**Once all workflows are production-ready, revisit the AI Content Factory vision:**

1. **AI Avatar Training** - Users upload 1-2 real videos → System creates AI twin
2. **Script Template Library** - Viral scripts with proven DPS 80+
3. **AI Video Generation** - Kling/HeyGen generates videos using avatar + scripts
4. **Limited Editor** - Submagic-style: captions, b-roll, zooms
5. **Live DPS Scoring** - Prediction updates in real-time during editing

This transforms CleanCopy from "predict virality" to "generate viral content."

---

**END OF PRODUCT OPERATIONS PACK v1.0**