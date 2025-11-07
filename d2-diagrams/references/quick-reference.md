# D2 Quick Reference

Essential syntax and pointers to official documentation.

## Official Documentation

**Primary resource**: [D2 Language Tour](https://d2lang.com/tour/intro)

This quick reference provides frequently-used patterns. For comprehensive documentation, refer to the official D2 tour linked throughout.

## Basic Syntax

### Shapes and Connections

```d2
# Simple connection
server -> database

# Connection with label
server -> database: queries

# Bidirectional
client <-> server

# Shape with custom label
api: "API Server"

# Shape with type
cache.shape: cylinder
user.shape: person
cloud_service.shape: cloud
```

📖 [Shapes](https://d2lang.com/tour/shapes) | [Connections](https://d2lang.com/tour/connections)

### Containers (Grouping)

```d2
vpc: AWS VPC {
  frontend: Frontend {
    web1
    web2
  }
  backend: Backend {
    app1
    app2
    database.shape: cylinder
  }

  frontend.web1 -> backend.app1
  frontend.web2 -> backend.app2
}
```

📖 [Containers](https://d2lang.com/tour/containers)

## Common Shape Types

```d2
# Most frequently used
server: {shape: rectangle}      # Default
db: {shape: cylinder}           # Databases
user: {shape: person}           # Users/actors
gateway: {shape: hexagon}       # Gateways/load balancers
external: {shape: cloud}        # External services
decision: {shape: diamond}      # Decision points
queue: {shape: queue}           # Message queues
```

📖 [All shape types](https://d2lang.com/tour/shapes)

## Styling

### Basic Styling

```d2
service: {
  style: {
    fill: "#3498DB"
    stroke: "#2C3E50"
    stroke-width: 2
    font-color: white
    bold: true
  }
}

# Connection styling
a -> b: {
  style: {
    stroke: "#E74C3C"
    stroke-width: 3
    stroke-dash: 5
  }
}
```

📖 [Style](https://d2lang.com/tour/style) | [Themes](https://d2lang.com/tour/themes)

### Themes

Apply professional themes via CLI:

```bash
d2 --theme=0 diagram.d2 output.svg     # Neutral default
d2 --theme=1 diagram.d2 output.svg     # Neutral grey
d2 --theme=3 diagram.d2 output.svg     # Cool classic
d2 --theme=4 diagram.d2 output.svg     # Mixed berry blue

# List all themes
d2 themes
```

📖 [Themes guide](https://d2lang.com/tour/themes)

## Layout and Direction

```d2
# Set flow direction
direction: right   # left, right, up, down

# Layout engines
# CLI: d2 --layout=<engine> diagram.d2 output.svg

# dagre (default) - Fast, hierarchical
# elk - Mature, complex layouts
# tala - Software architecture
```

📖 [Layouts](https://d2lang.com/tour/layouts)

## Advanced Features

### Variables

```d2
vars: {
  primary-color: "#3498DB"
  db-color: "#E74C3C"
}

service: {
  style.fill: ${primary-color}
}

database: {
  shape: cylinder
  style.fill: ${db-color}
}
```

📖 [Variables](https://d2lang.com/tour/vars)

### Classes (Reusable Styles)

```d2
classes: {
  service: {
    shape: rectangle
    style.fill: "#3498DB"
  }
  database: {
    shape: cylinder
    style.fill: "#E74C3C"
  }
}

authService: {class: service}
userService: {class: service}
userDB: {class: database}
```

📖 [Classes](https://d2lang.com/tour/classes)

### Globs (Batch Styling)

```d2
# Style all services
*.service*: {
  style.fill: "#3498DB"
  shape: rectangle
}

authService
userService
orderService
# All automatically styled
```

📖 [Globs](https://d2lang.com/tour/globs)

### Imports

```d2
# Import entire file
common: @shared-components.d2

# Spread import (merge into current map)
...@base-infrastructure.d2

# Partial import
managers: @org-chart.managers
```

📖 [Imports](https://d2lang.com/tour/imports)

### Composition (Multiple Boards)

```d2
# Layers - independent boards
layers: {
  production: {
    # Production setup
  }
  staging: {
    # Staging setup
  }
}

# Scenarios - inherit from base
scenarios: {
  normal: {
    # Normal flow
  }
  failure: {
    # Failure scenario
  }
}

# Steps - sequential progression
steps: {
  1: {
    # Step 1
  }
  2: {
    # Step 2 (inherits from step 1)
  }
}
```

📖 [Composition](https://d2lang.com/tour/composition) | [Scenarios](https://d2lang.com/tour/scenarios) | [Layers](https://d2lang.com/tour/layers)

### Legend

```d2
d2-legend: {
  service: Service {
    shape: rectangle
    style.fill: "#3498DB"
  }
  database: Database {
    shape: cylinder
    style.fill: "#E74C3C"
  }
}
```

📖 [Legend](https://d2lang.com/tour/legend)

### Positioning

```d2
title: "Architecture Diagram" {
  near: top-center
  shape: text
  style.font-size: 24
}

# Available: top-left, top-center, top-right,
#            center-left, center-right,
#            bottom-left, bottom-center, bottom-right
```

📖 [Positions](https://d2lang.com/tour/positions)

### Dimensions

```d2
server: {
  width: 200
  height: 100
}

# Note: Containers auto-size and ignore width/height
```

📖 [Dimensions](https://d2lang.com/tour/dimensions)

## Special Features

### Sketch Mode (Hand-drawn)

```bash
d2 --sketch diagram.d2 output.svg
```

📖 [Sketch mode](https://d2lang.com/tour/sketch)

### Sequence Diagrams

```d2
shape: sequence_diagram
alice -> bob: Hello
bob -> alice: Hi there
```

📖 [Sequence diagrams](https://d2lang.com/tour/sequence-diagrams)

### SQL Tables

```d2
users: {
  shape: sql_table
  id: int {constraint: primary_key}
  name: varchar
  email: varchar
}
```

📖 [SQL tables](https://d2lang.com/tour/sql-tables)

### UML Classes

```d2
MyClass: {
  shape: class

  -privateField: int
  +publicField: string

  +publicMethod()
  -privateMethod()
}
```

📖 [UML classes](https://d2lang.com/tour/uml-classes)

## Common Patterns

### Microservices Architecture

```d2
users.shape: person
gateway.shape: hexagon

users -> gateway: HTTPS
gateway -> authService: authenticate
gateway -> userService: user ops

authService -> database.shape: cylinder
userService -> cache.shape: cylinder
```

### Network Topology

```d2
internet.shape: cloud
firewall.shape: hexagon

dmz: DMZ {
  web1; web2
}

internal: Internal {
  app.shape: rectangle
  db.shape: cylinder
}

internet -> firewall -> dmz.web1
dmz.web1 -> internal.app
internal.app -> internal.db
```

### Deployment Pipeline

```d2
direction: right

code -> build -> test -> deploy

code.shape: document
deploy.shape: cloud
```

## CLI Commands

```bash
# Generate diagram
d2 diagram.d2 output.svg

# With theme
d2 --theme=3 diagram.d2 output.svg

# With layout engine
d2 --layout=elk diagram.d2 output.svg

# Sketch mode
d2 --sketch diagram.d2 output.svg

# Output formats
d2 diagram.d2 output.png
d2 diagram.d2 output.pdf
d2 diagram.d2 output.svg

# List available themes
d2 themes

# List available layouts
d2 layout

# Watch mode (auto-regenerate on changes)
d2 --watch diagram.d2 output.svg
```

📖 [CLI reference](https://d2lang.com/tour/help)

## Additional Resources

- **Interactive playground**: [play.d2lang.com](https://play.d2lang.com)
- **Complete tour**: [d2lang.com/tour](https://d2lang.com/tour/intro)
- **Examples gallery**: [d2lang.com/tour/intro#examples](https://d2lang.com/tour/intro#examples)
- **VSCode extension**: [github.com/terrastruct/d2-vscode](https://github.com/terrastruct/d2-vscode)
- **Cheat sheet**: [d2lang.com/tour/cheat-sheet](https://d2lang.com/tour/cheat-sheet)
- **FAQ**: [d2lang.com/tour/faq](https://d2lang.com/tour/faq)
- **Troubleshooting**: [d2lang.com/tour/troubleshoot](https://d2lang.com/tour/troubleshoot)

## Quick Troubleshooting

- **Syntax errors**: Check proper quoting of labels with spaces
- **Layout issues**: Try different layout engines (`--layout=elk` or `--layout=tala`)
- **Overlapping elements**: Use containers to group related elements
- **Style not applying**: Verify syntax (`style.fill` not `style: fill`)
- **Connection issues**: Ensure both endpoints exist before connecting

For detailed troubleshooting: [d2lang.com/tour/troubleshoot](https://d2lang.com/tour/troubleshoot)
