---
name: d2-diagrams
description: Create diagrams using D2 (d2lang.com), a modern declarative diagramming language. This skill should be used when users request architecture diagrams, flowcharts, network diagrams, sequence diagrams, or any visual representations that can be expressed through connections and shapes. Requires the d2 CLI to be installed.
allowed-tools: Write, Read, Edit, Bash, Glob, Grep
---

# D2 Diagrams

Generate professional diagrams using D2, a modern text-to-diagram language that creates beautiful visualizations from simple declarative syntax.

## When to Use This Skill

This skill should be used when users request:
- Architecture diagrams (system design, infrastructure)
- Network diagrams
- Flowcharts and process diagrams
- Sequence diagrams
- Entity relationship diagrams
- Organizational charts
- Any diagram that shows relationships between entities

## Prerequisites

The user should have the `d2` CLI installed:
- macOS: `brew install d2`
- Other platforms: See https://d2lang.com/tour/install

Optional: VSCode extension for D2 syntax highlighting and preview support: https://github.com/terrastruct/d2-vscode

## Workflow

### 1. Understand Requirements
To understand requirements:
- Ask clarifying questions about the diagram's purpose
- Identify key entities and their relationships
- Determine appropriate diagram type and layout

### 2. Create D2 Source File
To create the D2 source:
- Write a `.d2` file with clear, descriptive D2 syntax
- Use appropriate shapes for different entity types (see references/d2-syntax-full.md)
- Add meaningful labels and connections
- Apply styling and layout options as needed

### 3. Generate Diagram
To generate the diagram:
- Run `d2 <filename>.d2 <output>.svg` to generate SVG output
- Alternative formats: PNG, PDF (use `--output=<file>.png` or similar)
- Use layout engine flags if needed: `--layout=elk` or `--layout=tala`
- Apply themes with `--theme=<theme-id>` if desired

### 4. Review and Iterate
To complete the task:
- Show the generated file path to the user
- Be ready to modify the D2 source based on feedback
- Regenerate after making changes

## D2 Syntax Quick Reference

### Basic Elements
```d2
# Simple connection
server -> database: queries

# Shape with label
api: "API Server"

# Shape with type
cache.shape: cylinder
user.shape: person
```

### Common Shape Types
- `rectangle` (default)
- `cylinder` - databases, caches
- `person` - users, actors
- `hexagon` - gateways, load balancers
- `cloud` - cloud services
- `diamond` - decision points

For complete syntax reference, see `references/d2-syntax-full.md`.

## Layout Engines

- **dagre** (default): Fast, hierarchical layouts
- **elk**: Mature engine, good for complex diagrams
- **tala**: Best for software architecture

Specify via: `d2 --layout=elk diagram.d2 output.svg`

## Quick Example

For a microservices architecture request:

```d2
users: Users {shape: person}
api: "API Gateway" {shape: hexagon}
auth: "Auth Service"
user: "User Service"
db: "Database" {shape: cylinder}

users -> api: HTTPS
api -> auth: authenticate
api -> user: requests
auth -> db: user data
user -> db: profiles
```

Generate with: `d2 diagram.d2 output.svg`

## Advanced Features

For power users, D2 supports advanced features:
- **Variables**: Define reusable values with `vars` keyword
- **Globs**: Apply styles to multiple objects with wildcard patterns
- **Classes**: Create reusable style sets
- **Overrides**: Modify previously defined elements
- **Legend**: Add explanatory legends to diagrams

See `references/advanced-features.md` for complete documentation on these features.

## Troubleshooting

- **d2 command not found**: User needs to install D2 CLI
- **Layout issues**: Try different layout engines (`--layout=elk` or `--layout=tala`)
- **Overlapping elements**: Use containers to group related elements
- **Syntax errors**: Check proper quoting of labels with spaces
- **Need detailed syntax**: Reference `references/d2-syntax-full.md` for complete documentation

## Example Diagrams

Complete example diagrams are available in `examples/`:
- `examples/microservices-architecture.d2` - Microservices setup with API gateway, services, and data layer
- `examples/network-topology.d2` - DMZ network architecture with firewall, public and internal zones

These examples can be used as starting templates or reference for common patterns.

## Additional Resources

- D2 Documentation: https://d2lang.com/tour/intro
- D2 Playground: https://play.d2lang.com (test syntax interactively)
- Complete syntax reference: `references/d2-syntax-full.md`
- Advanced features guide: `references/advanced-features.md`
- Example templates: `examples/`
