---
name: docs-audit-scan
description: Parallelized documentation audit that scans for completeness, accuracy, consistency, and staleness, then creates implementation-ready fix issues.
---

<purpose>
Audit all project documentation across four dimensions -- completeness, accuracy, consistency, and staleness -- using parallel subagents. Synthesize findings into deduplicated, actionable GitHub issues grouped by file. This is the first phase of the docs-audit workflow.
</purpose>

<required_context>

## Prerequisites

- Git repository initialized with `.wingman/` workspace
- `gh` CLI installed and authenticated
- MCP tools available: `issues_create`, `issues_list`
- Access to project source tree (`packages/`, `docs/`, root config files)

</required_context>

<available_agent_types>

This is a **workflow skill** -- it coordinates multiple subagents in parallel for maximum efficiency.

- **Agent A:** Completeness Audit
- **Agent B:** Accuracy Audit
- **Agent C:** Consistency Audit
- **Agent D:** Staleness Audit

</available_agent_types>

<process>

<step name="inventory" priority="first">

### Phase 1: Documentation Inventory (Sequential)

**Why sequential:** Need a complete manifest before dispatching parallel auditors.

**Task:** Gather a comprehensive inventory of all documentation files and establish audit expectations.

**Approach:**

**1.1 Scan for Documentation Files**

Build a manifest of all documentation by scanning:

- `docs/` directory (recursive)
- `README.md` files in all directories (root, packages, skills)
- `AGENTS.md` and `CLAUDE.md` files
- `.wingman/config/CONSTITUTION.md`
- `.wingman/rules/` directory
- `.wingman/memory/` directory (for reference docs)
- `packages/*/AGENTS.md` and `packages/*/CLAUDE.md`

For each file, record:
- Absolute file path
- Last modified date (via `git log -1 --format="%ai" -- <path>`)
- File size

**1.2 Establish Audit Expectations**

Read the project's documentation expectations from:

- `CONSTITUTION.md` -- what documentation is required
- `AGENTS.md` -- documentation map and conventions
- The Documentation Map table (if present) for expected coverage

**1.3 Build Package Manifest**

List all packages under `packages/` and note which have:
- A `README.md`
- An `AGENTS.md`
- A `docs/` or documentation reference

**Output:** Documentation manifest (list of files with metadata) and package coverage map.

</step>

---

<step name="parallel-audit">

### Phase 2: Parallel Audit (Parallel)

**Why parallel:** Four independent audit dimensions with no dependencies -- run simultaneously.

Launch **four parallel subagents** using the Task tool:

#### Agent A: Completeness Audit

**Task:** Check if all packages, modules, and features have documentation.

**Approach:**

1. Cross-reference `packages/*/` directories against `docs/` entries
2. Check for missing README files in packages and significant subdirectories
3. Check for undocumented MCP tools:
   - List all MCP tool definitions in the codebase
   - Cross-reference against `docs/06-reference/mcp-tools.md`
4. Check for undocumented skills:
   - List all skills in `packages/agent-core/builtins/skills/`
   - Verify each has a `SKILL.md` with meaningful content (not just a stub)
5. Check for undocumented workflows:
   - List all workflow YAMLs in `.wingman/playbooks/workflows/`
   - Verify each is referenced in documentation
6. Check the Documentation Map in `AGENTS.md` for completeness

**Output format:**
```
COMPLETENESS_FINDINGS:
- [MISSING] <path> -- <what is missing>
- [STUB] <path> -- <file exists but lacks meaningful content>
- [GAP] <topic> -- <feature/module with no documentation>
```

---

#### Agent B: Accuracy Audit

**Task:** Verify documentation claims against code reality.

**Approach:**

1. Check file paths mentioned in docs actually exist:
   - Extract all file path references from markdown files
   - Verify each path resolves to an existing file
2. Check code examples are syntactically valid:
   - Extract fenced code blocks from documentation
   - Verify syntax is plausible for the declared language
3. Check API/tool parameter docs match actual implementations:
   - Compare MCP tool parameter descriptions against actual tool schemas
   - Compare CLI command docs against actual CLI help output
4. Check configuration file references:
   - Verify documented config keys exist in actual config schemas
   - Verify documented default values match actual defaults
5. Check import paths and module references:
   - Verify documented import paths are valid

**Output format:**
```
ACCURACY_FINDINGS:
- [BROKEN_PATH] <doc_path>:<line> -- references <missing_path>
- [STALE_API] <doc_path>:<line> -- documents <old_signature>, actual is <new_signature>
- [WRONG_DEFAULT] <doc_path>:<line> -- says default is <X>, actual is <Y>
- [INVALID_EXAMPLE] <doc_path>:<line> -- code block has syntax issues
```

---

#### Agent C: Consistency Audit

**Task:** Check formatting conventions, cross-references, and naming consistency.

**Approach:**

1. Check formatting conventions:
   - Header levels are consistent within each document
   - Code block language identifiers are present and correct
   - Table formatting is consistent
   - List formatting (bullets vs numbers) is consistent within sections
2. Check cross-references between docs are valid:
   - Extract all relative markdown links (`[text](path.md)`)
   - Verify each target file exists
   - Check for anchor references that may be broken
