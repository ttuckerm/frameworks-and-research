top 5% performance relative to creator's cohort (DPS)
The Dynamic Percentile System (DPS) is a sophisticated, adaptive method for determining viral content that replaces the outdated "5X rule." Here's the complete system:
Core Formula:
Viral_Score = (View_Count / Cohort_Median) × Platform_Weight × Decay_Factor

Where:
- Cohort_Median = median views for accounts with ±20% follower count
- Platform_Weight = {TikTok: 1.0, Instagram: 0.85, YouTube: 0.7}
- Decay_Factor = e^(-λt), where λ = platform decay rate and t = time
Viral Classification Thresholds:
•	Viral: Top 5% of Viral_Score within cohort
•	Hyper-viral: Top 1% of Viral_Score within cohort
•	Mega-viral: Top 0.1% of Viral_Score within cohort
Platform-Specific Decay Rates (λ):
•	TikTok: λ = 0.5 (steep decay)
•	Instagram: λ = 0.3 (moderate decay)
•	YouTube: λ = 0.1 (gradual decay)
How It Works:
1. Cohort Definition
Instead of comparing all content universally, DPS groups creators by similar follower counts:
•	A 10K follower account is compared to other 8K-12K accounts
•	A 1M follower account is compared to other 800K-1.2M accounts
2. Dynamic Baselines
The system continuously updates what's "normal" for each cohort:
•	Calculates median performance weekly
•	Adjusts for seasonal trends
•	Accounts for platform-wide changes
3. Time-Sensitive Scoring
The Decay_Factor ensures early performance is weighted appropriately:
•	TikTok: A video's first 3 hours are critical (scores decay rapidly after)
•	Instagram: First 6 hours matter most
•	YouTube: Performance window extends to 24+ hours
4. Platform Weighting
Recognizes that different platforms have different viral dynamics:
•	TikTok (1.0): Easiest to go viral due to algorithm
•	Instagram (0.85): Moderate viral potential
•	YouTube (0.7): Harder to achieve viral status
Update Mechanism:
Weekly (Sundays 00:00 UTC):
1.	Recalculate cohort medians for all follower brackets
2.	Analyze platform-wide performance shifts
3.	Adjust decay rates if engagement patterns change
4.	Update platform weights based on viral frequency data
Monthly:
•	Redefine cohort boundaries if needed
•	Review percentile thresholds based on actual viral outcomes
Quarterly:
•	Major platform weight recalibration
•	Algorithm change detection and adjustment
Example Calculation:
Scenario: A TikTok video from a 50K follower account gets 500K views in 2 hours
1.	Cohort_Median for 40-60K accounts = 75K views
2.	View_Ratio = 500K / 75K = 6.67
3.	Platform_Weight (TikTok) = 1.0
4.	Decay_Factor at 2 hours = e^(-0.5×2) = 0.368
5.	Viral_Score = 6.67 × 1.0 × 0.368 = 2.45
This score is then compared to all other scores in the cohort to determine percentile ranking.
Advantages Over Static Rules:
1.	Self-Adjusting: Automatically adapts to platform changes
2.	Fair Comparison: Small creators aren't compared to mega-influencers
3.	Time-Aware: Recognizes that viral momentum has different speeds per platform
4.	Statistically Grounded: Based on actual distribution rather than arbitrary multipliers
5.	Nuanced Classification: Distinguishes between viral, hyper-viral, and mega-viral
Implementation Benefits:
•	For Small Creators: More realistic viral thresholds (not competing with 1M+ accounts)
•	For Large Creators: Acknowledges that 5× is nearly impossible at scale
•	For Platforms: Accurate reflection of each platform's unique dynamics
•	For Trending Content: Captures early momentum accurately with time decay
This system ensures that "viral" actually means exceptional performance relative to similar creators and current platform norms, not an arbitrary fixed threshold that becomes meaningless over time.



Updated Platform-Specific Metrics:
TikTok Performance Standards
Viral Thresholds (Dynamic Percentile System):
•	Viral = Top 5% performance for creator cohort
•	Hyper-viral = Top 1% performance
•	Small accounts (<10K): Different baseline than large
•	Platform-specific decay: Steep curve (1.0 → 0.3 → 0.1 within 24 hours)
•	Engagement rate: 3-4% average, >6% for top 5% performance
•	Share rate: 0.3% good, 0.5%+ for top 5%
•	Comment rate: 0.5-1.0% healthy
•	First hour: Strong performance indicates viral potential
Instagram Reels Performance Standards
Viral Thresholds (Dynamic Percentile System):
•	Viral = Top 5% performance for creator cohort
•	Hyper-viral = Top 1% performance
•	Engagement rate: 2% average, >3% good, >6% for top 5%
•	Share rate: 0.3% indicates strong performance
•	Save rate: 1% exceptional
•	Non-follower percentage: >90% for viral reach
•	Platform-specific decay: Gradual curve (1.0 → 0.8 → 0.5 → 0.3 over 48 hours)
YouTube Shorts Performance Standards
Viral Thresholds (Dynamic Percentile System):
•	Viral = Top 5% performance for creator cohort
•	Hyper-viral = Top 1% performance
•	Platform-specific decay: Extended curve (1.0 → 0.9 → 0.7 → 0.5 over 7 days)
•	Swipe-away rate: <40% excellent
•	AVD: >70% of video length
•	CTR: >10% for browse features
•	Engagement rate: 5-6% average, >8% for top 5%
LinkedIn Performance Standards
Viral Thresholds (Dynamic Percentile System):
•	Viral = Top 5% performance for professional content in your niche
•	Hyper-viral = Top 1% (rare on LinkedIn)
•	Platform-specific decay: Sharp initial (1.0 → 0.4 → 0.2 in 6 hours)
•	First hour: 20+ engagements minimum
•	Engagement rate: >2% average, >4% for top 5%
•	Professional network amplification crucial
New Performance Calibration Note:
Dynamic Percentile System (DPS) Explained:
•	Replaces static multiplier rules (e.g., "5X followers")
•	Accounts for creator size variations
•	Platform-specific normalization
•	Regular recalibration based on platform changes
•	Cohort-based comparison (creators of similar size/niche)
•	More accurate viral prediction
•	Adapts to algorithm updates automatically
This system ensures that viral benchmarks remain relevant regardless of creator size or platform algorithm changes, providing more accurate and actionable metrics for content performance evaluation.
