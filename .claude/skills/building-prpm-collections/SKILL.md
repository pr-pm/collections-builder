---
name: building-prpm-collections
description: Use when creating curated PRPM package collections for specific workflows (Next.js, testing, DevOps, etc.) - guides discovery, selection, and structuring of 3-15+ related packages with proper metadata and quality standards
---

# Building PRPM Collections

## Overview

Build curated collections of PRPM packages that solve complete workflows. Uses LLM-guided search to discover packages, evaluates quality, and structures them with proper metadata, versioning, and IDE-specific variants.

## When to Use

- Creating a new collection for a framework, tech stack, or workflow
- Curating packages for project startups (Next.js, Django, Flutter, etc.)
- Building team onboarding collections
- Assembling DevOps or testing toolchains
- When homepage advertises a collection that doesn't exist yet

**Don't use for:**
- Single package creation
- Ad-hoc package combinations without broader reuse value

## Prerequisites

Before building, ensure you have:
- Access to PRPM search/API for package discovery
- Understanding of the target workflow/framework
- Knowledge of prpm-collections skill (reference for structure)

## Collection Building Workflow

### 1. Define Collection Scope

**Identify:**
- Target audience (beginners, professionals, teams)
- Use case (project startup, workflow optimization, tech stack)
- Framework/technology if applicable
- Minimum viable package count (3-7 for starter, 8-15+ for comprehensive)

**Example scoping:**
```typescript
{
  id: 'nextjs-pro',
  targetAudience: 'Professional developers starting Next.js projects',
  useCase: 'Production-ready Next.js with TypeScript and best practices',
  framework: 'nextjs',
  targetPackageCount: 5-8
}
```

### 2. Discover Packages

**Search strategy:**
- Start with core/foundational packages for the technology
- Add complementary tools (linting, testing, patterns)
- Include optional enhancements
- Verify all packages exist in PRPM registry

**Search patterns:**
```bash
# Framework-specific
prpm search nextjs --category development
prpm search react --tags typescript

# Workflow-specific
prpm search testing --tags jest,playwright
prpm search devops --category infrastructure

# Quality indicators
prpm search --verified --min-downloads 100
```

**Discovery checklist:**
- [ ] Core framework packages (required)
- [ ] Best practices and patterns (required)
- [ ] Type safety/linting (recommended)
- [ ] Testing tools (optional)
- [ ] Developer experience enhancements (optional)

### 3. Evaluate Package Quality

**For each candidate package, verify:**
- [ ] Clear purpose and documentation
- [ ] Actively maintained (recent updates)
- [ ] Good download/star metrics
- [ ] Compatible with target framework/workflow
- [ ] No conflicts with other selected packages

**Minimum quality bar:**
- Official packages: Always include if relevant
- Verified packages: Prefer over unverified
- Community packages: Require strong metrics + reviews

### 4. Structure Collection Metadata

**Required fields:**
```typescript
{
  id: string;              // kebab-case, descriptive
  scope: 'collection';     // Official collections
  name: string;            // Human-readable title
  description: string;     // 2-3 sentences, clear value prop
  version: '1.0.0';        // Start at 1.0.0
  author: 'prpm';          // For official
  category: Category;      // Match primary use case
  tags: string[];          // 3-7 relevant tags
  framework?: string;      // If framework-specific
  icon: string;            // Emoji representing collection
}
```

**Category selection guide:**
- `development`: Framework stacks, project setups
- `testing`: Test frameworks and tools
- `devops`: Infrastructure, deployment, monitoring
- `design`: UI/UX tools and patterns
- `data-science`: Data analysis, ML workflows
- `documentation`: Docs generation, API specs
- `general`: Cross-cutting or multi-purpose

### 5. Define Package Relationships

**For each package in collection:**
```typescript
{
  packageId: string;       // Exact package ID from registry
  version: string;         // Specific version or 'latest'
  required: boolean;       // True for core, false for optional
  reason: string;          // WHY this package is included
  installOrder: number;    // 1-based, dependencies first
}
```

**Required vs Optional strategy:**
- Required (50-70%): Core functionality, can't skip
- Optional (30-50%): Enhancements, style choices, extras

**Installation order rules:**
- 1-3: Foundation (language, framework basics)
- 4-6: Patterns and best practices
- 7+: Enhancements and optional tools

### 6. Add Configuration

**Optional but recommended:**
```typescript
{
  config: {
    defaultFormat: 'cursor',           // Most common editor
    installOrder: 'sequential',        // For dependencies
    postInstall?: string,              // Setup instructions
    mcpServers?: {...}                 // Claude Code only
  }
}
```

### 7. Write Collection README

**Sections to include:**
```markdown
# Collection Name

## What's Included
- Package 1: Purpose
- Package 2: Purpose
- ...

## Who This Is For
Target audience and use cases

## Getting Started
prpm install collection/name

## What You Get
Expected outcome after installation

## Optional Packages
What's optional and why you might want them
```

### 8. Quality Checklist

