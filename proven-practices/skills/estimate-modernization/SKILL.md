---
name: estimate-modernization
description: Estimate the effort, token consumption, and cost of modernizing a codebase.  Use when you need to provide an software modernization estimate on how hard, how long, or how costly.
allowed-tools: Read, Grep, Glob, Bash
---

# Estimate Legacy Modernization

## Purpose

Estimate the effort, token consumption, and cost of modernizing legacy codebases using AI agents. Combines deterministic models (COCOMO II-based) with strategic LLM sampling for fast, accurate estimates suitable for pre-sales scoping.

## When to Use

- Pre-sales estimation for legacy modernization projects
- Scoping language/framework migrations (e.g., Python 2→3, Java→Kotlin)
- Estimating architecture modernization (monolith→microservices)
- Planning dependency and infrastructure updates
- Comparing different agent architecture approaches (single, hierarchical, swarm)

## Overview

This skill uses a **hybrid sampling approach** that:
1. Performs static analysis for baseline estimates (COCOMO II formulas)
2. Strategically samples 10-20 representative files to measure actual complexity
3. Measures real token consumption during sampling
4. Extrapolates to full codebase with confidence intervals
5. Self-calibrates over time using historical project data

**Execution time:** 2-5 minutes
**Accuracy target:** ±30% of actual effort and tokens
**Cost per estimate:** $1-5 in API calls

## Process

### Phase 1: Discovery (30 seconds)

Announce: "Analyzing codebase structure and technology stack..."

1. Scan the codebase to gather:
   - Total lines of code (LOC) by language
   - File count and directory structure
   - Technology stack (frameworks, build tools, dependencies)
   - Test coverage if detectable

2. **Tools to use:**
   - Run `tokei` or `cloc` for language statistics
   - Check for package managers: `package.json`, `requirements.txt`, `pom.xml`, `Gemfile`, etc.
   - Look for framework indicators in file patterns and imports

3. **Auto-detect modernization type:**
   - Language migration: Different target language specified or inferred from context
   - Architecture refactoring: Monolithic structure with microservice patterns desired
   - Dependency update: Old framework versions detected

4. If modernization type unclear, ask user:
   ```
   What type of modernization are you planning?
   A) Language/framework migration (e.g., Python 2→3, Rails→Node)
   B) Architecture modernization (e.g., monolith→microservices)
   C) Dependency/infrastructure updates (e.g., framework upgrades)
   D) Combination (specify which)
   ```

5. Gather additional context if needed:
   - Source language/framework and target language/framework
   - Desired agent architecture (ask if not specified)

### Phase 2: Static Analysis (30 seconds)

Announce: "Computing baseline estimates using adapted COCOMO model..."

1. **Calculate base metrics:**
   - Effective KLOC (thousands of LOC that need modernization)
   - Cyclomatic complexity (use language-specific tools)
   - Dependency coupling
   - Test coverage percentage

2. **Apply COCOMO II formula:**
   ```
   Effort (person-hours) = A × (KLOC)^B × MTM × TDM × LPD × AC

   Where:
   - A = 2.94 × 30 (convert person-months to hours)
   - B = 1.0 to 1.3 (based on project complexity)
   - MTM = Modernization Type Multiplier (1.2-2.5)
   - TDM = Technical Debt Multiplier (1.0-2.0)
   - LPD = Language Pair Difficulty (0.7-2.0)
   - AC = Architecture Complexity (0.8-1.8)
   ```

3. **Calculate baseline token estimate:**
   ```
   Base_Tokens = LOC × 100 × Complexity_Factor

   Where:
   - 100 = baseline tokens per LOC
   - Complexity_Factor = TDM × sqrt(avg_cyclomatic_complexity / 10)
   ```

4. **Output baseline estimate:**
   - Effort range: [low, high] person-hours
   - Token range: [min, max] tokens
   - Note: "This is the formula baseline; refining with strategic sampling..."

### Phase 3: Strategic Sampling (1-3 minutes)

Announce: "Analyzing [N] representative files to refine estimates..."

1. **Select samples using stratified sampling:**

   **Stratification dimensions:**
   - Complexity tier (high 20%, medium 60%, low 20%)
   - Architectural layer (UI, business logic, data, infrastructure, tests)
   - File size (small, medium, large)

   **Target: 10-20 files total**
   - Aim for 2-3 files per complexity tier
   - Ensure coverage across architectural layers
   - Prioritize files representative of modernization challenges

