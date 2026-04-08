---
name: map-codebase
description: Generates a visual architecture overview of a codebase using Mermaid diagrams, producing ARCHITECTURE.md at the repo root. Use when onboarding to a new project, documenting system structure, or creating a high-level map of modules, dependencies, and key flows.
---

# Map Codebase

## Overview

This skill produces a single `ARCHITECTURE.md` file at the repository root containing Mermaid diagrams that visualize the codebase's structure, dependencies, key types, and critical flows. The output is designed to render natively on GitHub and serve as a quick-reference architectural map.

**Scope:** The default scope is the entire repository. The user may optionally request scoping to a subdirectory (e.g., "map packages/gateway"). When scoped, all diagrams and analysis are restricted to that subtree, but external dependency references are still noted.

## When to Use

- Onboarding to an unfamiliar codebase
- Creating or refreshing architectural documentation
- Understanding module boundaries and dependency flow before a refactor
- Generating a visual overview for a code review or design discussion

## Constraints

- Write `ARCHITECTURE.md` at the repo root (or scoped directory root if scoped). Overwrite if it already exists.
- Every section in the output MUST contain at least one Mermaid diagram.
- All Mermaid syntax must be valid and renderable on GitHub.
- Focus on architecture (modules, boundaries, flows), not implementation details.
- Keep the total output under 500 lines.
- Do NOT use emojis anywhere in the output.
- Support both monorepos (multiple packages/workspaces) and single-package repos.

## Phase 1: Discover Repository Structure

### Step 1: Identify repo type and language ecosystem

Run a directory listing of the repo root. Look for indicators:

