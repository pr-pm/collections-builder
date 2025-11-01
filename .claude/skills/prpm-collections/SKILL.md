---
name: prpm-collections
description: Use when working with PRPM collections - understanding, creating, or managing curated bundles of packages for specific workflows and use cases
---

# PRPM Collections: Definition & Working Knowledge

## What is a Collection?

A **collection** in PRPM is a curated bundle of multiple related packages designed to solve a specific use case or workflow. Think of collections as "starter packs" or "meta-packages" that allow users to install complete development environments with a single command.

**Core Analogy**: Collections are to PRPM packages what project templates are to npm packages - they group related items together for convenience and completeness.

## When to Use This Skill

Use this skill when:
- Creating or modifying PRPM collections
- Explaining what collections are to users
- Deciding whether to create a collection vs. individual packages
- Troubleshooting collection installation or structure
- Reviewing collection quality or curation
- Working with collection APIs or database schema

## Critical Characteristics

### 1. Identity & Metadata
```typescript
{
  id: string;              // Unique identifier (e.g., "nextjs-pro")
  scope: string;           // "collection" (official) or username (community)
  name: string;            // Human-readable title
  description: string;     // Detailed description
  version: string;         // Semantic versioning (e.g., "1.2.0")
  author: string;          // Owner/maintainer
  maintainers: string[];   // Co-maintainers
  official: boolean;       // PRPM team curated
  verified: boolean;       // Verified/trusted author
}
```

**Naming Convention**: `@scope/id`
- Official: `collection/nextjs-pro`
- Community: `@username/my-workflow`

### 2. Classification
```typescript
{
  category: 'development' | 'design' | 'data-science' | 'devops' | 'testing' | 'documentation' | 'general';
  tags: string[];          // Searchable tags (e.g., ["react", "typescript"])
  framework?: string;      // Specific framework (e.g., "nextjs", "vue")
  icon: string;            // Emoji or image URL
  banner?: string;         // Optional header image
  readme?: string;         // Optional markdown documentation
}
```

### 3. Package Contents

**Collections contain 2-15+ related packages:**

```typescript
interface CollectionPackage {
  packageId: string;           // Reference to a PRPM package
  version?: string;            // Specific version or 'latest'
  required: boolean;           // Must include or optional?
  reason?: string;             // Why this package is in the collection
  installOrder?: number;       // Sequential installation order
  formatOverride?: string;     // Force specific format for this package
  formatSpecific?: {           // IDE-specific package variants
    cursor?: string;           // Cursor variant package ID
    claude?: string;           // Claude variant (may include MCP)
    continue?: string;         // Continue variant
    windsurf?: string;         // Windsurf variant
  };
}
```

**Key Rules:**
- Minimum 3 packages per collection
- All packages must exist in registry
- Mix required and optional packages strategically
- Provide clear "reason" for each package's inclusion

### 4. Configuration Options

```typescript
interface CollectionConfig {
  defaultFormat?: 'cursor' | 'claude' | 'continue' | 'windsurf';  // Auto-format
  installOrder?: 'sequential' | 'parallel';                        // Install behavior
  postInstall?: string;                                            // Script to run after
  extends?: string;                                                // Base collection
  mcpServers?: Record<string, MCPServerConfig>;                   // Claude MCP config
}
```

## Format-Aware Installation

Collections automatically adapt to the user's editor environment:

```bash
# Auto-detects environment
prpm install collection/nextjs-pro

# Force specific format
prpm install collection/nextjs-pro --as claude
prpm install collection/nextjs-pro --as cursor

# Skip optional packages
prpm install collection/nextjs-pro --skip-optional

# Interactive selection
prpm install collection/nextjs-pro --customize
```

## IDE-Specific Package Variants

Collections can include different package variants per IDE:

