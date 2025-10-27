# Forensic Dashboard Command

Generate a comprehensive code forensics dashboard combining multiple analyses for executive reporting and ongoing quality monitoring.

---

## What This Command Does

This command orchestrates multiple forensic skills to create a comprehensive dashboard showing:
- Overall code health metrics
- Technical debt costs and trends
- Code hotspots (high-risk files)
- Team knowledge map and bus factor
- Quality trends over time
- Refactoring priorities with ROI

**Use this when:**
- Creating executive reports on code quality
- Preparing quarterly technical debt reviews
- Demonstrating ROI of quality investments
- Ongoing code health monitoring
- Stakeholder communication about technical work

---

## Dashboard Generation Workflow

**You are a code forensics expert creating comprehensive dashboards for stakeholder communication.**

When this command is invoked, follow this systematic process:

### Step 1: Gather Requirements

Ask the user:

1. **Audience** (determines level of detail and language):
   - `executive` - High-level, business-focused, minimal technical detail
   - `technical` - Detailed metrics, code-level insights, technical recommendations
   - `both` - Layered report with executive summary + technical deep-dive (default)

2. **Output Format**:
   - `markdown` - Text-based report (easy to version control, read in terminal/GitHub)
   - `html` - Interactive dashboard with charts (shareable, presentation-ready)
   - `json` - Raw data export (for external tools, APIs, trending)
   - `all` - Generate all formats (default for comprehensive reporting)

3. **Time Period** (for trend analysis):
   - Default: "12 months ago"
   - Can specify: "6 months ago", "last quarter", "since 2024-01-01", etc.

4. **Output Location**:
   - Default: `./forensic-dashboard/`
   - User can specify custom path

### Step 2: Run Core Forensic Analyses

Execute these skills in sequence (or use cached results if recent):

1. **forensic-hotspot-finder**
   - Identifies high-risk files (high change frequency + high complexity)
   - Provides risk scores for prioritization

2. **forensic-debt-quantification**
   - Calculates total technical debt cost in dollars
   - Breaks down by category (productivity, defects, coordination)

3. **forensic-knowledge-mapping**
   - Maps code ownership
   - Calculates bus factor
   - Identifies knowledge silos

4. **forensic-complexity-trends**
   - Tracks complexity evolution over time period
   - Classifies files as improving/stable/deteriorating

5. **forensic-unplanned-work**
   - Analyzes bug fix commits vs planned work
   - Identifies interrupt hotspots

6. **forensic-change-coupling**
   - Detects files that change together
   - Reveals shotgun surgery patterns

7. **forensic-coordination-analysis**
   - Identifies coordination bottlenecks
   - Detects files with >9 contributors (high defect risk)

8. **forensic-test-analysis**
   - Analyzes test suite quality
   - Identifies brittle tests

9. **forensic-refactoring-roi**
   - Calculates ROI for refactoring candidates
   - Generates prioritized roadmap (quick wins → strategic)

### Step 3: Aggregate & Synthesize

Combine results from all analyses:

1. **Calculate Overall Health Score** (0-100):
   ```
   Health = (
     (100 - hotspot_severity) × 0.25 +
     (100 - debt_growth_rate) × 0.20 +
     (bus_factor_score) × 0.15 +
     (complexity_trend_score) × 0.15 +
     (100 - unplanned_work_ratio) × 0.15 +
     (test_quality_score) × 0.10
   )

   Rating:
     90-100: EXCELLENT (low risk)
     70-89:  GOOD (moderate risk)
     50-69:  MEDIUM (attention needed)
     30-49:  POOR (urgent action required)
     0-29:   CRITICAL (crisis mode)
   ```

2. **Identify Top 3-5 Critical Issues**:
   - Cross-reference analyses to find compound risks
   - Example: Hotspot + Knowledge silo + Deteriorating complexity = CRITICAL
   - Prioritize by business impact

3. **Calculate Total Debt Cost**:
   - Use forensic-debt-quantification results
   - Show trend (growing/stable/shrinking)
   - Break down by category

4. **Determine Refactoring Investment**:
   - Use forensic-refactoring-roi results
   - Calculate total investment needed
   - Show expected ROI and break-even period

### Step 4: Generate Dashboard

Create comprehensive dashboard with these sections:

#### For All Audiences:

**1. Executive Summary**
- Overall code health score (with rating)
- Top 3-5 critical issues (in business language)
- Total technical debt cost (annual)
- Recommended investment (with ROI)
- Trend assessment (improving/stable/deteriorating)

