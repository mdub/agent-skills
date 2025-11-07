# D2 Advanced Features Reference

This document covers advanced D2 features for power users who need more control over their diagrams.

## Table of Contents

- [Variables](#variables)
- [Globs](#globs)
- [Comments](#comments)
- [Overrides](#overrides)
- [Classes](#classes)
- [Legend](#legend)
- [Themes](#themes)
- [Advanced Style Properties](#advanced-style-properties)
- [Dimensions](#dimensions)
- [Positioning](#positioning)
- [Sketch Mode](#sketch-mode)

## Variables

Variables allow you to define reusable values and reference them throughout your diagram.

### Basic Variable Syntax

```d2
vars: {
  primary-color: "#3498DB"
  database-color: "#E74C3C"
  service-name: "User Service"
}

# Reference variables with ${variable-name}
api: ${service-name} {
  style.fill: ${primary-color}
}

db: Database {
  shape: cylinder
  style.fill: ${database-color}
}
```

### Nested Variables

Variables support dot notation for accessing nested properties:

```d2
vars: {
  config: {
    database: {
      host: "db.example.com"
      port: "5432"
    }
    colors: {
      primary: "#3498DB"
      secondary: "#2ECC71"
    }
  }
}

db: Database at ${config.database.host}:${config.database.port} {
  style.fill: ${config.colors.primary}
}
```

### Variable Scoping

Variables follow programming language scoping rules:
- Inner scopes can access outer scope variables
- Outer scopes cannot access inner scope variables
- Nearest definition takes precedence when names conflict

```d2
vars: {
  color: "blue"
}

container: {
  vars: {
    color: "red"  # Overrides outer color
  }
  item: Thing {
    style.fill: ${color}  # Uses "red"
  }
}

other: Another {
  style.fill: ${color}  # Uses "blue"
}
```

### Bypassing Substitutions

Use single quotes to treat `${}` as literal text:

```d2
example: 'Variables use ${variable-name} syntax'
```

### Spread Operator

The spread syntax `...${variable}` expands map or array contents:

```d2
vars: {
  common-styles: {
    fill: "#3498DB"
    stroke: "#2C3E50"
    stroke-width: 2
  }
}

service1: {
  style: {
    ...${common-styles}
  }
}

service2: {
  style: {
    ...${common-styles}
    fill: "#E74C3C"  # Override one property
  }
}
```

### Configuration Variables

Special configuration variables can be set in `vars`:

```d2
vars: {
  theme-id: 3
  pad: 100
}
```

Precedence order: command-line flags > environment variables > file variables

## Globs

Globs enable making global changes to multiple objects with a single statement.

### Basic Glob Syntax

```d2
# Style all objects starting with "service"
*.service*: {
  style.fill: "#3498DB"
  shape: rectangle
}

# Create this after the glob
service1
service2
serviceAuth

# All three will automatically get the styling
```

### Key Characteristics

1. **Bidirectional**: Globs apply to both existing and future objects
2. **Case-insensitive**: Patterns match regardless of capitalization
3. **Multiple globs**: You can define multiple independent glob rules

### Connection Globs

Automatically create connections between matching objects:

```d2
# Define services
serviceA
serviceB
serviceC

database

# Connect all services to database
*.service* -> database: queries
```

### Scoped Globs

Globs respect container boundaries:

```d2
backend: {
  *.service*: {
    style.fill: "#3498DB"
  }

  authService
  userService
}

frontend: {
  # backend glob doesn't affect these
  frontendService
}
```

### Glob Patterns

```d2
# Wildcard matching
*service*         # Matches anything containing "service"
service*          # Matches anything starting with "service"
*service          # Matches anything ending with "service"

# Combine with containers
backend.*: {      # All objects in backend container
  style.opacity: 0.8
}
```

## Comments

D2 supports two types of comments.

### Line Comments

```d2
# This is a line comment
server -> database  # Comment at end of line
```

### Block Comments

```d2
"""
This is a block comment
spanning multiple lines
"""

server: {
  """
  This container represents
  the application server tier
  """
  app1
  app2
}
```

## Overrides

Overrides allow you to modify previously defined elements by redeclaring them.

### Basic Override Syntax

```d2
# Initial declaration
server: Web Server {
  style.fill: "#3498DB"
}

# Override to change properties
server: {
  style.fill: "#E74C3C"
  shape: hexagon
}
```

### Nulling Values

Set attributes to `null` to delete shapes, connections, or properties:

```d2
# Remove a shape entirely
old_server: null

# Delete a connection
client -> old_server: null

# Clear a specific property
server.style.fill: null
```

### Cascading Behavior

- **Cascading deletion**: Nulling a shape also nulls all its connections
- **Descendant removal**: Nulling a container nulls all children

```d2
container: {
  item1
  item2
  item3
}

# This removes container and all items
container: null
```

### Use Cases

**Importing and Filtering**
```d2
# Import a diagram
...@imported-diagram

# Remove unwanted elements
unwanted_element: null
```

**Batch with Exceptions**
```d2
# Connect all services to database
*.service* -> database

# Except this one
legacyService -> database: null
```

## Classes

Classes allow you to aggregate and reuse style attributes.

### Basic Class Syntax

```d2
classes: {
  primary-service: {
    style: {
      fill: "#3498DB"
      stroke: "#2C3E50"
      stroke-width: 2
    }
  }

  database-style: {
    shape: cylinder
    style: {
      fill: "#E74C3C"
      stroke: "#C0392B"
    }
  }
}

# Apply classes
authService: {
  class: primary-service
}

userDB: {
  class: database-style
}
```

### Connection Classes

```d2
classes: {
  critical-path: {
    style: {
      stroke: "#E74C3C"
      stroke-width: 3
      animated: true
    }
  }
}

# Apply to connection
client -> server: {
  class: critical-path
}

# Or apply after declaration
a -> b
a -> b.class: critical-path
```

### Multiple Classes

Apply multiple classes to combine styles:

```d2
classes: {
  base-service: {
    shape: rectangle
    style.fill: "#3498DB"
  }

  critical: {
    style.stroke: "#E74C3C"
    style.stroke-width: 3
  }
}

# Classes applied left-to-right
importantService: {
  class: [base-service, critical]
}
```

### Overriding Class Attributes

Object attributes override class attributes:

```d2
classes: {
  service: {
    style.fill: "#3498DB"
    style.stroke: "#2C3E50"
  }
}

normalService: {
  class: service
  # Uses class colors
}

specialService: {
  class: service
  style.fill: "#E74C3C"  # Overrides class fill
  # Still uses class stroke
}
```

### Classes in SVG Output

Classes are written to SVG as `class` attributes, enabling CSS styling and JavaScript:

```d2
classes: {
  interactive: {
    style.fill: "#3498DB"
  }
}

button: {
  class: interactive
}
```

The SVG output will include `class="interactive"`, allowing external styling.

## Legend

Legends provide visual explanations of diagram symbols and conventions.

### Basic Legend Syntax

```d2
d2-legend: {
  # Define legend items
  microservice: Microservice {
    shape: rectangle
    style.fill: "#3498DB"
  }

  database: Database {
    shape: cylinder
    style.fill: "#E74C3C"
  }

  external: External Service {
    shape: cloud
    style.fill: "#95A5A6"
  }
}
```

### Legend with Connections

```d2
d2-legend: {
  good: Good Relationship -> {
    style.stroke: "#27AE60"
    style.stroke-width: 2
  }

  bad: Bad Relationship -> {
    style.stroke: "#E74C3C"
    style.stroke-dash: 3
  }
}
```

### Hiding Shapes in Legend

To show only the connection (not shapes) in the legend:

```d2
d2-legend: {
  a: {style.opacity: 0}
  b: {style.opacity: 0}
  a -> b: Connection Type
}
```

### Custom Legend Label

```d2
d2-legend: {
  label: "Diagram Key"  # Custom legend title

  item1: Description 1
  item2: Description 2
}
```

### Practical Legend Example

```d2
d2-legend: {
  label: "Architecture Legend"

  service: Microservice {
    shape: rectangle
    style.fill: "#3498DB"
  }

  db: Database {
    shape: cylinder
    style.fill: "#E74C3C"
  }

  sync: Synchronous Call -> {
    style.stroke: "#2C3E50"
    style.stroke-width: 2
  }

  async: Asynchronous Call -> {
    style.stroke: "#95A5A6"
    style.stroke-dash: 5
  }
}

# Actual diagram follows
api: API Gateway {shape: hexagon}
userService: User Service {style.fill: "#3498DB"}
database: Database {shape: cylinder; style.fill: "#E74C3C"}

api -> userService: {style.stroke: "#2C3E50"; style.stroke-width: 2}
userService -> database: {style.stroke: "#2C3E50"; style.stroke-width: 2}
```

## Advanced Style Properties

Complete list of style properties available in D2.

### Visual Properties

```d2
shape: {
  style: {
    # Transparency
    opacity: 0.8                    # 0.0 to 1.0

    # Colors and fills
    stroke: "#2C3E50"               # Outline color
    fill: "#3498DB"                 # Fill color (shapes only)
    fill-pattern: "dots"            # Pattern options

    # Stroke styling
    stroke-width: 2                 # Outline thickness
    stroke-dash: 5                  # Dashed line (number or "5 5")

    # Shape styling
    border-radius: 8                # Corner rounding
    shadow: true                    # Drop shadow (shapes only)
    3d: true                        # 3D effect (rectangles/squares)
    multiple: true                  # Multiple outline
    double-border: true             # Double outline (rectangles/ovals)
  }
}
```

### Typography

```d2
shape: {
  style: {
    font: "Helvetica"
    font-size: 16
    font-color: "#FFFFFF"
    bold: true
    italic: false
    underline: false
    text-transform: "uppercase"     # or "lowercase", "capitalize", "none"
  }
}
```

### Animation

```d2
connection: {
  style: {
    animated: true
  }
}
```

### Gradient Fills

```d2
shape: {
  style: {
    fill: "linear-gradient(to bottom, #3498DB, #2980B9)"
  }
}
```

### Pattern Fills

Available fill patterns:
- `dots`
- `lines`
- `grain`

```d2
shape: {
  style: {
    fill-pattern: "dots"
  }
}
```

### Special Shape Styling

For `sql_table` and `class` shapes:
- `stroke` applies as fill to the body
- `fill` controls header appearance

## Best Practices

1. **Use variables for consistency**: Define colors and common values once
2. **Apply globs for themes**: Style groups of similar objects together
3. **Leverage classes for reusability**: Create reusable style sets
4. **Document with comments**: Explain complex logic and relationships
5. **Use legends for clarity**: Help viewers understand diagram conventions
6. **Override judiciously**: Keep overrides organized and documented
7. **Scope variables and globs**: Use containers to limit scope and avoid conflicts

## Common Patterns

### Theme Management with Variables

```d2
vars: {
  theme: {
    primary: "#3498DB"
    secondary: "#2ECC71"
    error: "#E74C3C"
    text: "#2C3E50"
  }
}

classes: {
  service: {
    style: {
      fill: ${theme.primary}
      font-color: white
    }
  }
  error-state: {
    style: {
      stroke: ${theme.error}
      stroke-width: 3
    }
  }
}
```

### Consistent Service Styling with Globs

```d2
# Style all services consistently
*.service*: {
  shape: rectangle
  style: {
    fill: "#3498DB"
    stroke: "#2C3E50"
    stroke-width: 2
  }
}

authService
userService
orderService
# All automatically styled
```

### Complex Legend

```d2
d2-legend: {
  label: "System Architecture Legend"

  shapes: Shapes {
    service: Service {shape: rectangle; style.fill: "#3498DB"}
    database: Database {shape: cylinder; style.fill: "#E74C3C"}
    external: External {shape: cloud; style.fill: "#95A5A6"}
  }

  connections: Connections {
    sync: {style.opacity: 0}
    syncTarget: {style.opacity: 0}
    sync -> syncTarget: Synchronous {style.stroke: "#2C3E50"}

    async: {style.opacity: 0}
    asyncTarget: {style.opacity: 0}
    async -> asyncTarget: Async {style.stroke-dash: 5}
  }
}
```

## Themes

D2 includes numerous built-in themes to make diagrams look professional and ready for blogs, wikis, and presentations.

### Available Themes

D2 provides many themes with different color schemes and styles. Some examples include:
- **Neutral default** (theme 0)
- **Neutral grey** (theme 1)
- **Cool classic** (theme 3)
- **Mixed berry blue** (theme 4)
- **Grape soda** (theme 5)
- **Vanilla nitro cola**
- And many more...

### Applying Themes via CLI

Use the `-t` or `--theme` flag to apply a theme:

```bash
# Neutral default
d2 --theme=0 diagram.d2 output.svg

# Neutral grey
d2 --theme=1 diagram.d2 output.svg

# Cool classic
d2 --theme=3 diagram.d2 output.svg

# Mixed berry blue
d2 --theme=4 diagram.d2 output.svg

# Grape soda
d2 --theme=5 diagram.d2 output.svg
```

To list all available themes:
```bash
d2 themes
```

### Applying Themes in Diagram File

You can set the theme using variables:

```d2
vars: {
  theme-id: 3
}

# Diagram content
server -> database
```

**Precedence order**: CLI flags > environment variables > file variables

### Dark Mode Support

By default, diagrams maintain consistent appearance regardless of system settings. To enable automatic dark theme switching based on user system preferences:

```bash
d2 --theme=0 --dark-theme=200 diagram.d2 output.svg
```

This allows diagrams to automatically adapt when users view them in dark mode.

### Theme Customization

Customize theme colors using override variables:

```d2
vars: {
  theme-overrides: {
    N1: "#FFFFFF"
    N7: "#000000"
    B1: "#3498DB"
    B2: "#2980B9"
  }
}
```

For dark themes:

```d2
vars: {
  dark-theme-overrides: {
    N1: "#1E1E1E"
    N7: "#FFFFFF"
    B1: "#5DADE2"
  }
}
```

### Color Codes

D2 uses standardized color codes:
- **N1-N7**: Neutral colors (grayscale)
- **B1-B6**: Primary brand colors
- **AA2-AA5**: Accent color variations
- **AB4-AB5**: Secondary accent variations

### Special Themes

**Terminal Theme**

The Terminal theme applies multiple formatting defaults simultaneously:
- UPPERCASE labels on all text
- No border radius (square corners)
- Monospaced fonts
- Dot fill patterns for containers
- Double borders on outer containers

```bash
d2 --theme=terminal diagram.d2 output.svg
```

This is useful for creating diagrams with a retro/technical aesthetic.

## Dimensions

Control the size of shapes with width and height properties.

### Setting Width and Height

```d2
server: {
  width: 200
  height: 100
}

database: {
  shape: cylinder
  width: 150
  height: 150
}
```

### Important Limitations

**Containers cannot have dimensions set** - they automatically resize to fit their children:

```d2
# This works
standalone_shape: {
  width: 100
  height: 50
}

# This does NOT work - will be ignored
container: {
  width: 300   # Ignored - containers auto-size
  height: 200  # Ignored - containers auto-size

  child1
  child2
}
```

### Use Cases

- **Fixed-size elements**: Ensure consistent sizing across diagrams
- **Emphasizing importance**: Make critical components larger
- **Visual hierarchy**: Size shapes proportionally to their significance
- **Aspect ratio control**: Maintain specific shape proportions

### Example with Consistent Sizing

```d2
classes: {
  microservice: {
    width: 150
    height: 80
    style.fill: "#3498DB"
  }

  database: {
    width: 120
    height: 120
    shape: cylinder
    style.fill: "#E74C3C"
  }
}

authService: {class: microservice}
userService: {class: microservice}
orderService: {class: microservice}

userDB: {class: database}
orderDB: {class: database}
```

## Positioning

D2's layout engines automatically position elements, but you can exert manual control using the `near` keyword.

### Near Positioning

Position elements at preset points in your diagram:

**Available positions:**
- `top-left`, `top-center`, `top-right`
- `center-left`, `center-right`
- `bottom-left`, `bottom-center`, `bottom-right`

### Basic Positioning Syntax

```d2
title: "System Architecture" {
  near: top-center
  shape: text
  style: {
    font-size: 24
    bold: true
  }
}

legend_note: "Updated: 2024-01" {
  near: bottom-right
  shape: text
  style.font-size: 12
}
```

### Common Use Cases

#### 1. Diagram Titles

```d2
diagram_title: "Microservices Architecture" {
  near: top-center
  shape: text
  style: {
    font-size: 28
    bold: true
    underline: true
  }
}

# Main diagram content
api -> services -> database
```

#### 2. Legends

```d2
d2-legend: {
  near: bottom-left

  service: Service {shape: rectangle; style.fill: "#3498DB"}
  database: Database {shape: cylinder; style.fill: "#E74C3C"}
}
```

#### 3. Notes and Descriptions

```d2
description: {
  near: top-left
  shape: text
  label: |md
    # Notes
    - Production environment
    - Auto-scaling enabled
    - Multi-region deployment
  |
}
```

### Label and Icon Positioning

Position labels and icons relative to their parent elements:

```d2
server: Web Server {
  icon: https://icons.com/server.svg
  icon.near: top-left

  label.near: bottom-center
}
```

### Key Benefits

The `near` positioning approach allows:
- Diagram-level element placement without disrupting automatic layout
- Consistent positioning across diagram updates
- Easy addition of metadata, titles, and annotations
- Separation of decorative elements from structural elements

### Best Practice

"In general, positioning is controlled entirely by the layout engine. It's one of the primary benefits of text-to-diagram that you don't have to manually define all the positions of objects."

Use `near` positioning sparingly - primarily for titles, legends, and annotations rather than core diagram structure.

## Sketch Mode

Sketch mode renders diagrams with a hand-drawn aesthetic for a more casual, informal appearance.

### Enabling Sketch Mode

Apply sketch mode via CLI flag:

```bash
d2 --sketch diagram.d2 output.svg
```

Or set it in the diagram file:

```d2
vars: {
  sketch: true
}

# Diagram content
client -> server -> database
```

### What Sketch Mode Does

Sketch mode transforms standard precise diagrams into hand-drawn style:
- Lines appear slightly wavy/irregular
- Shapes have hand-drawn appearance
- Text maintains readability with slight variations
- Overall effect is more casual and informal

### Use Cases

**1. Preliminary Designs**
```bash
# Early-stage mockups
d2 --sketch mockup.d2 wireframe.svg
```

**2. Informal Documentation**
```d2
vars: {
  sketch: true
}

# Internal documentation with friendly feel
team_structure: Team Structure {
  engineers
  designers
  product
}
```

**3. Presentation Slides**
```bash
# Make technical diagrams more approachable
d2 --sketch --theme=3 architecture.d2 presentation.svg
```

**4. Whiteboard-style Diagrams**
```d2
vars: {
  sketch: true
  theme-id: 1
}

# Brainstorming session visualization
idea1 -> idea2 -> final_concept
```

### Combining with Other Features

Sketch mode works well with other customization:

```d2
vars: {
  sketch: true
  theme-id: 3
}

classes: {
  component: {
    style: {
      fill: "#FFF9E6"
      stroke: "#8B4513"
    }
  }
}

# Hand-drawn style with custom colors
frontend: {class: component}
backend: {class: component}
database: {class: component}

frontend -> backend -> database
```

### When to Use Sketch Mode

**Good for:**
- Casual presentations
- Internal documentation
- Early-stage designs
- Brainstorming visualizations
- Less formal contexts

**Avoid for:**
- Official documentation
- Client-facing deliverables
- Highly technical specifications
- Situations requiring precision

## Best Practices Summary

1. **Themes**: Choose appropriate themes for your context (professional vs. casual)
2. **Dimensions**: Set explicit sizes for consistency, but let containers auto-size
3. **Positioning**: Use `near` for metadata; let layout engines handle structure
4. **Sketch Mode**: Reserve for informal contexts; use standard rendering for formal docs
5. **Combine features**: Themes + classes + variables create consistent, maintainable diagrams
6. **Test outputs**: Always preview diagrams after applying customization
7. **Document choices**: Comment your theme/style decisions in the D2 file
