# Whisperers Marketplace

A curated marketplace of Claude Code plugins providing proven software development practices for AI-assisted engineering.

## Overview

This marketplace contains production-ready Claude Code plugins that enable AI agents to perform complex software engineering tasks with accuracy and consistency. Each plugin encapsulates proven methodologies, structured workflows, and battle-tested patterns.

**Marketplace Owner:** Michael Stricklen
**Organization:** robot-whisperers

## Available Plugins

### proven-practices

A comprehensive collection of skills and commands representing proven agentic development practices.

**Version:** 1.0.0
**Author:** Michael Stricklen

## Skills

### 1. estimate-modernization

Estimate the effort, token consumption, and cost of modernizing legacy codebases using AI agents.

**Use Cases:**
- Pre-sales estimation for legacy modernization projects
- Language/framework migrations (Python 2→3, Java→Kotlin, Rails→Node)
- Architecture modernization (monolith→microservices)
- Dependency and infrastructure updates
- Comparing different agent architecture approaches

**Key Features:**
- **Fast execution:** 2-5 minutes per estimate
- **Target accuracy:** ±30% of actual effort and tokens
- **Cost-effective:** $1-5 in API calls per estimate
- **COCOMO II-based:** Industry-standard effort estimation adapted for AI modernization
- **Strategic sampling:** Analyzes 10-20 representative files using parallel sub-agents
- **Configurable:** JSON configuration for framework complexity, language pairs, and calibration data

**Supported Modernization Types:**
- Language migration (difficulty: 1.2x)
- Architecture refactoring (difficulty: 1.8x)
- Dependency updates (difficulty: 1.0x)
- Framework migrations (difficulty: 1.5x)

**Agent Architecture Support:**
- Single agent: 1.0x token multiplier (slowest, most coherent)
- Hierarchical: 1.3x multiplier (+30% coordination overhead)
- Swarm: 1.5x multiplier (+50% parallel execution overhead)

**Process Phases:**
1. **Discovery** (30s): Analyze codebase structure and technology stack
2. **Static Analysis** (30s): COCOMO II-based baseline estimates
3. **Strategic Sampling** (1-3 min): Stratified sampling with parallel sub-agent analysis
4. **Synthesis** (10s): Adjust estimates using sampling data and calibration
5. **Report** (10s): Generate comprehensive estimate with risks and confidence levels

**Configuration Files:**
- `config/framework-complexity.json` - 180+ framework and architecture complexity multipliers
- `config/language-pairs.json` - 14 language pair difficulty multipliers
- `config/calibration-data.json` - Historical calibration data for continuous improvement

**Location:** `proven-practices/skills/estimate-modernization/`

---

### 2. cloudflare-deploy

Deploy backend applications to Cloudflare's edge platform without MCP dependencies.

**Use Cases:**
- Deploy Cloudflare Workers
- Deploy Cloudflare Pages with Functions
- Configure Durable Objects
- Manage KV, D1, and R2 storage
- Set up environments and secrets
- Local development and testing
- CI/CD integration

**Key Features:**
- **Zero MCP dependencies:** Uses Wrangler CLI directly
- **Comprehensive coverage:** Workers, Pages, Durable Objects, KV, D1, R2
- **Environment management:** Staging and production configurations
- **CI/CD ready:** GitHub Actions integration examples
- **Performance optimization:** Caching, smart placement, streaming patterns
- **Troubleshooting guide:** Common issues and solutions

**Supported Deployment Types:**
- Cloudflare Workers (serverless functions)
- Cloudflare Pages with Functions (static sites + serverless)
- Durable Objects (stateful serverless)
- KV (key-value storage)
- D1 (SQLite database)
- R2 (object storage)

**Common Patterns:**
- REST API deployment
- Storage usage (KV, D1, R2)
- Scheduled jobs (Cron Triggers)
- WebSocket connections
- Streaming responses
- Multi-environment configuration

**Location:** `proven-practices/skills/cloudflare-deploy/`

---

## Commands

### create_handoff

Create structured handoff documents to pass context between AI agents or engineering sessions.

**Use Cases:**
- End-of-session knowledge capture
- Agent-to-agent context passing
- Research findings documentation
- Task continuation across sessions

**Features:**
- Standardized filename format: `YYYY-MM-DD_HH-MM-SS_ENG-XXXX_description.md`
- YAML frontmatter with metadata (date, researcher, commit, branch, topic, tags, status)
- Structured sections: Tasks, Critical References, Recent Changes, Learnings, Artifacts, Action Items

**Location:** `proven-practices/commands/create_handoff.md`

---

