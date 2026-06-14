# Investigate

## Core move

Replace assumptions with evidence. Use tools to check — don't reason about what might be true.

This is not a thinking mode. It is a doing mode. Every action below is something you execute, not something you consider.

## Actions

### Read before modifying

Before changing any file, read it completely. Read the surrounding code too — context prevents breaking adjacent functionality. Don't assume you know what a file contains, even if you've seen similar files before.

### Grep before guessing

If your plan depends on a function, pattern, type, or behavior existing, verify it:

- `grep -rn "functionName" --include="*.ts" --include="*.tsx"`
- `grep -rn "pattern" src/`
- `find . -name "*.config.*" -type f`
- `find . -path "*/auth/*" -type f`

If grep returns nothing, your plan has a false assumption. Adjust before proceeding.

### Check history when it matters

For bugs: when did this break? For design: why was it built this way?

- `git log --oneline -10 -- path/to/file`
- `git log --all --oneline --grep="keyword"`
- `git blame path/to/file`
- `git diff HEAD~5 -- path/to/file`

Commit messages and recent diffs often explain WHY something is the way it is. This prevents "fixing" intentional behavior.

### Run before claiming

If you say "this test passes" — run it. If you say "this builds" — build it. If you say "this handles edge case X" — trace the exact code path with concrete values.

### Check types and interfaces

If using a library, API, or internal module: read its type definitions, interface declarations, or documentation. Don't rely on memory or pattern matching from similar-looking code.

### Trace the data flow

For bugs and integration work: trace the actual flow from input to output. Check each transformation step. Don't trust your mental model — verify each hop.

## What to look for

- Dependencies between components you'll modify
- Error handling patterns used in surrounding code (match them)
- Test coverage for the area you're touching
- Recent changes that might interact with your work
- Configuration or environment variables that affect behavior
- Existing utilities or helpers that already do what you're about to build

## Record what you find

After investigating:

- **Found**: Facts confirmed with tools (with source: file, line, command)
- **Missing**: Information you need but couldn't find
- **Contradictions**: Where code, docs, tests, or types disagree with each other

Contradictions are high-value signals — they often point to the actual bug or the real design question.

## Failure checks

- Planning changes without reading the target file first
- Assuming function signatures, return types, or behavior from memory
- Checking easy things while leaving the critical assumption unchecked
- Reading one file when the behavior spans multiple files
- Trusting variable or function names as documentation of behavior
- Skipping the test files (they document intended behavior and edge cases)

## Done when

Your understanding is grounded in what you read, ran, and checked — not what you assumed or predicted. You know what exists, where it is, and how it currently works.