```json
{
  "packageId": "typescript-expert",
  "formatSpecific": {
    "cursor": "typescript-expert",              // Standard rule
    "claude": "typescript-expert-with-mcp",    // Enhanced with MCP
    "continue": "typescript-expert-simple",    // Simplified version
    "windsurf": "typescript-expert"            // Standard rule
  }
}
```

## MCP Server Integration (Claude Code Only)

Collections can include Model Context Protocol servers:

```json
{
  "config": {
    "mcpServers": {
      "pulumi": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-pulumi"],
        "description": "Pulumi infrastructure management",
        "optional": false
      },
      "aws": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-aws"],
        "env": { "AWS_REGION": "us-east-1" },
        "optional": true
      }
    }
  }
}
```

**MCP Integration Rules:**
- Only for Claude Code format
- Can be optional or required
- Must include clear descriptions
- Environment variables supported
- Command must be accessible (npx, npm, system binary)

## Collection Hierarchy & Dependencies

Collections can extend other collections:

```typescript
{
  id: 'enterprise-nextjs',
  extends: 'collection/nextjs-pro',    // Base collection
  additionalPackages: [
    { packageId: 'auth-patterns', version: 'latest' },
    { packageId: 'monitoring-setup', version: '1.0.0' },
  ],
}
```

**Inheritance Rules:**
- Child collection inherits all parent packages
- Child can override parent package versions
- Child can add new packages
- Only one level of extension (no grandchildren)

## Database Structure

Collections are stored across three tables:

### `collections` Table
Primary collection metadata and configuration

### `collection_packages` Table
Junction table linking collections to their constituent packages with relationship metadata (required, reason, install_order)

### `collection_installs` Table
Tracks installation events with user, version, format, and timestamp

**Key Indexes:**
- `(scope, id, version)` - Unique collection identifier
- `category` - Fast filtering by category
- `official, verified` - Quality indicators
- `downloads, stars` - Popularity metrics

## Collections vs. Packages

| Aspect | Package | Collection |
|--------|---------|------------|
| Definition | Single atomic unit (rule, skill, agent) | Bundle of related packages |
| Granularity | One prompt/rule/skill | 2-15+ packages |
| Installation | `prpm install react-best-practices` | `prpm install collection/nextjs-pro` |
| Scope | Solves one specific problem | Solves a complete workflow |
| Analogy | npm module | Project boilerplate |

## Creating Quality Collections

### Minimum Requirements
- [ ] Minimum 3 packages
- [ ] All packages exist in registry
- [ ] Clear, descriptive name
- [ ] Detailed description (2-3 sentences minimum)
- [ ] Appropriate category
- [ ] Relevant tags (3-7 tags)
- [ ] Icon (emoji or image)
- [ ] Each package has "reason" field

### Quality Guidelines
- [ ] Packages are genuinely related and complementary
- [ ] Mix of required and optional packages makes sense
- [ ] Install order is logical (dependencies first)
- [ ] README explains use case and setup
- [ ] Version pinning for stability (avoid all "latest")
- [ ] Test installation in all supported formats
- [ ] Clear documentation of any MCP servers
- [ ] Post-install script (if needed) is well-tested

### Official Collection Standards
For PRPM team-curated collections:
- [ ] High-quality packages only
- [ ] Comprehensive testing across all formats
- [ ] Maintained regularly (updates within 30 days)
- [ ] Professional documentation
- [ ] Clear onboarding for new users
- [ ] Beta testing period completed
- [ ] Community feedback incorporated

## Defining Collections in prpm.json

Collections are defined in the `collections` array within `prpm.json`. See examples in the `prpm-json-best-practices-skill`.

### Collections-Only Repository

