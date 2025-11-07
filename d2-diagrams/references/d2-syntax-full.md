# D2 Syntax Full Reference

Complete reference for D2 diagram syntax and features.

## Basic Shapes and Connections

### Simple Connections
```d2
# Basic connection
server -> database: queries

# Bidirectional
client <-> server: requests/responses

# Multiple connections
client -> server
server -> database
server -> cache
```

### Shapes with Labels
```d2
# Shape with custom label
api: "API Server"
db: "PostgreSQL Database"

# Label with quotes (for spaces)
load_balancer: "Load Balancer"
```

### Shape Types
```d2
# Specify shape type
cache.shape: cylinder
user.shape: person
cloud_service.shape: cloud
storage.shape: cylinder
gateway.shape: hexagon
document.shape: document
queue.shape: queue
package.shape: package
```

### Available Shape Types

- `rectangle` (default)
- `square`
- `circle`
- `oval`
- `diamond`
- `hexagon`
- `cylinder` - Good for databases and caches
- `cloud` - Good for cloud services
- `person` - Good for users/actors
- `package` - Good for modules/packages
- `queue` - Good for message queues
- `document` - Good for files/documents
- `parallelogram` - Good for data/input
- `callout` - Good for annotations
- `stored_data` - Alternative database representation

## Containers (Grouping)

### Basic Container
```d2
network: {
  web_server
  app_server
  database

  web_server -> app_server
  app_server -> database
}
```

### Nested Containers
```d2
infrastructure: {
  frontend: Frontend Tier {
    web1: Web Server 1
    web2: Web Server 2
  }
  backend: Backend Tier {
    app1: App Server 1
    app2: App Server 2
    db: Database {shape: cylinder}
  }

  frontend.web1 -> backend.app1
  frontend.web2 -> backend.app2
  backend.app1 -> backend.db
  backend.app2 -> backend.db
}
```

### Container with Label
```d2
vpc: "AWS VPC" {
  public_subnet: "Public Subnet" {
    nat_gateway
    load_balancer
  }
  private_subnet: "Private Subnet" {
    application_servers
    database
  }
}
```

## Styling

### Shape Styling
```d2
server: {
  shape: rectangle
  style: {
    fill: "#4A90E2"
    stroke: "#2E5C8A"
    stroke-width: 2
    font-color: white
    font-size: 16
    bold: true
    italic: false
    underline: false
  }
}
```

### Connection Styling
```d2
client -> server: {
  style: {
    stroke: "#E74C3C"
    stroke-width: 3
    stroke-dash: 5
    font-color: "#E74C3C"
  }
}
```

### Common Color Schemes
```d2
# Blue theme
primary: {style.fill: "#4A90E2"}
secondary: {style.fill: "#AED6F1"}

# Red/Error theme
error: {style.fill: "#E74C3C"}
warning: {style.fill: "#F39C12"}

# Green/Success theme
success: {style.fill: "#27AE60"}
info: {style.fill: "#3498DB"}
```

## Direction and Layout

### Setting Direction
```d2
direction: right  # Flows left to right
direction: left   # Flows right to left
direction: up     # Flows bottom to top
direction: down   # Flows top to bottom (default)
```

### Layout Per Container
```d2
services: {
  direction: right

  api -> processor -> storage
}

users: {
  direction: down

  admin
  regular_user
  guest
}
```

## Connection Types

### Arrow Types
```d2
# Directed (default)
a -> b

# Bidirectional
a <-> b

# Undirected
a -- b

# Reverse direction
a <- b
```

### Connection Labels
```d2
client -> server: HTTPS request
server -> database: SQL query {
  style.stroke: "#3498DB"
}
```

## Advanced Features

### Multiple Labels on Shapes
```d2
database: {
  shape: cylinder
  label: "PostgreSQL"
  style.fill: "#336791"
}
```

### Icons and Special Characters
D2 supports Unicode, allowing for special characters in labels:
```d2
success: "✓ Completed"
warning: "⚠ Warning"
error: "✗ Failed"
```

### Comments
```d2
# Single line comment

# Multi-line comments are just
# multiple single-line comments
```

### Semicolons for Compact Syntax
```d2
# Compact declaration
web; app; db

# Equivalent to:
# web
# app
# db
```

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

app.style.fill: "#3498DB"
db.style.fill: "#E74C3C"
cache.style.fill: "#E67E22"
```

### Sequence Flow
```d2
direction: right

client -> server: 1. Request
server -> database: 2. Query
database -> server: 3. Result
server -> client: 4. Response

