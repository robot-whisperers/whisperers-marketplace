# Legacy Modernization Estimation Skill

A Claude Code skill that estimates the effort, token consumption, and cost of modernizing legacy codebases using AI agents.

## Overview

This skill uses a **hybrid sampling approach** combining:
- **Deterministic models** (COCOMO II-based formulas)
- **Strategic LLM sampling** (10-20 representative files)
- **Actual token measurement** during sampling
- **Self-calibration** from historical project data

**Key Metrics:**
- ⏱️ **Execution time:** 2-5 minutes
- 🎯 **Accuracy target:** ±30% of actual effort and tokens
- 💰 **Cost per estimate:** $1-5 in API calls

## Use Cases

- Pre-sales estimation for legacy modernization projects
- Language/framework migrations (Python 2→3, Java→Kotlin, Rails→Node)
- Architecture modernization (monolith→microservices)
- Dependency and infrastructure updates (framework upgrades)
- Comparing agent architecture approaches (single, hierarchical, swarm)

## Installation

1. **Copy this skill to your Claude Code skills directory:**
   ```bash
   cp -r estimate-modernization ~/.claude/skills/
   ```

2. **Install recommended dependencies:**
   ```bash
   # Line counting (choose one)
   brew install tokei        # Recommended: faster
   # OR
   brew install cloc

   # Language-specific tools (optional but recommended)
   pip install radon         # Python complexity analysis
   npm install -g eslint     # JavaScript analysis
   ```

## Usage

### Basic Usage

Navigate to the codebase you want to estimate, then invoke the skill:

```bash
cd /path/to/legacy-codebase
```

In Claude Code:
```
/estimate-modernization
```

The skill will:
1. Auto-detect the codebase structure and technology stack
2. Ask clarifying questions about the modernization type
3. Perform static analysis and strategic sampling
4. Present a comprehensive estimate report

### With Parameters

You can provide parameters to skip interactive prompts:

```
/estimate-modernization --type=language --from=python2 --to=python3 --architecture=hierarchical
```

**Parameters:**
- `--type`: `language`, `architecture`, `dependency`, or `framework`
- `--from`: Source language/framework
- `--to`: Target language/framework
- `--architecture`: `single`, `hierarchical`, or `swarm`

### Calibration Mode

After completing a modernization project, calibrate the skill to improve future estimates:

```
/estimate-modernization --calibrate
```

The skill will prompt for:
- Original estimate date or ID
- Actual tokens consumed
- Actual effort hours
- Lessons learned

This data is used to improve future estimates through self-calibration.

## Understanding the Output

### Sample Report Structure

```markdown
# Legacy Modernization Estimate

## Project Overview
Basic information about the codebase and modernization type

## Effort Estimate
- Formula baseline (COCOMO)
- Sampling adjustment
- Final range (min/likely/max in person-hours)
- Calendar time with FTE assumptions

## Token Consumption Estimate
- Static baseline
- Sampling-based adjustment (measured tokens/LOC)
- Agent architecture overhead
- Final range (min/likely/max tokens)

## Cost Estimate
- API costs (Claude pricing)
- Human effort costs (optional, parameterizable)
- Total project cost range

## Breakdown by Component
Table showing effort and token estimates per architectural layer

## Key Risk Factors
Specific risks identified during sampling

## Confidence Level
Overall confidence, sample variance, recommendations

## Methodology Notes
Tools used, samples analyzed, techniques applied
```

### Interpreting Confidence Levels

- **High Confidence:** Low sample variance, good calibration data, ±20-30% expected
- **Medium Confidence:** Moderate variance, some calibration, ±30-50% expected
- **Low Confidence:** High variance, no calibration, ±50-100% expected

## How It Works

### Phase 1: Discovery (30s)
- Scans codebase for languages, files, LOC
- Identifies technology stack
- Auto-detects or confirms modernization type

### Phase 2: Static Analysis (30s)
- Computes COCOMO baseline using adapted formulas
- Calculates complexity, coupling, test coverage
- Generates initial estimate range

### Phase 3: Strategic Sampling (1-3min)
- **Stratified sampling:** Selects 10-20 representative files by:
  - Complexity tier (high/medium/low)
  - Architectural layer (UI/logic/data/infra/tests)
  - File size
- **Parallel analysis:** Spawns sub-agents to analyze each sample
- **Token measurement:** Tracks actual tokens consumed per file
- **Difficulty scoring:** Each file rated 1-10 for modernization difficulty

### Phase 4: Estimation Synthesis (10s)
- Adjusts baseline using sampling data
- Extrapolates token consumption from measured samples
- Applies agent architecture overhead factors
- Generates confidence intervals from sample variance
- Applies historical calibration if available

