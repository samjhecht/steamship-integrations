---
name: verifying-governance
description: Validates governance file completeness, format compliance, and metric accuracy. Use when auditing governance health, after bulk changes, or ensuring documentation integrity.
---

# Verify Governance Framework

## Purpose

Governance frameworks can drift over time:
- Directories get renamed or go missing
- Files end up in wrong locations
- .gitignore patterns become incomplete
- Structure diverges from canonical schema

This skill performs systematic verification to detect and fix structural drift using `workspace-schema.json` as the single source of truth.

## What This Skill Checks

**IN SCOPE** (Structure and Existence):
- Directory structure matches `workspace-schema.json`
- Subdirectories exist where schema defines them
- Governance files exist at correct paths
- Files are in correct locations (not legacy paths)
- `.wingman/.gitignore` exists with all required patterns

**OUT OF SCOPE** (Content Quality):
- File content/template compliance (future enhancement)
- Governance file sections/structure validation
- Cross-document link validation
- Template version mismatches
- Metrics staleness

This simplification focuses on answering: "Is the structure correct?" not "Is the content good?"

## Verification Workflow

### Phase 1: Load Schema

**Load workspace-schema.json as canonical structure:**

```bash
# Find wingman installation (usually in current project or ~/.local/share/wingman/)
WINGMAN_ROOT=$(pwd)
SCHEMA_PATH="$WINGMAN_ROOT/.wingman/config/workspace-schema.json"

# Check if schema exists
if [ ! -f "$SCHEMA_PATH" ]; then
  echo "ERROR: workspace-schema.json not found at $SCHEMA_PATH"
  echo "This project may not have wingman initialized."
  exit 1
fi

# Read schema (you'll parse this to extract directories, files, gitignore patterns)
cat "$SCHEMA_PATH"
```

**Parse key sections:**
- `directories` - Canonical directory structure
- `governanceFiles` - Required governance files
- `readmeFiles` - Directory README files
- `gitignorePatterns` - Patterns for .wingman/.gitignore

### Phase 2: Detect Directory Drift

**Check each directory in schema:**

```bash
# For each directory in schema.directories:
# Example: .wingman/issues, .wingman/specifications, etc.

echo "=== Checking Directories ==="

# From schema, check all directories
for dir in .wingman/issues .wingman/specifications .wingman/notes .wingman/notes \
           .wingman/plans .wingman/cache .wingman/config; do
  if [ -d "$dir" ]; then
    echo "✓ $dir exists"
  else
    echo "✗ MISSING: $dir"
  fi
done

# Check subdirectories (from schema.directories[].subdirectories)
echo "=== Checking Subdirectories ==="
if [ -d ".wingman/issues/completed" ]; then
  echo "✓ .wingman/issues/completed exists"
else
  echo "✗ MISSING: .wingman/issues/completed"
fi
```

**Detect legacy renamed directories:**

Common v1.0 → v1.2 migrations to detect:
- `.wingman/issues/complete/` → `.wingman/issues/completed/` (note: schema uses "completed", user may have "complete")
- `.wingman/specifications/done/` → `.wingman/specifications/archived/`
- `.wingman/templates/` → (removed, templates now in `.wingman/playbooks/templates/` or skill dirs)

```bash
# Detect renamed directories
echo "=== Detecting Legacy Directory Names ==="

if [ -d ".wingman/issues/complete" ]; then
  echo "⚠️ LEGACY: .wingman/issues/complete/ should be renamed to .wingman/issues/completed/"
fi

if [ -d ".wingman/specifications/done" ]; then
  echo "⚠️ LEGACY: .wingman/specifications/done/ should be renamed to .wingman/specifications/archived/"
fi

if [ -d ".wingman/templates" ]; then
  echo "LEGACY: .wingman/templates/ no longer used (templates in .wingman/playbooks/templates/ or skill dirs)"
fi
```

### Phase 3: Detect File Drift

**Check governance files from schema:**

```bash
echo "=== Checking Governance Files ==="

# From schema.governanceFiles
for file in .wingman/CONSTITUTION.md .wingman/ROADMAP.md; do
  if [ -f "$file" ]; then
    echo "✓ $file exists"
  else
    echo "ℹ️  OPTIONAL: $file (not required, but recommended)"
  fi
done

# From schema.readmeFiles
for file in .wingman/issues/README.md .wingman/specifications/README.md \
            .wingman/notes/README.md .wingman/plans/README.md; do
  if [ -f "$file" ]; then
    echo "✓ $file exists"
  else
    echo "ℹ️  MISSING: $file (consider creating)"
  fi
done
```

