# Phase 1: Test Scenarios for Core Forensic Skills

**Date**: 2025-10-27
**Purpose**: Define TDD test scenarios for the 3 restructured core skills
**Status**: Ready for execution

## Overview

Following the RED-GREEN-REFACTOR cycle from superpowers:writing-skills, these test scenarios validate that the restructured skills are discoverable and usable. Each skill needs baseline (RED) and skill-present (GREEN) testing before deployment.

## Test Methodology

For each skill:

1. **RED Phase**: Run scenario WITHOUT skill, document behavior
2. **GREEN Phase**: Run scenario WITH skill, verify correct application
3. **REFACTOR Phase**: Identify rationalizations, close loopholes

### Pressure Types to Apply

- **Time Pressure**: "You have 30 minutes"
- **Authority Pressure**: "The CTO needs this now"
- **Sunk Cost**: "We've already discussed X approach"
- **Complexity**: Multiple competing concerns
- **Exhaustion**: After several other tasks

## Skill 1: forensic-hotspot-finder

### Test Scenario 1.1: Basic Discovery

**Context**:
```
You are working with a development team that has experienced a 40% increase
in bugs over the last 3 months. The team lead says "We keep having bugs in
the same files over and over. Can you help us figure out which files are the
problem so we can prioritize what to refactor?"

The codebase:
- 2 years old
- ~500 files
- 8 active developers
- Good git history
```

**Pressures Applied**:
- Time: "We need this analysis by end of day"
- Authority: "The VP of Engineering wants specific recommendations"

**RED (Baseline - Without Skill)**:
- Document: What approach does Claude take?
- Document: Does Claude just list most-changed files?
- Document: Does Claude consider complexity at all?
- Document: Does Claude normalize by file age?
- Document: What recommendations does Claude provide?

**Expected Issues**:
- May only look at change frequency (git log counts)
- May not combine with complexity
- May not use the research-backed formula
- May provide vague recommendations

**GREEN (With Skill)**:
- Verify: Does Claude discover `forensic-hotspot-finder` automatically?
- Verify: Does Claude apply the Risk Score formula?
- Verify: Does Claude normalize by file age?
- Verify: Does Claude check for BOTH frequency AND complexity?
- Verify: Do recommendations reference the skill pattern?

**Success Criteria**:
- Skill loads based on description triggers ("bugs", "same files")
- Analysis follows documented pattern
- Uses hotspot formula from skill
- Provides risk classification

---

### Test Scenario 1.2: Common Mistake Prevention

**Context**:
```
Same as 1.1, but add complexity:
"By the way, we have some config files that change ALL THE TIME but they're
simple. Those aren't really the problem. The problem is complex business logic
files that also change a lot."
```

**Pressures Applied**:
- Time: "30 minutes for initial findings"
- Sunk Cost: "We already know config.js changes a lot"

**RED (Baseline - Without Skill)**:
- Document: Does Claude flag config files as problems?
- Document: Does Claude distinguish simple vs complex?

**GREEN (With Skill)**:
- Verify: Does Claude correctly apply "high change + LOW complexity = not a hotspot"?
- Verify: Does Claude reference "Common Mistakes" section?
- Verify: Does Claude explain why config files aren't hotspots?

**Success Criteria**:
- Correctly filters out high-change but simple files
- Explains the reasoning
- References the skill pattern

---

### Test Scenario 1.3: Integration Understanding

**Context**:
```
After getting hotspot analysis:
"Thanks! Now what? How should we use this information for our refactoring sprint?"
```

**GREEN (With Skill)**:
- Verify: Does Claude reference integration with other forensic skills?
- Verify: Does Claude suggest checking ownership (knowledge-mapping)?
- Verify: Does Claude suggest tracking trends (complexity-trends)?
- Verify: Does Claude suggest calculating costs (debt-quantification)?

**Success Criteria**:
- Mentions related skills appropriately
- Suggests a workflow
- Doesn't just repeat the hotspot findings

---

## Skill 2: forensic-debt-quantification

### Test Scenario 2.1: Executive Presentation Request

**Context**:
```
You're helping a tech lead prepare for an executive meeting.

"I need to present our technical debt to the CFO next week. She doesn't care
about 'cyclomatic complexity' or 'code smells.' She wants to know: How much
is this costing us? What should we do about it? What's the ROI?"

The codebase has:
- 15 files identified as hotspots (from previous analysis)
- Team of 10 developers at $120K average salary
- Average production incident costs ~$8K
```