```json
{
  "name": "@prpm/collections",
  "version": "1.0.0",
  "description": "Official PRPM collections",
  "author": "PRPM Team",
  "license": "MIT",
  "collections": [
    {
      "id": "nextjs-pro",
      "name": "Next.js Professional Setup",
      "description": "Production-ready Next.js environment",
      "version": "1.0.0",
      "category": "development",
      "tags": ["nextjs", "react", "typescript"],
      "icon": "⚡",
      "packages": [
        {
          "packageId": "react-best-practices",
          "version": "^2.0.0",
          "required": true,
          "reason": "Core React patterns"
        }
      ]
    }
  ]
}
```

### Combined Packages + Collections

You can define both packages and collections in the same repo. Collections can reference local packages or external ones:

```json
{
  "name": "@username/my-repo",
  "author": "Your Name",
  "license": "MIT",
  "packages": [
    {
      "name": "my-skill",
      "version": "1.0.0",
      "description": "A skill",
      "format": "claude",
      "subtype": "skill",
      "files": [".claude/skills/my-skill.md"]
    }
  ],
  "collections": [
    {
      "id": "my-collection",
      "name": "My Collection",
      "description": "Bundles my skill with external packages",
      "version": "1.0.0",
      "category": "development",
      "tags": ["workflow"],
      "packages": [
        {
          "packageId": "my-skill",
          "version": "^1.0.0",
          "required": true
        },
        {
          "packageId": "@external/package",
          "version": "^2.0.0",
          "required": false
        }
      ]
    }
  ]
}
```

**Key points:**
- Collections reference packages by `packageId` (not file paths)
- Can mix local packages (defined in same repo) with external ones
- Each collection has its own `id`, `version`, and metadata
- Use `required: true/false` to mark packages as optional

## Common Use Cases

### 1. Project Startups
Quick setup for new projects:
- `collection/nextjs-pro` - Complete Next.js environment
- `collection/python-data` - Python data science stack
- `collection/django-api` - Django REST API setup

### 2. Workflow Optimization
Complete development toolchains:
- `collection/testing-complete` - All testing tools
- `collection/devops-infrastructure` - Full DevOps setup
- `collection/code-review` - Review and quality tools

### 3. Technology Stacks
Full ecosystem setups:
- `collection/full-stack-react` - Frontend + backend + DB
- `collection/enterprise-java` - Spring Boot microservices
- `collection/mobile-flutter` - Flutter development stack

### 4. Team Onboarding
New team members get standard setup:
- `collection/company-frontend` - Company frontend standards
- `collection/company-backend` - Company backend standards
- `collection/claude-skills` - All Claude-specific skills

## CLI Commands Reference

```bash
# List all collections
prpm collections

# Filter by category
prpm collections list --category development

# View only official collections
prpm collections list --official

# Get collection details
prpm collection info collection/nextjs-pro

# Install a collection
prpm install collection/nextjs-pro

# Create a collection
prpm collection create my-workflow

# Add packages to collection
prpm collection add my-workflow react-best-practices
prpm collection add my-workflow typescript-rules@2.0.0

# Remove package from collection
prpm collection remove my-workflow typescript-rules

# Publish collection
prpm collection publish my-workflow

# Update collection metadata
prpm collection update my-workflow --description "New description"
```

## API Endpoints

```
GET    /api/v1/collections                    - List collections with filters
GET    /api/v1/collections/:scope/:id         - Get collection details
POST   /api/v1/collections                    - Create collection
PUT    /api/v1/collections/:scope/:id         - Update collection
DELETE /api/v1/collections/:scope/:id         - Delete collection
POST   /api/v1/collections/:scope/:id/install - Track installation
GET    /api/v1/collections/:scope/:id/stats   - Get collection statistics
```

## Quality Indicators & Statistics

Collections track:
- `downloads` - Total installations
- `stars` - User ratings/favorites
- `installsByFormat` - Breakdown by editor (cursor, claude, continue, windsurf)
- `installsLastWeek` - Recent usage
- `installsLastMonth` - Monthly trend
- `package_count` - Number of packages in collection

## Example: Well-Structured Collection