- **Monorepo signals:** `pnpm-workspace.yaml`, `lerna.json`, root `packages/` or `apps/` directory, Cargo workspace in `Cargo.toml`, Go workspace `go.work`
- **Single-package signals:** A single `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, or `setup.py` at root
- **Language detection:** File extensions (`.ts`, `.py`, `.go`, `.rs`, `.java`), config files (`tsconfig.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`)

### Step 2: Map the directory tree

Run `find {SCOPE} -type f -not -path '*/.git/*' -not -path '*/node_modules/*' -not -path '*/dist/*' -not -path '*/build/*' -not -path '*/__pycache__/*' -not -path '*/.next/*' -not -path '*/.turbo/*' -not -path '*/target/*' -not -path '*/.cache/*' | head -500` to get a file listing.

Also run `find {SCOPE} -type d -not -path '*/.git/*' -not -path '*/node_modules/*' -not -path '*/dist/*' -not -path '*/build/*' -not -path '*/__pycache__/*' -not -path '*/.next/*' -not -path '*/.turbo/*' -not -path '*/target/*' -not -path '*/.cache/*' -maxdepth 3` to understand the top-level directory layout.

### Step 3: Count files by type

Run `find {SCOPE} -type f -name '*.ts' -o -name '*.tsx' -o -name '*.js' -o -name '*.jsx' -o -name '*.py' -o -name '*.go' -o -name '*.rs' -o -name '*.java' -o -name '*.md' -o -name '*.json' -o -name '*.yaml' -o -name '*.yml' -o -name '*.toml' | grep -v node_modules | grep -v dist` and group by extension to understand codebase composition. Use `wc -l` or similar to get counts per extension.

### Step 4: Identify packages and modules

For each ecosystem:

- **Node/TypeScript:** Read `package.json` files for `name`, `dependencies`, `devDependencies`. Check workspace definitions in root `package.json` or `pnpm-workspace.yaml`.
- **Python:** Read `pyproject.toml` or `setup.py` for package name, dependencies. Check for `src/` layout or flat layout.
- **Go:** Read `go.mod` for module path and dependencies. Check `go.work` for workspace members.
- **Rust:** Read `Cargo.toml` for package name and dependencies. Check `[workspace]` members.

Record each package/module with its name, path, and declared dependencies.

## Phase 2: Analyze Dependencies and Relationships

### Step 1: Map internal dependencies

For monorepos, determine which packages depend on which other packages by reading their dependency declarations (e.g., `"@scope/pkg-b": "workspace:*"` in package.json).

For single-package repos, identify the major modules/directories and trace import relationships between them.

### Step 2: Trace import graphs

Search for import/require statements to understand module relationships:

- **TypeScript/JavaScript:** Grep for `import .* from ['"]` and `require\(['"]` patterns
- **Python:** Grep for `^import ` and `^from .* import` patterns
- **Go:** Grep for `import \(` blocks and single import statements
- **Rust:** Grep for `use ` and `mod ` statements

Focus on internal imports (between modules in the repo), not external library imports. Identify the most-imported modules -- these are the architectural pillars.

### Step 3: Find entry points

Search for:

- `main` functions, `index.ts`/`index.js` files, `__main__.py`, `cmd/` directories
- CLI entry points: `bin` fields in `package.json`, `[tool.poetry.scripts]` in `pyproject.toml`, `main.go` in `cmd/`
- Server starts: grep for `listen(`, `serve(`, `app.run(`, `createServer(`
- Build/bundle entry points: `entry` fields in webpack/vite/rollup configs

### Step 4: Identify key types and interfaces

Search for major type definitions:

- **TypeScript:** Grep for `export interface `, `export type `, `export class `, `export enum `
- **Python:** Grep for `^class `, `@dataclass`, `class.*BaseModel`
- **Go:** Grep for `type .* struct`, `type .* interface`
- **Rust:** Grep for `pub struct `, `pub enum `, `pub trait `
- **Java:** Grep for `public class `, `public interface `

Focus on exported/public types. Identify which types are used across module boundaries (these are the architectural contracts).

### Step 5: Identify critical flows

Look for the 2-4 most important execution paths through the system:

- HTTP request handling: trace from route definition to handler to service to data layer
- CLI command dispatch: trace from argument parsing to command execution
- Event/message processing: trace from event emission to handler
- Build/pipeline flows: trace from input to output transformations

Read key files along these paths to understand the flow sequence.

## Phase 3: Compose ARCHITECTURE.md

Build the output file with these sections. Every section MUST include at least one Mermaid diagram.

### Section 1: Overview

Write 2-4 sentences describing what the project is, what language/framework it uses, and its high-level architecture pattern (monorepo, client-server, CLI tool, library, etc.).

### Section 2: Module / Package Dependency Diagram

Create a `graph TD` (top-down) Mermaid diagram showing packages or major modules as nodes and dependency arrows between them.

Guidelines:
- Use short, readable node labels (package name without scope prefix)
- Group related packages with `subgraph` blocks when there are more than 6 nodes
- Distinguish internal dependencies (solid arrows) from key external dependencies (dashed arrows) if helpful
- Limit to the top 15-20 most architecturally significant modules; collapse minor utilities into a single node if needed

Example structure:
```
graph TD
    A[gateway] --> B[agent-core]
    A --> C[config]
    B --> D[fuzzy-match]
    B --> E[issue-manager]
```

### Section 3: Key Types and Relationships

Create a `classDiagram` showing the most important types/interfaces and their relationships.

Guidelines:
- Include only types that cross module boundaries or define core contracts (aim for 8-15 types)
- Show key fields/methods (2-4 per type, not exhaustive)
- Use proper Mermaid class diagram relationships: `<|--` inheritance, `*--` composition, `o--` aggregation, `-->` dependency
- Group by module using Mermaid namespaces or comments

### Section 4: Critical Flow Diagrams

Create 1-3 `sequenceDiagram` blocks showing the most important execution paths.

Guidelines:
- Pick flows that illustrate how the major components interact
- Show the happy path; note error handling only when architecturally significant
- Use meaningful participant names (module or class names, not variable names)
- Keep each diagram under 20 interactions

### Section 5: Codebase Composition

Create a `pie` chart showing the breakdown of the codebase by language or file type.

Use the file counts from Phase 1 Step 3. Only include categories that represent more than 2% of the total. Group everything else under "Other".

### Section 6: Directory Structure

Write an annotated directory tree showing the top 2-3 levels of the repo structure. Use code block formatting. Add brief inline annotations describing each directory's purpose.

Example:
```
repo-root/
  packages/
    gateway/          # HTTP API server
    agent-core/       # Shared algorithms and utilities
    desktop/          # Electron desktop app
  docs/               # Project documentation
  scripts/            # Build and deployment scripts
```

### Section 7: Symbol Index

Create a markdown table listing the 15-30 most architecturally significant symbols (types, functions, modules) with their location and a brief description.

| Symbol | Location | Description |
|--------|----------|-------------|
| Gateway | packages/gateway/src/server.ts | HTTP server entry point |
| FuzzyMatcher | packages/agent-core/src/fuzzy/ | String similarity matching |

## Phase 4: Validate and Write

### Step 1: Check Mermaid syntax

Review each Mermaid block for common syntax errors:
- Node IDs must not contain spaces or special characters (use brackets for labels: `A[My Label]`)
- Arrow syntax must be consistent (`-->`, `-.->`, `==>`)
- `classDiagram` uses `:` for members, not `=`
- `sequenceDiagram` participant names must not contain spaces (use aliases)
- `pie` values must be numeric
- All `subgraph` blocks must have a matching `end`

### Step 2: Check line count

Count the total lines. If over 500, trim by:
1. Reducing the symbol index to top 15
2. Removing the least critical sequence diagram
3. Shortening directory annotations
4. Collapsing small modules in the dependency diagram

### Step 3: Write the file

Write `ARCHITECTURE.md` to the repo root (or scoped directory root). If the file already exists, overwrite it entirely.

### Step 4: Verify

Read back the written file to confirm:
- All Mermaid blocks have opening and closing fences
- No broken markdown formatting
- File is under 500 lines
- No emojis present

## Output Format

The final `ARCHITECTURE.md` must follow this template:

```markdown
# Architecture Overview

{OVERVIEW_TEXT}

## Module Dependencies

{DEPENDENCY_DIAGRAM}

## Key Types

{CLASS_DIAGRAM}

## Critical Flows

{SEQUENCE_DIAGRAMS}

## Codebase Composition

{PIE_CHART}

## Directory Structure

{ANNOTATED_TREE}

## Symbol Index

{SYMBOL_TABLE}
```

## What NOT to Do

- Do not read every file in the repo -- sample strategically
- Do not include implementation details (function bodies, algorithm explanations)
- Do not critique the architecture -- describe it objectively
- Do not invent relationships you cannot verify from imports/dependencies
- Do not include test files or test utilities in the architecture diagrams
- Do not use emojis in any output
- Do not exceed 500 lines in the output file
- Do not leave any section without a Mermaid diagram