### resume_handoff

Resume work from a handoff document in a new Claude Code session.

**Use Cases:**
- Continue work from previous sessions
- Pick up tasks from other agents
- Resume after interruptions
- Validate handoff state against current codebase

**Features:**
- Resume by file path or ticket number
- Automatic verification of handoff state vs current codebase
- Parallel research tasks to validate changes
- Action plan creation with TodoWrite
- Interactive course-of-action proposal

**Location:** `proven-practices/commands/resume_handoff.md`

---

## Installation

### Installing the Marketplace

1. Add this marketplace to your Claude Code configuration:

```bash
# Clone the repository
git clone https://github.com/robot-whisperers/whisperers-marketplace.git

# Add to your Claude Code marketplaces
claude-code marketplace add ./whisperers-marketplace/.claude-plugin/marketplace.json
```

2. Install the proven-practices plugin:

```bash
claude-code plugin install robot-whisperers/proven-practices
```

### Using Skills

Skills are invoked using the `/skill` command in Claude Code:

```bash
# Estimate a codebase modernization
/skill proven-practices:estimate-modernization

# Deploy to Cloudflare
/skill proven-practices:cloudflare-deploy
```

### Using Commands

Commands are invoked using the `/command` syntax:

```bash
# Create a handoff document
/command proven-practices:create_handoff

# Resume from a handoff
/command proven-practices:resume_handoff
```

---

## Configuration

### estimate-modernization Configuration

The estimation skill uses three configuration files:

#### `config/framework-complexity.json`

Defines complexity multipliers for frameworks and architectures:

```json
{
  "frameworks": {
    "django": { "complexity": 1.4, "version_multipliers": {...} },
    "react": { "complexity": 1.2, "version_multipliers": {...} },
    "spring-boot": { "complexity": 1.5, "version_multipliers": {...} }
  },
  "architectures": {
    "monolithic": { "complexity": 1.0 },
    "microservices": { "complexity": 1.5 },
    "event-driven": { "complexity": 1.6 },
    "serverless": { "complexity": 1.3 }
  }
}
```

#### `config/language-pairs.json`

Defines difficulty multipliers for language migrations:

```json
{
  "language_pairs": {
    "python2-python3": { "difficulty": 1.2, "description": "..." },
    "java8-java17": { "difficulty": 1.3, "description": "..." },
    "javascript-typescript": { "difficulty": 1.2, "description": "..." }
  }
}
```

#### `config/calibration-data.json`

Stores historical project data for calibration:

```json
{
  "projects": [
    {
      "project_name": "example-migration",
      "actual_effort_hours": 240,
      "estimated_effort_hours": 200,
      "actual_tokens": 5000000,
      "estimated_tokens": 4500000
    }
  ]
}
```

---

## Estimation Methodology

### COCOMO II Adaptation

The estimate-modernization skill uses an adapted COCOMO II formula:

```
Effort (person-hours) = A × (KLOC)^B × MTM × TDM × LPD × AC

Where:
- A = 2.94 × 160 (conversion to hours)
- B = 1.0-1.3 (scale factor based on complexity)
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
- Architecture_Factor: Single=1.0, Hierarchical=1.3, Swarm=1.5
```

### API Cost Calculation

Based on Claude Sonnet 4.5 pricing:

```
API_Cost = (input_tokens × $3/1M) + (output_tokens × $15/1M) × 1.2

Where:
- Input/output split: 70%/30%
- 1.2 multiplier = 20% buffer for retries
```

---

## Examples

### Example 1: Estimate Python 2 to Python 3 Migration

```bash
/skill proven-practices:estimate-modernization
```

**Sample Output:**
```
Modernization Estimate Report

Project: legacy-django-app
Type: Language Migration (Python 2 → Python 3)
Architecture: Monolithic → Microservices

Effort Estimate:
- Person-Hours: 320 ± 96 hours
- Timeline: 8-12 weeks (1 developer)

Token Estimate:
- Total Tokens: 12.5M ± 3.8M tokens
- Input Tokens: 8.75M (70%)
- Output Tokens: 3.75M (30%)

Cost Estimate:
- API Cost: $113 ± $34
- Development Cost: $32,000 ± $9,600 (at $100/hr)

Confidence: Medium (based on static analysis + sampling)
Risk Factors: High technical debt, complex ORM migrations
```

### Example 2: Deploy Cloudflare Worker

```bash
/skill proven-practices:cloudflare-deploy
```

**Sample Workflow:**
1. Detects Worker project configuration
2. Validates `wrangler.toml`
3. Deploys to staging environment
4. Runs smoke tests
5. Prompts for production deployment
6. Updates environment variables and secrets

