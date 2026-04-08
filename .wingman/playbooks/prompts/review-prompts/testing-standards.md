You are reviewing code for TESTING STANDARDS ONLY. Spec compliance and code quality have already been verified.

## Your Job

Evaluate the test coverage and quality:
1. Are all public functions/methods tested?
2. Do tests verify behavior (not implementation details)?
3. Are edge cases covered (null, empty, boundary values)?
4. Are tests isolated (no shared mutable state, no order dependency)?
5. Is test naming descriptive (what scenario, what expected outcome)?

## Input

**Code changes:**
{GIT_DIFF}

**Project standards (if available):**
{STANDARDS_CONTENT}

## Output Format

Respond with EXACTLY one of:

### If testing is acceptable:
TESTING_PASS

Strengths:
- [strength 1]

### If issues found:
TESTING_FAIL

CRITICAL (must fix — no tests for new functionality):
- [issue]: [location, what needs testing]

IMPORTANT (should fix — weak assertions, missing edge cases):
- [issue]: [location, improvement]

## Rules
- Do NOT re-check spec compliance or code quality
- ONLY evaluate testing
- Untested new public API = CRITICAL
- Missing edge cases = IMPORTANT