**Detect files in wrong locations:**

Common legacy file locations:
- `.wingman/hooks-config.json` → `.wingman/config/hooks-config.json`
- `.wingman/workspace-schema.json` → `.wingman/config/workspace-schema.json`

```bash
echo "=== Detecting Legacy File Locations ==="

if [ -f ".wingman/hooks-config.json" ]; then
  echo "⚠️ LEGACY: .wingman/hooks-config.json should be at .wingman/config/hooks-config.json"
fi

if [ -f ".wingman/workspace-schema.json" ]; then
  echo "⚠️ LEGACY: .wingman/workspace-schema.json should be at .wingman/config/workspace-schema.json"
fi
```

### Phase 4: Detect .gitignore Drift

**Check .wingman/.gitignore:**

```bash
echo "=== Checking .wingman/.gitignore ==="

if [ ! -f ".wingman/.gitignore" ]; then
  echo "✗ MISSING: .wingman/.gitignore"
  echo "   Required patterns: cache/, sessions/"
else
  echo "✓ .wingman/.gitignore exists"

  # Check each pattern from schema.gitignorePatterns
  echo "=== Checking gitignore patterns ==="

  for pattern in "cache/" "sessions/"; do
    if grep -q "^${pattern}$" .wingman/.gitignore; then
      echo "✓ Pattern present: $pattern"
    else
      echo "✗ MISSING PATTERN: $pattern"
    fi
  done
fi
```

### Phase 5: Report Drift

**Compile findings into clear report:**

```markdown
# Governance Structure Verification Report

**Date**: [YYYY-MM-DD HH:MM]
**Schema Version**: [from workspace-schema.json version field]

---

## Summary

- **Status**: [✅ COMPLIANT / ⚠️ DRIFT DETECTED / ❌ CRITICAL ISSUES]
- **Missing Directories**: [N]
- **Missing Files**: [N]
- **Wrong Locations**: [N]
- **Missing Gitignore Patterns**: [N]

---

## Detailed Findings

### Missing Directories ([N])

- `.wingman/issues/completed/` (required by schema)
- `.wingman/cache/` (required by schema)

**Fix**: `mkdir -p .wingman/issues/completed .wingman/cache`

### Legacy Directory Names ([N])

- `.wingman/issues/complete/` exists but schema expects `.wingman/issues/completed/`
  - **Impact**: 12 files in old location
  - **Fix**: `mv .wingman/issues/complete .wingman/issues/completed`

- `.wingman/specifications/done/` exists but schema expects `.wingman/specifications/archived/`
  - **Impact**: 5 files in old location
  - **Fix**: `mv .wingman/specifications/done .wingman/specifications/archived`

### Wrong File Locations ([N])

- `.wingman/hooks-config.json` should be at `.wingman/config/hooks-config.json`
  - **Fix**: `mv .wingman/hooks-config.json .wingman/config/hooks-config.json`

### Missing Files ([N])

- `.wingman/.gitignore` (required for proper git tracking)
  - **Fix**: Create with patterns: cache/, sessions/

**Note**: Governance files (CONSTITUTION.md, ROADMAP.md) are optional per schema.

### Missing Gitignore Patterns ([N])

- Pattern `sessions/` missing from `.wingman/.gitignore`
  - **Fix**: Add to .gitignore

---

## Recommended Actions

[List specific commands to fix each issue, grouped by type]
```

### Phase 6: Get Permission

**Present user with 4 options:**

```
## How would you like to proceed?

1. **Fix all automatically** (recommended)
   - I'll apply all fixes shown above
   - Affected files will be moved/renamed
   - Directories will be created
   - .gitignore will be updated

2. **Show me the commands**
   - I'll display bash commands
   - You run them manually
   - Good for reviewing changes first

3. **Let me choose**
   - I'll ask about each fix individually
   - You approve or skip each one
   - Good for selective fixes

4. **Cancel**
   - No changes made
   - Exit verification

Please choose: [1/2/3/4]
```

**Wait for user response before proceeding.**

### Phase 7: Apply Fixes

**Option 1: Fix All Automatically**

