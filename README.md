# mdub-agent-skills

A marketplace of Claude Code skills.

## Repository Structure

Skills are organized at the root level:

```
mdub-agent-skills/
├── .claude-plugin/
│   └── marketplace.json
├── d2-diagrams/        # D2 diagram generation skill
└── [future-skill]/     # Additional skills can be added here
```

## Available Skills

### d2-diagrams

Create professional diagrams using D2 (d2lang.com), a modern declarative diagramming language.

**Features:**
- Generate architecture diagrams, flowcharts, network diagrams, and more
- Support for multiple layout engines (dagre, elk, tala)
- Professional themes and styling options
- Comprehensive syntax reference and examples

**Prerequisites:**
- Requires the `d2` CLI to be installed
- macOS: `brew install d2`
- Other platforms: See https://d2lang.com/tour/install

## Installation

### Option 1: Add as Marketplace (Recommended)

Add this marketplace to your Claude Code settings:

```bash
claude marketplace add https://github.com/mdub/agent-skills
```

Then install specific plugins:

```bash
claude plugin install diagramming
```

### Option 2: Direct GitHub Installation

Install the plugin directly from GitHub:

```bash
claude plugin install github:mdub/agent-skills
```

### Option 3: Local Development

Clone this repository and add it as a local marketplace:

```bash
git clone https://github.com/mdub/agent-skills.git
claude marketplace add file:///path/to/agent-skills
claude plugin install diagramming
```

## Usage

Once installed, Claude Code will automatically use these skills when appropriate. For example:

- "Create a diagram showing my microservices architecture"
- "Generate a network topology diagram"
- "Draw a sequence diagram for the authentication flow"

You can also explicitly invoke the skill using the Skill tool in Claude Code.

## Contributing

### Adding New Skills

1. Create a new directory at the repository root with your skill name
2. Add a `SKILL.md` file with the skill definition (see existing skills for examples)
3. Update this README with skill details
4. Submit a pull request

### Skill Structure

Each skill directory must contain:

```
skill-name/
├── SKILL.md (required)
├── examples/ (optional)
└── templates/ (optional)
```

Each `SKILL.md` must include:
- YAML frontmatter with `name`, `description`, and optional `allowed-tools`
- Detailed instructions for Claude
- Examples and usage patterns
- Prerequisites and troubleshooting

## License

MIT License - See LICENSE file for details

## Author

Mike Williams ([@mdub](https://github.com/mdub))