2. **For each sampled file, spawn a sub-agent:**

   Use the Task tool with a detailed prompt:
   ```
   Analyze this file for modernization from [source] to [target].

   File: [file_path]

   Your task:
   1. Read and understand the code
   2. Identify what modernization work is needed
   3. Assess the difficulty on a scale of 1-10 where:
      - 1-3: Minor syntax changes, straightforward
      - 4-6: Moderate refactoring, some logic changes
      - 7-9: Major restructuring, complex logic changes
      - 10: Complete rewrite needed

   4. Explain the key challenges

   Return JSON:
   {
     "file_path": "...",
     "loc": <number>,
     "difficulty_score": <1-10>,
     "key_challenges": ["...", "..."],
     "reasoning": "..."
   }

   IMPORTANT: Be specific and realistic in your assessment.
   ```

3. **Track token consumption:**
   - For each sub-agent task, note the tokens consumed (available in response metadata)
   - Calculate `tokens_per_loc = tokens_consumed / file_loc`

4. **Run all samples in parallel:**
   - Use Task tool to launch multiple sub-agents concurrently
   - Show progress: "Sampling progress: [N/total] files analyzed..."

5. **Collect results:**
   - Aggregate difficulty scores by stratum
   - Calculate average `tokens_per_loc` by stratum
   - Compute sample variance for confidence intervals

### Phase 4: Estimation Synthesis (10 seconds)

Announce: "Synthesizing final estimates from sampling data..."

1. **Adjust effort estimate:**
   ```
   avg_difficulty = weighted_average(difficulty_scores)

   if avg_difficulty >= 7:
       difficulty_multiplier = 1.5
   elif avg_difficulty >= 4:
       difficulty_multiplier = 1.0 + (avg_difficulty - 4) / 10
   else:
       difficulty_multiplier = 0.8

   adjusted_effort = baseline_effort × difficulty_multiplier
   ```

2. **Extrapolate token consumption:**
   ```
   For each stratum:
       stratum_tokens = stratum_loc × avg_tokens_per_loc_in_stratum

   total_tokens = sum(stratum_tokens for all strata)

   # Apply agent architecture overhead
   if architecture == "single":
       architecture_factor = 1.0
   elif architecture == "hierarchical":
       architecture_factor = 1.3
   elif architecture == "swarm":
       architecture_factor = 1.5

   final_tokens = total_tokens × architecture_factor
   ```

3. **Calculate confidence intervals:**
   ```
   Use sample variance to compute:
   - P10 (pessimistic): median - 30%
   - P50 (likely): median estimate
   - P90 (optimistic): median + 50%
   ```

4. **Apply historical calibration (if available):**
   - Load calibration data from `config/calibration-data.json`
   - Apply learned adjustment factors for this modernization type
   - Update confidence level based on calibration history

### Phase 5: Cost Calculation (5 seconds)

1. **Calculate API costs:**
   ```
   # Claude Sonnet 4.5 pricing
   input_tokens = final_tokens × 0.7
   output_tokens = final_tokens × 0.3

   api_cost = (input_tokens / 1_000_000 × $3) + (output_tokens / 1_000_000 × $15)
   api_cost_with_buffer = api_cost × 1.2  # 20% buffer for retries
   ```

2. **Calculate human effort cost (optional):**
   ```
   # Use parameterizable hourly rate (default $150)
   human_cost = adjusted_effort_hours × hourly_rate
   ```

3. **Generate ranges:**
   ```
   For tokens, effort, and costs:
   - Min (P10)
   - Likely (P50)
   - Max (P90)
   ```

### Phase 6: Report Generation (10 seconds)

Present a comprehensive report using the following structure:

```markdown
# Legacy Modernization Estimate

## Project Overview
- **Codebase Size:** [LOC] across [N] files
- **Primary Language:** [language]
- **Modernization Type:** [type]
- **Technology Stack:** [frameworks, tools]
- **Agent Architecture:** [single/hierarchical/swarm]

## Effort Estimate
- **Formula Baseline:** [X-Y] person-hours
- **Sampling Adjustment:** [+/-Z%] ([reason])
- **Final Range:** [min-max] person-hours (likely: [median])
- **Calendar Time:** [weeks/months] (with [N] FTE)

## Token Consumption Estimate
- **Static Formula Baseline:** [X-Y] tokens
- **Sampling-Based Adjustment:** [adjusted] tokens (measured avg: [N] tokens/LOC)
- **Agent Architecture Overhead:** [+X%] ([architecture type])
- **Final Range:** [min-max] tokens (likely: [median])

## Cost Estimate
- **API Costs:** $[min] - $[max] (likely: $[median])
- **Human Effort (at $[rate]/hr):** $[min] - $[max] (likely: $[median])
- **Total Project Cost:** $[min] - $[max] (likely: $[median])

## Breakdown by Component
[Table showing LOC, difficulty, tokens, and hours per architectural layer/component]

## Key Risk Factors
[List of risks identified during sampling with ⚠️ or ✅ indicators]

## Confidence Level
- **Overall Confidence:** [Low/Medium/High]
- **Sample Variance:** ±[X%]
- **Recommendation:** [guidance based on confidence]

## Methodology Notes
- [Tools used for static analysis]
- [Number] files sampled (stratified by [dimensions])
- Actual token measurement: [N] tokens/LOC average
- COCOMO II adapted with modernization multipliers
```

After presenting the report, ask:

```
Would you like to:
1. Export this estimate (Markdown/PDF)
2. Adjust parameters and re-estimate
3. See detailed sampling analysis
4. Proceed with modernization planning
```

## Calibration Mode

When user runs `/estimate-modernization --calibrate`:

1. **Prompt for project data:**
   ```
   Let's calibrate based on a completed project.

   - Original estimate ID or date: [prompt]
   - Actual tokens consumed: [prompt]
   - Actual effort hours: [prompt]
   - Any lessons learned: [prompt]
   ```

2. **Calculate adjustment factors:**
   ```
   token_adjustment = actual_tokens / estimated_tokens
   effort_adjustment = actual_hours / estimated_hours
   ```

3. **Update calibration data:**
   - Load `config/calibration-data.json`
   - Add new project entry
   - Recalculate global adjustments using exponential moving average
   - Save updated calibration data

4. **Report improvement:**
   ```
   Calibration updated! Your estimates will now be more accurate.

   - Previous accuracy: ±[X%]
   - Updated accuracy: ±[Y%]
   - Total calibration data points: [N] projects
   ```

## Key Principles

1. **Speed over perfection:** Pre-sales estimates need to be fast (2-5 min)
2. **Range over precision:** Provide min/likely/max, not false precision
3. **Measure, don't guess:** Use actual token consumption from sampling
4. **Learn continuously:** Apply calibration data to improve over time
5. **Explain reasoning:** Show methodology and risk factors clearly
6. **Be honest about confidence:** Low sample quality = lower confidence

## Error Handling

### Unsupported Language
- Warn user that language is not in configuration
- Use generic multipliers (1.0 for all factors)
- Request calibration feedback after project completes

### Very Small Codebase (<1K LOC)
- Skip sampling phase
- Use formula-only estimate
- Add caveat: "Codebase too small for statistical sampling"

### Very Large Codebase (>500K LOC)
- Increase sample size to 30-40 files
- Warn: "This may take 5-7 minutes"
- Stratify more finely for better representation

### Sampling Failures
- If a sub-agent fails on a file, skip it
- Continue with remaining samples
- Note reduced confidence in final report

### Missing Tools
- If `tokei`/`cloc` not available, use basic file parsing
- Warn about reduced accuracy
- Suggest installing tools for better estimates

## Dependencies

Required for best results:
- `tokei` or `cloc` (line counting)
- Language-specific analyzers (optional but recommended):
  - Python: `radon` (complexity)
  - JavaScript: `eslint`
  - Java: `checkstyle`

Install instructions will be shown if tools are missing.

## Success Criteria

A good estimate should:
- Complete in 2-5 minutes for typical codebases
- Provide a realistic range that covers actual effort 80% of the time
- Improve accuracy over time as calibration data accumulates
- Present results clearly with actionable insights
- Be explainable to non-technical stakeholders