# Add styling for clarity
client.shape: person
database.shape: cylinder
```

### Network Topology
```d2
internet: Internet {shape: cloud}
firewall: Firewall {shape: hexagon}

dmz: DMZ {
  web1: "Web Server 1"
  web2: "Web Server 2"

  web1.style.fill: "#3498DB"
  web2.style.fill: "#3498DB"
}

internal: Internal Network {
  app_servers: "App Tier"
  db_servers: "DB Tier" {shape: cylinder}

  app_servers.style.fill: "#27AE60"
  db_servers.style.fill: "#E74C3C"
}

internet -> firewall
firewall -> dmz.web1
firewall -> dmz.web2
dmz.web1 -> internal.app_servers
dmz.web2 -> internal.app_servers
internal.app_servers -> internal.db_servers
```

### Microservices Architecture
```d2
direction: right

api_gateway: "API Gateway" {
  shape: hexagon
  style.fill: "#9B59B6"
}

services: Microservices {
  auth: "Auth Service" {style.fill: "#3498DB"}
  user: "User Service" {style.fill: "#3498DB"}
  order: "Order Service" {style.fill: "#3498DB"}
  payment: "Payment Service" {style.fill: "#3498DB"}
}

data: Data Layer {
  user_db: "User DB" {shape: cylinder; style.fill: "#E74C3C"}
  order_db: "Order DB" {shape: cylinder; style.fill: "#E74C3C"}
  cache: "Redis Cache" {shape: cylinder; style.fill: "#E67E22"}
}

api_gateway -> services.auth
api_gateway -> services.user
api_gateway -> services.order
api_gateway -> services.payment

services.auth -> data.user_db
services.user -> data.user_db
services.order -> data.order_db
services.payment -> data.order_db

services.user -> data.cache
services.order -> data.cache
```

### Entity Relationship Diagram
```d2
users: Users {
  shape: rectangle
  style.fill: "#3498DB"
}

posts: Posts {
  shape: rectangle
  style.fill: "#3498DB"
}

comments: Comments {
  shape: rectangle
  style.fill: "#3498DB"
}

users -> posts: "has many"
users -> comments: "has many"
posts -> comments: "has many"
posts -> users: "belongs to"
comments -> posts: "belongs to"
comments -> users: "belongs to"
```

## Layout Engines

### Dagre (Default)
Fast, hierarchical layouts for directed graphs. Best for simple diagrams.

```bash
d2 diagram.d2 output.svg
# or explicitly:
d2 --layout=dagre diagram.d2 output.svg
```

### ELK
Mature engine with better maintenance. Good for complex layouts.

```bash
d2 --layout=elk diagram.d2 output.svg
```

### TALA
Best for software architecture diagrams with the most customization options.

```bash
d2 --layout=tala diagram.d2 output.svg
```

## Themes

### Applying Themes
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

### Dark Mode
```bash
# Specify both light and dark themes
d2 --theme=0 --dark-theme=200 diagram.d2 output.svg
```

### Theme Overrides
Themes can be customized using configuration variables, though this requires additional D2 features beyond basic syntax.

## Best Practices

1. **Use meaningful IDs**: `user_service` not `us1`
2. **Group related elements**: Use containers for logical grouping
3. **Consistent styling**: Apply similar styles to similar elements
4. **Label connections**: Make relationships clear with labels
5. **Choose appropriate shapes**: Use cylinder for databases, person for users, etc.
6. **Start simple**: Add complexity gradually
7. **Use direction wisely**: Set direction based on natural flow
8. **Comment complex sections**: Help future maintainers understand intent

## Troubleshooting

### Syntax Errors
- Ensure labels with spaces are quoted: `server: "API Server"`
- Check for proper closing of containers with `}`
- Verify connection syntax uses `->`, `<->`, or `--`

### Layout Issues
- Try different layout engines if elements overlap
- Use containers to group elements that should be close
- Adjust direction to improve flow
- Consider breaking complex diagrams into multiple simpler ones

### Styling Not Applied
- Verify style block syntax: `style.fill` not `style: fill`
- Check color format: Use `#RRGGBB` hex colors
- Ensure style is applied to correct element or connection

## Additional Resources

- Official D2 Tour: https://d2lang.com/tour/intro
- D2 Playground: https://play.d2lang.com
- Shape Gallery: https://d2lang.com/tour/shapes
- Style Reference: https://d2lang.com/tour/style
- Layout Guide: https://d2lang.com/tour/layouts
- Theme Gallery: https://d2lang.com/tour/themes