**2. Code Hotspot Heatmap**
- ASCII or chart visualization (change frequency vs complexity)
- Top 5 critical hotspots with metrics
- Color-coded risk levels

**3. Technical Debt Breakdown**
- Total annual cost
- Breakdown by category (productivity, defects, coordination)
- Top 3 most expensive files
- Trend over time period

**4. Team Knowledge Map**
- Bus factor (number + assessment)
- Code ownership distribution
- Critical risk files (single owner + hotspot)
- Knowledge silos by developer

**5. Quality Trends**
- Unplanned work ratio over time
- Complexity trends for top hotspots
- Target vs current metrics

**6. Refactoring Priorities (ROI Ranked)**
- Phase 1: Quick wins (high ROI, low effort)
- Phase 2: High impact (strategic investments)
- Phase 3: Long-term improvements
- Effort-impact matrix visualization

**7. Recommended Action Plan**
- Immediate actions (this sprint)
- Short-term (1-2 months)
- Medium-term (2-4 months)
- Long-term (ongoing)

**8. Success Metrics**
- KPIs to track monthly
- Current vs target values
- Status indicators (🔴/🟡/🟢)

**9. Key Insights for Leadership**
- 3-5 bullet points in business language
- Bottom-line summary with clear ROI

#### Additional for Technical Audience:

- Detailed coupling analysis with network diagrams
- Test suite health metrics (brittle tests, CI/CD time)
- Specific file-level recommendations
- Code examples of patterns to refactor
- Git commands to reproduce analyses

### Step 5: Export & Save

1. **Create output directory** if it doesn't exist
2. **Generate requested formats**:
   - Markdown: `dashboard-YYYY-MM-DD.md`
   - HTML: `dashboard-YYYY-MM-DD.html` (with embedded Chart.js)
   - JSON: `dashboard-data-YYYY-MM-DD.json`
3. **Save raw data** for historical trending: `raw-data-YYYY-MM-DD.json`
4. **Create index** if multiple dashboards exist: `index.md` or `index.html`

### Step 6: Present Results

1. **Show file paths** of generated dashboards
2. **Display executive summary** inline (first section)
3. **Highlight top 3 critical issues** and recommended actions
4. **Provide next steps**:
   - View full dashboard (provide commands to open files)
   - Run specific forensic skills for deep dives
   - Schedule next dashboard generation
   - Set up automated monitoring

---

## Dashboard Template Structure

Use this structure for all dashboards:

```markdown
# Code Forensics Dashboard
**Generated:** [Date]
**Period Analyzed:** [Date Range]
**Repository:** [Repo Name]

---

## 🎯 Executive Summary

### Overall Code Health Score: [Score]/100 ([Rating])

**Critical Issues Requiring Attention:**
1. [Issue 1] - [Impact]
2. [Issue 2] - [Impact]
3. [Issue 3] - [Impact]

**Cost of Technical Debt:** $[Amount]/year
- Productivity Loss: $[Amount]
- Defect Risk: $[Amount]
- Coordination Overhead: $[Amount]

**Recommended Investment:** $[Amount] over [Time Period]
- Expected ROI: [Percentage]% annually
- Break-even: [Months] months
- Payback: $[Amount]/year savings

**Trend:** [↑ DETERIORATING / → STABLE / ↓ IMPROVING]

---

## 📊 Code Hotspot Heatmap

[ASCII visualization or chart]

**Top 5 Critical Hotspots:**
1. [file] - [changes] changes, [LOC] LOC, [key metric]
2. ...

---

## 💰 Technical Debt Breakdown

[Visualization + breakdown]

**Trend (Past [Period]):**
[Chart showing quarterly/monthly progression]

---

## 👥 Team Knowledge Map

**Bus Factor:** [Number] ([Assessment])

[Ownership distribution visualization]

**Critical Risk Files:**
- [file] ([owner]) - [ownership %]

---

## 📈 Quality Trends ([Period] View)

[Trend charts for:]
- Unplanned work ratio
- Complexity trends (top hotspots)

---

## 🔗 Change Coupling & Dependencies

**Top Change Couplings:**
1. [file A] ↔ [file B] - Coupling: [strength] ([assessment])

[Network visualization]

---

## 🧪 Test Suite Health

**Test Quality Metrics:**
- Brittle tests: [count]
- Test duplication: [percentage]
- Slow tests: [count]
- Flaky tests: [count]

**CI/CD Impact:**
- Average build time: [time]
- Daily developer wait time: [hours]

---

## 🏆 Refactoring Priorities (ROI Ranked)

**Phase 1: Quick Wins**
[List with investment/savings/ROI]

**Phase 2: High Impact**
[List with investment/savings/ROI]

**Phase 3: Strategic**
[List]

[Effort-impact matrix]

---

## 🎯 Recommended Action Plan

### Immediate Actions (This Sprint)
1. [Action] ([time]) → Save $[amount]/year

### Short-Term (Next 1-2 Months)
[Actions with metrics]

### Medium-Term (Months 2-4)
[Actions with metrics]

### Long-Term (Ongoing)
[Strategic initiatives]

---

## 📋 Success Metrics

| Metric | Current | Target | Status |
|--------|---------|--------|--------|
| [metric] | [value] | [target] | [🔴/🟡/🟢] |

**Overall Health: [🔴/🟡/🟢] [Assessment]**

---

## 💡 Key Insights for Leadership

1. [Insight 1 in business language]
2. [Insight 2 with quantified impact]
3. [Insight 3 with clear recommendation]

**Bottom line:** [One sentence summary with ROI]

---

*Dashboard generated by Claude Code Forensics Suite*
*Next update scheduled: [Date]*
```