```json
{
  "id": "nextjs-pro",
  "scope": "collection",
  "name": "Next.js Professional Setup",
  "description": "Production-ready Next.js development environment with TypeScript, Tailwind CSS, testing, and best practices. Optimized for building scalable web applications.",
  "version": "2.0.0",
  "author": "prpm",
  "maintainers": ["prpm-team"],
  "official": true,
  "verified": true,
  "category": "development",
  "tags": ["react", "nextjs", "typescript", "tailwind", "frontend"],
  "framework": "nextjs",
  "icon": "⚡",
  "readme": "# Next.js Pro Collection\n\nComplete setup for professional Next.js development...",
  "packages": [
    {
      "packageId": "react-best-practices",
      "version": "2.1.0",
      "required": true,
      "reason": "Core React patterns and component guidelines for maintainable code",
      "installOrder": 1
    },
    {
      "packageId": "typescript-strict",
      "version": "latest",
      "required": true,
      "reason": "TypeScript strict mode configuration for type safety",
      "installOrder": 2
    },
    {
      "packageId": "nextjs-patterns",
      "version": "2.0.0",
      "required": true,
      "reason": "Next.js-specific patterns for routing, data fetching, and optimization",
      "installOrder": 3
    },
    {
      "packageId": "tailwind-helper",
      "version": "3.0.1",
      "required": false,
      "reason": "Tailwind CSS utility classes and responsive design helpers",
      "installOrder": 4
    },
    {
      "packageId": "component-architect",
      "version": "1.2.0",
      "required": false,
      "reason": "Component architecture patterns for large-scale applications",
      "installOrder": 5
    }
  ],
  "config": {
    "defaultFormat": "cursor",
    "installOrder": "sequential",
    "postInstall": "echo 'Next.js Pro collection installed! Run: npm install && npm run dev'"
  }
}
```

## Technical Implementation Notes

### Server-Side Format Conversion
- Collections store packages in canonical format
- Server converts to each editor format on-the-fly
- Ensures consistency across all installations

### Atomic Operations
- All packages install together or fail together
- Rollback on any package failure
- Transaction-based installation process

### Dependency Resolution
- Automatic handling of package dependencies
- Version conflict detection
- Dependency graph validation

### Version Pinning Strategy
- Specific versions for stability
- "latest" for active development
- Semantic versioning enforcement

## Common Pitfalls to Avoid

1. **Too Few Packages**: Collections should bundle 3+ related packages. If only 1-2 packages, users should install individually.

2. **Unrelated Packages**: All packages should serve a common use case or workflow. Don't mix Django and React unless it's a full-stack collection.

3. **All Optional Packages**: At least 50% of packages should be required. Optional packages are for customization.

4. **Missing Reasons**: Every package needs a clear "reason" field explaining its inclusion.

5. **Wrong Install Order**: Dependencies must install before dependent packages.

6. **Broken MCP Configs**: Test all MCP servers thoroughly in Claude Code before publishing.

7. **Outdated Packages**: Regularly review and update package versions.

8. **Poor Documentation**: README should explain use case, setup steps, and expected outcome.

## Summary Checklist

When working with collections, remember:

- [ ] Collections bundle 3-15+ related packages for complete workflows
- [ ] Each collection has scope (collection/ or @username/)
- [ ] Packages can be required or optional
- [ ] Format-specific variants enable IDE optimization
- [ ] MCP servers enhance Claude Code collections
- [ ] Installation is atomic (all or nothing)
- [ ] Quality requires clear documentation and testing
- [ ] Official collections meet higher standards

## Key Metrics

- **25+ Official Collections** currently available
- **1,300+ Packages** available to include
- **4 Format Support**: Cursor, Claude, Continue, Windsurf
- **MCP Integration** available for Claude Code

---

**Remember**: Collections dramatically reduce friction by providing pre-curated, complete development environments that work seamlessly across all supported AI editors. They are the fastest way to get users productive with PRPM.