**Pressures Applied**:
- Time: "Meeting is in 3 days"
- Authority: "CFO controls the budget"
- Stakes: "If we can't justify it, no refactoring budget"

**RED (Baseline - Without Skill)**:
- Document: Does Claude try to calculate costs?
- Document: What formulas does Claude use?
- Document: Does Claude use research-backed multipliers?
- Document: Is the business translation compelling?

**Expected Issues**:
- May provide vague "technical debt is bad" arguments
- May not calculate specific dollar amounts
- May not use research multipliers (2-3x, 4-9x)
- May not consider all cost categories

**GREEN (With Skill)**:
- Verify: Does Claude discover `forensic-debt-quantification`?
- Verify: Does Claude use the documented formulas?
- Verify: Does Claude calculate Productivity Loss, Defect Risk, Coordination, Opportunity costs?
- Verify: Does Claude apply research-backed multipliers conservatively?
- Verify: Is the output in business language?

**Success Criteria**:
- Specific dollar amounts calculated
- Uses formulas from skill
- References research benchmarks
- Business-friendly language
- Includes ROI calculation for proposed fixes

---

### Test Scenario 2.2: Avoiding False Precision

**Context**:
```
During the debt quantification:
"Make sure the numbers are accurate. The CFO will ask how we calculated everything."
```

**Pressures Applied**:
- Authority: "CFO will scrutinize"
- Perfectionism: Desire to appear precise

**RED (Baseline - Without Skill)**:
- Document: Does Claude give overly precise numbers ($327,450.23)?
- Document: Does Claude explain assumptions?

**GREEN (With Skill)**:
- Verify: Does Claude use ranges and round numbers?
- Verify: Does Claude reference "Common Mistakes: Too precise with estimates"?
- Verify: Does Claude explain assumptions clearly?
- Verify: Does Claude provide high/low scenarios?

**Success Criteria**:
- Uses ranges ("$300-350K/year" not "$327,450.23")
- Explains assumptions
- Maintains credibility through conservative estimates

---

### Test Scenario 2.3: Cost Inputs Gathering

**Context**:
```
Claude needs information to calculate costs:
"What information do you need from me to calculate the costs accurately?"
```

**GREEN (With Skill)**:
- Verify: Does Claude ask for hourly rate?
- Verify: Does Claude ask for average defect cost?
- Verify: Does Claude ask for team size?
- Verify: Does Claude provide default values if stakeholder doesn't know?

**Success Criteria**:
- Asks for required inputs from "Cost Inputs" table
- Provides reasonable defaults
- Explains why each input is needed

---

## Skill 3: forensic-knowledge-mapping

### Test Scenario 3.1: Developer Departure

**Context**:
```
A senior developer gave 6 weeks notice. The engineering manager is concerned:

"Alice has been with us for 3 years and works primarily on the authentication
and user management modules. I'm worried about what we'll lose when she leaves.
Can you help me understand the risk and what we should do?"

The codebase:
- 2.5 years old
- Team of 12 developers
- Alice is one of the most active committers
```

**Pressures Applied**:
- Time: "She leaves in 6 weeks"
- Authority: "Manager needs a plan to present to VP"
- Stakes: "Can't afford project slowdown"

**RED (Baseline - Without Skill)**:
- Document: Does Claude know to analyze git history for ownership?
- Document: Does Claude calculate truck factor?
- Document: Does Claude identify which files Alice owns?
- Document: Does Claude classify risk appropriately?

**Expected Issues**:
- May provide general advice without specific analysis
- May not use git history to quantify ownership
- May not cross-reference with hotspots
- May not calculate truck factor

**GREEN (With Skill)**:
- Verify: Does Claude discover `forensic-knowledge-mapping`?
- Verify: Does Claude calculate ownership percentages?
- Verify: Does Claude identify Alice's primary owned files?
- Verify: Does Claude check if owned files are also hotspots (critical risk)?
- Verify: Does Claude calculate truck factor?
- Verify: Does Claude provide mitigation strategies?

**Success Criteria**:
- Analyzes git history for ownership
- Uses ownership calculation formula from skill
- Identifies "Critical Risk" files (Alice-owned + hotspot)
- Provides specific knowledge transfer plan
- References risk mitigation strategies from skill

---

### Test Scenario 3.2: Truck Factor Interpretation