---

## HTML Dashboard Features

When generating HTML format, include:

1. **Interactive Charts** using Chart.js:
   - Technical debt trend (line chart)
   - Unplanned work ratio (bar chart)
   - Hotspot scatter plot (change frequency vs complexity)
   - Ownership distribution (pie chart)

2. **Summary Cards** with key metrics:
   - Technical debt cost
   - Unplanned work percentage
   - Bus factor
   - Code hotspots count

3. **Responsive Design**:
   - Mobile-friendly
   - Print-optimized
   - Dark/light mode toggle

4. **Export Capabilities**:
   - Print to PDF
   - Download JSON data
   - Copy charts as images

---

## Important Notes

- **Regenerate Regularly**: Monthly or quarterly for trend tracking
- **Track Historical Data**: Save raw JSON for comparison over time
- **Customize Language**: Use organization-specific terminology
- **Both Contexts**: Always include technical detail AND business context
- **Actionable Always**: Every insight must have a recommended action with ROI
- **Shareable**: Make it easy to distribute to stakeholders
- **Automatable**: Consider suggesting CI/CD integration for automated generation

---

## Example Invocation

```
User: /forensic-dashboard
Claude: I'll generate a comprehensive code forensics dashboard.

Before I begin, let me confirm a few settings:

1. **Audience**: executive, technical, or both? (default: both)
2. **Output Format**: markdown, html, json, or all? (default: all)
3. **Time Period**: How far back should I analyze? (default: 12 months)
4. **Output Location**: Where should I save the dashboard? (default: ./forensic-dashboard/)

Would you like to use the defaults, or specify custom settings?

[User responds or confirms defaults]

Claude: Perfect! I'll now run the comprehensive forensic analysis. This will:
1. Analyze code hotspots
2. Calculate technical debt costs
3. Map team knowledge
4. Track quality trends
5. Generate refactoring priorities with ROI

This may take 5-10 minutes for a large codebase. I'll show progress as I go...

[Runs analyses]

Dashboard generated successfully!

📊 **Executive Summary**
Overall Code Health: 68/100 (MEDIUM RISK)

**Top 3 Critical Issues:**
1. Payment module - High defect risk, 45% of unplanned work
2. Auth system - Single owner, high complexity, business critical
3. Core config - 14 contributors, coordination bottleneck

**Technical Debt Cost**: $465K/year
**Recommended Investment**: $120K over 3 months (ROI: 180%)

📁 **Files Generated**:
- ./forensic-dashboard/dashboard-2025-10-27.md
- ./forensic-dashboard/dashboard-2025-10-27.html
- ./forensic-dashboard/dashboard-data-2025-10-27.json

**Next Steps**:
1. Review full dashboard: `open ./forensic-dashboard/dashboard-2025-10-27.html`
2. Deep dive on payment module: Use forensic-hotspot-finder skill
3. Schedule next dashboard: Monthly (recommended)

Would you like me to:
- Open the dashboard?
- Deep dive into any specific issue?
- Generate a focused action plan?
```

---

## Success Criteria

A good dashboard should:
- ✅ Be understandable by non-technical stakeholders
- ✅ Include both high-level summary AND technical details
- ✅ Quantify all issues in business terms (dollars, time, risk)
- ✅ Provide specific, actionable recommendations
- ✅ Show ROI for all proposed investments
- ✅ Use visualizations to clarify complex data
- ✅ Include historical trends (not just current state)
- ✅ Be shareable and presentation-ready
- ✅ Save raw data for future comparison
- ✅ Guide next steps clearly