```bash
echo "=== Applying All Fixes ==="

# Create missing directories
mkdir -p .wingman/issues/completed
mkdir -p .wingman/cache
echo "✓ Created missing directories"

# Rename legacy directories (only if they exist)
if [ -d ".wingman/issues/complete" ]; then
  mv .wingman/issues/complete .wingman/issues/completed
  echo "✓ Renamed .wingman/issues/complete/ → .wingman/issues/completed/"
fi

# Move misplaced files
if [ -f ".wingman/hooks-config.json" ]; then
  mkdir -p .wingman/config
  mv .wingman/hooks-config.json .wingman/config/hooks-config.json
  echo "✓ Moved hooks-config.json to config/"
fi

# Create/update .gitignore
if [ ! -f ".wingman/.gitignore" ]; then
  cat > .wingman/.gitignore << 'EOF'
cache/
sessions/
EOF
  echo "✓ Created .wingman/.gitignore"
else
  # Append missing patterns
  for pattern in "cache/" "sessions/"; do
    if ! grep -q "^${pattern}$" .wingman/.gitignore; then
      echo "$pattern" >> .wingman/.gitignore
      echo "✓ Added pattern to .gitignore: $pattern"
    fi
  done
fi

echo "=== All Fixes Applied ==="
```

**Option 2: Show Commands**

Display the bash commands without executing:

```bash
echo "=== Commands to Fix Drift ==="
echo ""
echo "# Create missing directories"
echo "mkdir -p .wingman/issues/completed"
echo "mkdir -p .wingman/cache"
echo ""
echo "# Rename legacy directories"
echo "mv .wingman/issues/complete .wingman/issues/completed"
echo ""
echo "# Move misplaced files"
echo "mkdir -p .wingman/config"
echo "mv .wingman/hooks-config.json .wingman/config/hooks-config.json"
echo ""
echo "# Create .gitignore"
echo "cat > .wingman/.gitignore << 'EOF'"
echo "cache/"
echo "sessions/"
echo "EOF"
```

**Option 3: Let Me Choose**

Ask about each fix individually:

```
Fix: Create .wingman/issues/completed/ directory
Apply this fix? [y/n]: _

[wait for user response]

Fix: Rename .wingman/issues/complete/ → .wingman/issues/completed/ (12 files)
Apply this fix? [y/n]: _

[and so on...]
```

**Option 4: Cancel**

```
No changes made. Verification report saved for reference.
Run /wingman:verifying-governance again when ready to fix drift.
```

### Phase 8: Re-Verify

**After applying fixes, run verification again:**

```bash
echo "=== Re-Running Verification ==="
echo ""

# Run Phases 2-4 again to confirm all drift resolved

echo "=== Final Status ==="

if [ all checks pass ]; then
  echo "✅ All drift resolved! Governance structure now compliant."
else
  echo "⚠️ Some issues remain:"
  # List remaining issues
fi
```

## Legacy Migration Detection

This skill specifically handles v1.0 → v1.2 migration drift:

| Legacy Location | Current Location | Detection Method |
|-----------------|------------------|------------------|
| `.wingman/issues/complete/` | `.wingman/issues/completed/` | Directory exists check |
| `.wingman/specifications/done/` | `.wingman/specifications/archived/` | Directory exists check |
| `.wingman/hooks-config.json` | `.wingman/config/hooks-config.json` | File exists check |
| `.wingman/workspace-schema.json` | `.wingman/config/workspace-schema.json` | File exists check |
| `.wingman/templates/` | `.wingman/playbooks/templates/` or skill dirs | Directory exists check (now in playbooks or skill dirs) |

When detected, offer to migrate automatically.

## Edge Cases

### No workspace-schema.json

**Situation**: Schema file doesn't exist

**Response**:
```
ERROR: workspace-schema.json not found at .wingman/config/workspace-schema.json

This project may not have wingman v1.2+ initialized.

Options:
1. Run /wingman:initializing-governance to set up governance
2. Manually create .wingman/config/workspace-schema.json from template
3. Update wingman to latest version
```

### Partial .wingman/ Setup

**Situation**: Some directories exist, others don't

**Response**: Report exactly what's missing, offer to create all at once.

### .gitignore Has Extra Patterns

**Situation**: .gitignore has more patterns than schema requires

**Response**: This is fine. Only report MISSING patterns, never complain about extras.

### Schema Version Mismatch

**Situation**: Schema version field doesn't match wingman version

**Response**: Report version mismatch as informational only, don't block.

### Empty Directories

## References

For detailed information, see:

- `references/detailed-guide.md` - Complete workflow details, examples, and troubleshooting