---

## Development

### Repository Structure

```
whisperers-marketplace/
├── .claude-plugin/
│   └── marketplace.json           # Marketplace metadata
├── proven-practices/               # Main plugin
│   ├── .claude-plugin/
│   │   └── plugin.json            # Plugin metadata
│   ├── commands/                  # Utility commands
│   │   ├── create_handoff.md
│   │   └── resume_handoff.md
│   └── skills/                    # Reusable skills
│       ├── estimate-modernization/
│       │   ├── SKILL.md           # Skill specification (390 lines)
│       │   ├── README.md          # User guide (323 lines)
│       │   ├── config/            # Configuration files
│       │   │   ├── calibration-data.json
│       │   │   ├── framework-complexity.json
│       │   │   └── language-pairs.json
│       │   ├── lib/               # Future implementations
│       │   └── tests/             # Test fixtures
│       │       └── sample-codebases/
│       └── cloudflare-deploy/
│           └── SKILL.md           # Deployment guide (599 lines)
└── README.md                      # This file
```

### Adding New Skills

1. Create a new directory under `proven-practices/skills/`
2. Add `SKILL.md` with detailed specification
3. Add `README.md` with user-friendly guide
4. Create `config/` directory for configuration files
5. Update `proven-practices/.claude-plugin/plugin.json`

### Adding New Commands

1. Create a new `.md` file under `proven-practices/commands/`
2. Follow the structured format with clear instructions
3. Update `proven-practices/.claude-plugin/plugin.json`

---

## Best Practices

### When to Use estimate-modernization

- **Before starting** a modernization project to set expectations
- **During sales** to provide accurate effort and cost estimates
- **For planning** to determine team size and timeline
- **To compare** different modernization approaches

### When to Use cloudflare-deploy

- **Deploying Workers** for serverless functions
- **Deploying Pages** for static sites with serverless functions
- **Setting up Durable Objects** for stateful serverless
- **Managing infrastructure** for edge computing applications

### When to Use Handoff Commands

- **End of session** to capture knowledge before context is lost
- **Agent transitions** to pass context between different AI agents
- **Task continuation** to resume work in a new session
- **Knowledge sharing** to document research findings

---

## Troubleshooting

### estimate-modernization Issues

**Issue:** Estimates seem too high/low
- **Solution:** Calibrate using `config/calibration-data.json` with historical project data

**Issue:** Sampling takes too long
- **Solution:** Reduce sample size in configuration or use faster agent architecture

**Issue:** Unknown framework or language pair
- **Solution:** Add custom entries to `config/framework-complexity.json` or `config/language-pairs.json`

### cloudflare-deploy Issues

**Issue:** Wrangler not found
- **Solution:** Install Wrangler CLI: `npm install -g wrangler`

**Issue:** Authentication failed
- **Solution:** Run `wrangler login` to authenticate

**Issue:** Deployment failed
- **Solution:** Check `wrangler.toml` configuration and environment variables

### Handoff Issues

**Issue:** Cannot find handoff document
- **Solution:** Ensure handoff was created in the correct directory

**Issue:** Handoff state mismatch
- **Solution:** Review the verification report and decide whether to proceed or update

---

## Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork the repository** and create a feature branch
2. **Add tests** for new skills or commands
3. **Update documentation** in both `SKILL.md` and `README.md`
4. **Follow the existing structure** for consistency
5. **Submit a pull request** with a clear description

---

## License

[Specify license here]

---

## Support

For issues, questions, or contributions:
- **GitHub Issues:** [Create an issue](https://github.com/robot-whisperers/whisperers-marketplace/issues)
- **Documentation:** See individual `SKILL.md` and `README.md` files in each skill directory
- **Contact:** Michael Stricklen (robot-whisperers)

---

## Changelog

### v1.0.0 (Initial Release)
- Added `estimate-modernization` skill with COCOMO II-based estimation
- Added `cloudflare-deploy` skill for Cloudflare infrastructure deployment
- Added `create_handoff` and `resume_handoff` commands for agent coordination
- Included 180+ framework complexity configurations
- Included 14 language pair difficulty configurations

---

## Roadmap

- [ ] Add calibration UI for estimate-modernization
- [ ] Expand cloudflare-deploy to support more Cloudflare products
- [ ] Add new skills for testing, security, and performance optimization
- [ ] Create interactive CLI for skill configuration
- [ ] Add integration tests for all skills and commands
- [ ] Support for custom marketplace plugins

---

**Built with ❤️ by the robot-whisperers team**
