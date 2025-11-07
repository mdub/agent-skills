---
name: d2-diagrams
description: Create diagrams using D2 (d2lang.com), a modern declarative diagramming language. Use this when users request architecture diagrams, flowcharts, network diagrams, sequence diagrams, or any visual representations that can be expressed through connections and shapes. Requires the d2 CLI to be installed.
allowed-tools: Write, Read, Edit, Bash, Glob, Grep
---

# D2 Diagrams

Generate professional diagrams using D2, a modern text-to-diagram language that creates beautiful visualizations from simple declarative syntax.

## When to Use This Skill

Use this skill when users request:
- Architecture diagrams (system design, infrastructure)
- Network diagrams
- Flowcharts and process diagrams
- Sequence diagrams
- Entity relationship diagrams
- Organizational charts
- Any diagram that shows relationships between entities

## Prerequisites

This skill assumes the user has the `d2` CLI installed. If not, they can install it via:
- macOS: `brew install d2`
- Other platforms: See https://d2lang.com/tour/install

## Workflow

### 1. Understand Requirements
- Ask clarifying questions about the diagram's purpose
- Identify key entities and their relationships
- Determine appropriate diagram type and layout

### 2. Create D2 Source File
- Write a `.d2` file with clear, descriptive D2 syntax
- Use appropriate shapes for different entity types
- Add meaningful labels and connections
- Apply styling and layout options as needed

### 3. Generate Diagram
- Run `d2 <filename>.d2 <output>.svg` to generate SVG output
- Alternative formats: PNG (`--output=<file>.png`), PDF
- Use layout engine flags if needed: `--layout=elk` or `--layout=tala`
- Apply themes with `--theme=<theme-name>` if desired

### 4. Review and Iterate
- Show the generated file path to the user
- Be ready to modify the D2 source based on feedback
- Regenerate after making changes

## D2 Syntax Reference

### Basic Shapes and Connections
```d2
# Simple connection
server -> database: queries

# Shape with custom label
api: "API Server"

# Shape with type
cache.shape: cylinder
user.shape: person
```

### Shape Types
Common shapes: `rectangle` (default), `square`, `circle`, `oval`, `diamond`, `hexagon`, `cylinder`, `cloud`, `person`, `package`, `queue`, `document`

### Containers (Grouping)
```d2
network: {
  web_server
  app_server
  database

  web_server -> app_server
  app_server -> database
}
```

### Styling
```d2
server: {
  shape: rectangle
  style: {
    fill: "#4A90E2"
    stroke: "#2E5C8A"
    font-color: white
  }
}
```

### Direction and Layout
```d2
direction: right  # or: left, up, down

# Use layout engines for different results
# CLI: --layout=dagre (default), --layout=elk, --layout=tala
```

### Comments
```d2
# Single line comment
```

## Layout Engines

- **dagre** (default): Fast, hierarchical layouts for directed graphs
- **elk**: Mature engine with good maintenance and features
- **tala**: Best for software architecture diagrams, most customization

Specify via: `d2 --layout=elk diagram.d2 output.svg`

## Themes

Apply professional themes with `--theme` flag:
```bash
d2 --theme=0 diagram.d2 output.svg  # Neutral default
d2 --theme=1 diagram.d2 output.svg  # Neutral grey
d2 --theme=3 diagram.d2 output.svg  # Cool classic
d2 --theme=4 diagram.d2 output.svg  # Mixed berry blue
```

For dark mode support: `--dark-theme=<theme-id>`

## Best Practices

1. **Start Simple**: Begin with basic shapes and connections, then add detail
2. **Use Containers**: Group related entities for clarity
3. **Meaningful Names**: Use descriptive IDs and labels
4. **Consistent Styling**: Apply styles consistently across similar entities
5. **Test Layout**: Try different layout engines if default doesn't work well
6. **Readable Labels**: Keep labels concise but informative
7. **Verify Output**: Always generate and verify the diagram looks correct

## Common Patterns

### System Architecture
```d2
users: Users {shape: person}
lb: "Load Balancer" {shape: hexagon}
app: "Application" {shape: rectangle}
db: "Database" {shape: cylinder}
cache: "Redis" {shape: cylinder}

users -> lb: HTTPS
lb -> app: distributes
app -> db: reads/writes
app -> cache: caches
```

### Sequence Flow
```d2
direction: right
client -> server: 1. Request
server -> database: 2. Query
database -> server: 3. Result
server -> client: 4. Response
```

### Network Topology
```d2
internet: Internet {shape: cloud}
firewall: Firewall {shape: hexagon}
dmz: DMZ {
  web1: "Web Server 1"
  web2: "Web Server 2"
}
internal: Internal Network {
  app_servers: "App Tier"
  db_servers: "DB Tier" {shape: cylinder}
}

internet -> firewall
firewall -> dmz.web1
firewall -> dmz.web2
dmz.web1 -> internal.app_servers
dmz.web2 -> internal.app_servers
internal.app_servers -> internal.db_servers
```

## Troubleshooting

- **d2 command not found**: User needs to install D2 CLI
- **Layout issues**: Try different layout engines (elk or tala)
- **Overlapping elements**: Use containers or adjust with layout engine flags
- **Syntax errors**: Check for proper quoting of labels with spaces
- **File not found**: Ensure working directory is correct

## Example Usage

When a user requests "Create a diagram showing a microservices architecture with API gateway, auth service, and user service":

1. Create `microservices.d2` with appropriate D2 code
2. Run `d2 microservices.d2 microservices.svg`
3. Inform user of the generated file location
4. Iterate based on feedback

## Additional Resources

- D2 Documentation: https://d2lang.com/tour/intro
- Playground: https://play.d2lang.com (for testing syntax)
- Shape reference: https://d2lang.com/tour/shapes
- Styling guide: https://d2lang.com/tour/style