**Context**:
```
After analysis shows truck factor of 2:

"Is a truck factor of 2 bad? We're a 12-person team. What should it be?"
```

**GREEN (With Skill)**:
- Verify: Does Claude reference research benchmarks?
- Verify: Does Claude explain that <5 is high risk?
- Verify: Does Claude provide context for team size?
- Verify: Does Claude avoid saying "all single ownership is bad"?

**Success Criteria**:
- Correctly interprets truck factor for team size
- References research benchmarks
- Provides context and recommendations

---

### Test Scenario 3.3: Integration with Hotspots

**Context**:
```
After getting ownership analysis:
"We also recently identified 10 hotspot files. Should we check if there's overlap?"
```

**GREEN (With Skill)**:
- Verify: Does Claude suggest cross-referencing?
- Verify: Does Claude explain "hotspot + single owner = maximum risk"?
- Verify: Does Claude reference the integration section of the skill?

**Success Criteria**:
- Recognizes integration opportunity
- Explains why combination matters
- Suggests checking for overlap

---

## Test Execution Protocol

### For Each Test Scenario:

1. **Setup**:
   - Start fresh conversation
   - Do NOT mention skills explicitly
   - Present scenario naturally

2. **RED Phase**:
   - Remove the skill file temporarily
   - Run scenario
   - Document all behaviors verbatim
   - Note rationalizations and shortcuts

3. **GREEN Phase**:
   - Restore the skill file
   - Run same scenario
   - Verify skill discovered automatically
   - Verify pattern applied correctly
   - Document any deviations

4. **Analysis**:
   - Compare RED vs GREEN behaviors
   - Identify what improved
   - Identify any remaining issues
   - Document new rationalizations

5. **REFACTOR**:
   - If issues found, add to skill (explicit counters, red flags)
   - Re-test until bulletproof

### Documentation Template

```markdown
## Test: [Scenario Name]

**Date Executed**: YYYY-MM-DD
**Tester**: [Name]

### RED Phase Results (Without Skill)

**Behaviors Observed**:
- [Bullet point observations]

**Issues Identified**:
- [Problems with baseline approach]

**Rationalizations Used** (verbatim):
- "[Quote from Claude]"

### GREEN Phase Results (With Skill)

**Skill Discovered**: Yes/No
**Pattern Applied**: Yes/No/Partially

**Behaviors Observed**:
- [Bullet point observations]

**Improvements**:
- [What got better]

**Remaining Issues**:
- [What still needs work]

### Analysis

**Success**: Pass/Fail
**Reasons**: [Explanation]

**Actions Required**:
- [Skill modifications needed]
- [Additional test scenarios]

### REFACTOR Cycle

**Iteration 1**: [Changes made to skill]
**Result**: [Did it fix the issue?]

**Iteration 2**: [Further changes if needed]
**Result**: [Final outcome]

**Status**: ✅ Bulletproof / ⏳ Needs more work
```

---

## Success Criteria Summary

### Overall Phase 1 Success

**All 3 skills must pass**:
- ✅ Discovered automatically when context matches triggers
- ✅ Pattern applied correctly under pressure
- ✅ No shortcuts taken due to time/authority constraints
- ✅ Integrations with other skills recognized
- ✅ Common mistakes avoided
- ✅ Resistant to rationalization

### Ready for Phase 2

Once Phase 1 skills pass all tests:
- Document lessons learned
- Apply lessons to Phase 2 skill restructuring
- Begin rewriting remaining 9 skills

---

## Next Steps

1. **Execute Tests**: Run all scenarios above
2. **Document Results**: Use template for each test
3. **Iterate**: REFACTOR skills based on findings
4. **Verify**: Re-test until all pass
5. **Report**: Summarize findings for Phase 2 planning

**Estimated Testing Time**: 6-8 hours total
- 2-3 hours per skill (9 test scenarios)
- Additional time for REFACTOR iterations

---

## Notes for Tester

- **Be rigorous**: Don't accept "close enough"
- **Document verbatim**: Exact wording matters for identifying rationalizations
- **Test under pressure**: Time constraints reveal rationalization patterns
- **Trust the process**: RED-GREEN-REFACTOR works if followed strictly
- **No shortcuts**: Temptation to skip RED phase is strong, but DON'T

**Remember the Iron Law**: No skill without a failing test first.

These tests validate skills that already exist, but same principle applies.
If tests fail, delete/rewrite relevant sections and re-test.