Before publishing:
- [ ] Minimum 3 packages included
- [ ] All package IDs verified to exist
- [ ] Each package has clear "reason" field
- [ ] Mix of required/optional makes sense
- [ ] Install order is logical
- [ ] Description is clear and compelling (2-3 sentences)
- [ ] 3-7 relevant tags included
- [ ] Icon chosen (emoji or image)
- [ ] Category matches primary use case
- [ ] README explains value and setup
- [ ] Version pinning strategy applied (avoid all "latest")

## Example: Next.js Pro Collection

```json
{
  "id": "nextjs-pro",
  "scope": "collection",
  "name": "Next.js Professional Setup",
  "description": "Production-ready Next.js development environment with TypeScript, React best practices, and modern tooling. Perfect for building scalable web applications with confidence.",
  "version": "1.0.0",
  "author": "prpm",
  "official": true,
  "category": "development",
  "tags": ["nextjs", "react", "typescript", "frontend", "production"],
  "framework": "nextjs",
  "icon": "⚡",
  "packages": [
    {
      "packageId": "react-best-practices",
      "version": "latest",
      "required": true,
      "reason": "Core React patterns and component guidelines for maintainable code",
      "installOrder": 1
    },
    {
      "packageId": "nextjs-patterns",
      "version": "latest",
      "required": true,
      "reason": "Next.js-specific patterns for routing, data fetching, and optimization",
      "installOrder": 2
    },
    {
      "packageId": "typescript-strict",
      "version": "latest",
      "required": true,
      "reason": "TypeScript strict mode configuration for type safety",
      "installOrder": 3
    },
    {
      "packageId": "tailwind-helper",
      "version": "latest",
      "required": false,
      "reason": "Tailwind CSS utility classes and responsive design helpers",
      "installOrder": 4
    },
    {
      "packageId": "testing-react-components",
      "version": "latest",
      "required": false,
      "reason": "React Testing Library patterns for component testing",
      "installOrder": 5
    }
  ],
  "config": {
    "defaultFormat": "cursor",
    "installOrder": "sequential",
    "postInstall": "Collection installed! Start building with: npm create next-app"
  }
}
```

## Common Mistakes

| Mistake | Why It Fails | Fix |
|---------|--------------|-----|
| Too few packages (1-2) | Not a "collection" | Minimum 3, target 5-8+ |
| Unrelated packages | Confusing value prop | All packages serve common workflow |
| All optional | Users unsure what to pick | 50-70% required |
| Missing "reason" fields | Users don't understand value | Explain WHY each package matters |
| Wrong install order | Dependencies fail | Dependencies before dependents |
| Generic description | Poor discoverability | Specific use case + clear benefits |
| No framework tag | Hard to find | Tag framework if specific |
| Version chaos | Breaks over time | Pin stable versions, "latest" sparingly |

## Package Discovery Tips

**Finding packages:**
- Search by framework name: `nextjs`, `django`, `flutter`
- Search by technology: `typescript`, `testing`, `api`
- Search by pattern: `best-practices`, `patterns`, `guidelines`
- Filter by official/verified for quality

**Evaluating relevance:**
- Read package description and README
- Check download count (popularity signal)
- Verify last update (actively maintained)
- Test install individually before adding to collection

## Collection Types

### 1. Project Starters
Complete setup for new projects
- **Example**: `nextjs-pro`, `django-api`, `flutter-mobile`
- **Size**: 5-10 packages
- **Mix**: 60% required, 40% optional

### 2. Workflow Toolchains
End-to-end workflow support
- **Example**: `testing-complete`, `devops-infrastructure`
- **Size**: 8-15+ packages
- **Mix**: 50% required, 50% optional

### 3. Team Standards
Company/team conventions
- **Example**: `company-frontend`, `team-backend`
- **Size**: 5-12 packages
- **Mix**: 70% required, 30% optional

### 4. Technology Stacks
Full ecosystem bundles
- **Example**: `full-stack-react`, `python-data-science`
- **Size**: 10-20+ packages
- **Mix**: 50% required, 50% optional

## API/CLI Commands

```bash
# Create collection (if CLI exists)
prpm collection create nextjs-pro

# Add packages
prpm collection add nextjs-pro react-best-practices --required
prpm collection add nextjs-pro tailwind-helper --optional

# Set metadata
prpm collection update nextjs-pro \
  --category development \
  --tags nextjs,react,typescript \
  --description "Production-ready Next.js setup"

# Publish
prpm collection publish nextjs-pro
```

## Verification

**Before publishing, test:**
```bash
# Install locally to verify
prpm install collection/nextjs-pro

# Check all packages installed
prpm list

# Verify no conflicts
prpm doctor

# Test in each supported format
prpm install collection/nextjs-pro --as cursor
prpm install collection/nextjs-pro --as claude
```

## Summary

Building great collections:
1. **Scope clearly** - Know audience and use case
2. **Discover thoroughly** - Search, evaluate, verify
3. **Structure logically** - Required/optional, install order
4. **Document well** - Clear descriptions and reasons
5. **Test completely** - Verify all formats work

**Quality bar**: Would you confidently recommend this to a colleague starting a new project?

---

**Remember**: Collections solve complete workflows, not partial problems. Aim for "install once, productive immediately."