### Phase 5: Report Generation (10s)
- Formats comprehensive estimate with all metrics
- Includes breakdowns, risks, and recommendations

## Configuration

### Language Pairs
Edit `config/language-pairs.json` to add or adjust language pair difficulties.

Example:
```json
{
  "python2_to_python3": {
    "difficulty": 1.2,
    "notes": "Syntax changes, print statements, unicode handling"
  }
}
```

### Framework Complexity
Edit `config/framework-complexity.json` for framework-specific adjustments.

### Calibration Data
Automatically updated via `--calibrate` mode. Stored in `config/calibration-data.json`.

## Formulas and Models

### COCOMO II Adaptation

```
Effort (person-hours) = A × (KLOC)^B × MTM × TDM × LPD × AC

Where:
- A = 2.94 × 160 (converted to hours)
- B = 1.0 to 1.3 (scale factor)
- MTM = Modernization Type Multiplier (1.2-2.5)
- TDM = Technical Debt Multiplier (1.0-2.0)
- LPD = Language Pair Difficulty (0.7-2.0)
- AC = Architecture Complexity (0.8-1.8)
```

### Token Estimation

```
Base_Tokens = LOC × Base_Tokens_Per_LOC × Complexity_Factor × Architecture_Factor

Where:
- Base_Tokens_Per_LOC = 50-150 (adjusted from sampling)
- Complexity_Factor = TDM × sqrt(cyclomatic_complexity / 10)
- Architecture_Factor:
  - Single: 1.0
  - Hierarchical: 1.3 (+30% overhead)
  - Swarm: 1.5 (+50% overhead)
```

### Cost Calculation

```
API Cost = (input_tokens × $3/M) + (output_tokens × $15/M) × 1.2

Where:
- Input/output split: 70%/30%
- 1.2 = 20% buffer for retries
- Pricing: Claude Sonnet 4.5 rates
```

## Agent Architecture Options

### Single Autonomous Agent
- **Token multiplier:** 1.0 (baseline)
- **Best for:** Smaller codebases, highest coherence
- **Trade-off:** Slowest wall-clock time

### Hierarchical System
- **Token multiplier:** 1.3 (+30% overhead)
- **Best for:** Most projects, balanced approach
- **Overhead:** Coordination (10%), reporting (5%), integration (15%)

### Parallel Swarm
- **Token multiplier:** 1.5 (+50% overhead)
- **Best for:** Large codebases where speed critical
- **Overhead:** Context duplication (20%), conflicts (20%), coordination (10%)

## Accuracy and Calibration

### Expected Accuracy Over Time

| Projects Calibrated | Expected Accuracy | Confidence |
|---------------------|-------------------|------------|
| 0 (initial) | ±50% | Low |
| 1-4 | ±40% | Low-Medium |
| 5-10 | ±30% | Medium |
| 11-20 | ±25% | Medium-High |
| 20+ | ±20% | High |

### Tips for Better Estimates

1. **Calibrate regularly:** Submit actual data after projects complete
2. **Use similar projects:** Calibration data from similar modernizations is most valuable
3. **Review samples:** Look at the sampled files - do they represent your codebase well?
4. **Consider context:** Estimates assume experienced developers; adjust for team
5. **Buffer appropriately:** Use P10 (pessimistic) for contracts, P50 for planning

## Troubleshooting

### "Missing tokei/cloc" warning
Install line counting tools for better accuracy:
```bash
brew install tokei  # or: brew install cloc
```

### Estimates seem too high/low
- Check if the modernization type was correctly identified
- Review the sampled files - are they representative?
- After project completion, run `--calibrate` to improve future estimates

### Very large codebase (>500K LOC)
- Expect 5-7 minute execution time (larger sample size)
- Consider estimating subsystems separately if needed

### Sampling failures
- Some files may fail to parse (encoding, syntax errors)
- The skill continues with remaining samples
- Final report will note reduced confidence

## Design Documentation

For detailed design rationale and technical architecture, see:
- `../../docs/plans/2025-10-24-modernization-estimation-skill-design.md`

## Contributing

To improve this skill:

1. **Add language pairs:** Update `config/language-pairs.json`
2. **Add frameworks:** Update `config/framework-complexity.json`
3. **Submit calibration data:** Use `--calibrate` after projects
4. **Report issues:** Note any estimation outliers or failures

## License

Part of Claude Code Skills - see main repository for license.

## Version

**v1.0.0** - Initial release (2025-10-24)

- Hybrid sampling estimator
- COCOMO II-based formulas
- Strategic file sampling (10-20 files)
- Token consumption measurement
- Self-calibration system
- Three agent architecture models