3. Check naming consistency:
   - Tool names, skill names, and package names are spelled consistently
   - Terminology matches the glossary (if one exists)
   - Kebab-case vs camelCase consistency for identifiers
4. Check structural consistency:
   - Similar documents follow similar section structures
   - AGENTS.md files across packages follow a common pattern

**Output format:**
```
CONSISTENCY_FINDINGS:
- [BROKEN_LINK] <doc_path>:<line> -- link to <target> is broken
- [FORMAT] <doc_path>:<line> -- <formatting issue description>
- [NAMING] <doc_path>:<line> -- inconsistent name: <variant_a> vs <variant_b>
- [STRUCTURE] <doc_path> -- missing expected section: <section_name>
```

---

#### Agent D: Staleness Audit

**Task:** Cross-reference docs against recent git history to identify stale documentation.

**Approach:**

1. Get recent git activity (last 90 days):
   ```bash
   git log --oneline --since="90 days ago" --name-only
   ```
2. For each documentation file in the manifest:
   - Get the doc's last update date
   - Get the last modification date of the code it documents
   - Flag if code changed significantly after doc was last updated
3. Identify docs with no updates in 90+ days for areas with active development:
   - Cross-reference doc modification dates against source code modification dates
   - Flag docs where source has 5+ commits since doc was last touched
4. Check for recently added features with no documentation:
   - Look at new files added in recent commits
   - Check if corresponding docs were added or updated

**Output format:**
```
STALENESS_FINDINGS:
- [STALE] <doc_path> -- last updated <date>, but <code_path> changed <N> times since
- [OUTDATED] <doc_path> -- references code pattern from before <commit_hash>
- [NEW_UNDOCUMENTED] <code_path> -- added in <commit_hash> with no corresponding docs
```

</step>

---

<step name="issue-synthesis">

### Phase 3: Issue Synthesis (Sequential)

**Why sequential:** Needs results from all four audit agents to deduplicate and group.

**Task:** Collect results from all audit agents, deduplicate, group into fix units, and create GitHub issues.

**Approach:**

**3.1 Collect and Deduplicate**

1. Gather all findings from Agents A-D
2. Remove duplicate findings (same file + same issue found by multiple agents)
3. Merge related findings (e.g., broken path in doc + staleness for same doc = one issue)

**3.2 Group into Fix Units**

Group findings into coherent fix units:
- All issues for one documentation file = one issue
- If a single file has 10+ findings, split by category (accuracy vs completeness)
- Cross-cutting issues (e.g., naming inconsistency across many files) = one issue per pattern

**3.3 Prioritize**

Assign priority based on severity:
- **High**: Broken paths, wrong API docs, missing critical documentation
- **Medium**: Stale docs for actively developed code, formatting inconsistencies
- **Low**: Minor naming inconsistencies, style issues, old but still correct docs

**3.4 Create Issues**

For each fix unit, create a GitHub issue via `issues_create` MCP tool with:

- **Title**: `docs: fix [description] in [file]`
- **Type**: `issue`
- **Labels**: `["documentation", "docs-audit"]`
- **Priority**: based on severity (high/medium/low)
- **Description** containing:
  - Exact file paths affected
  - What is wrong (with line numbers where available)
  - What it should say or what needs to change
  - Acceptance criteria checklist (each finding = one checklist item)

**3.5 Generate Summary Report**

Produce a summary of the audit:

```
DOCS_AUDIT_SCAN_COMPLETE

Issues created: [count]
  High priority: [count]
  Medium priority: [count]
  Low priority: [count]

Findings by category:
  Completeness: [count]
  Accuracy: [count]
  Consistency: [count]
  Staleness: [count]

Issue IDs: [comma-separated list]
```

**Output:** The scan result object containing:
- `issueIds`: array of created issue IDs
- `summary`: the summary report text
- `findingCounts`: breakdown by category and priority

</step>

</process>

<success_criteria>
- [ ] Documentation inventory completed with file paths and metadata
- [ ] All four audit agents dispatched in parallel (single message with four Task calls)
- [ ] Completeness audit checked packages, MCP tools, skills, and workflows
- [ ] Accuracy audit verified file paths, code examples, and API parameters
- [ ] Consistency audit checked cross-references, formatting, and naming
- [ ] Staleness audit cross-referenced docs against recent git history
- [ ] Findings deduplicated and grouped into coherent fix units
- [ ] GitHub issues created for each fix unit with clear acceptance criteria
- [ ] Summary report produced with issue IDs and finding counts
</success_criteria>

<error_handling>

## Failure Modes

**Audit agent fails to complete:**
- Collect results from completed agents and proceed with partial data
- Note which audit dimension is missing in the summary report
- Do not block issue creation on a single failed agent

**Too many findings (100+):**
- Cap issue creation at 30 issues per run
- Prioritize high-severity findings
- Note remaining findings in a summary issue titled `docs: remaining audit findings from [date]`

**MCP tool unavailable:**
- Surface the MCP unavailability as a blocking error
- Report which MCP tools are missing and halt the skill
- Do not fall back to CLI tools -- the project constitution requires MCP tools for issue management
- Include a clear error message: "docs-audit-scan requires MCP tools (issues_create). Ensure the Wingman MCP server is running."

**Git history unavailable:**
- Staleness agent uses file modification dates from filesystem instead
- Note reduced accuracy in staleness findings

</error_handling>
