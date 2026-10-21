---
name: minimal-sufficient-testing
description: Choose and perform risk-based, minimal-sufficient verification when writing, fixing, refactoring, reviewing, or configuring software. Apply when deciding which tests or checks to run, whether to expand verification, and when to stop. Preserve explicit user requirements and mandatory project checks.
---

# Minimal Sufficient Testing

## Core Principle

Use risk-based, minimal-sufficient verification.

The goal is not to maximize the number of tests. The goal is to obtain enough reliable evidence that the requested change works, with the least necessary time, tool usage, and side effects.

Do not test merely because a test command exists. Before running any check, identify the specific failure mode it is intended to detect.

## 1. Assess the Scope

Inspect only the files, functions, interfaces, and configuration relevant to the requested change.

Do not perform a full repository audit, read unrelated documentation, or inspect unrelated modules unless the task requires it.

Classify the change by its actual risk:

- **Trivial:** comments, documentation, formatting, or other non-functional edits.
- **Localized:** a small change isolated to one function, module, or behavior.
- **Moderate:** changes affecting multiple functions, modules, or interfaces.
- **High risk:** security, authentication, concurrency, database migrations, data integrity, destructive operations, or production-critical behavior.

Choose verification effort based on the real risk, not the apparent complexity of the task.

## 2. Select the Minimum Necessary Checks

Apply these defaults:

- For trivial, non-functional changes, inspect the diff. Do not run tests unless there is a specific reason.
- For localized code changes, prefer one focused test or one targeted execution that directly verifies the changed behavior.
- For bug fixes, prefer a focused regression test or a minimal reproduction of the original failure. Run it after the fix.
- For refactoring, use the smallest existing test set that covers the affected behavior. Do not add redundant checks for unchanged behavior.
- For multi-module or high-risk changes, add checks only when they cover distinct failure modes or are required by the project's established release or safety requirements.

Do not automatically run the full test suite, full linting, type checking, a complete build, and integration tests for every change.

Do not run multiple commands that provide substantially overlapping evidence.

A single strong, targeted check is preferable to several redundant checks. However, do not impose a fixed numerical limit when multiple independent risks genuinely require separate verification.

## 3. Avoid Repeated Verification

Before running a command, consider whether its result could materially change the decision to finish the task.

Once a relevant check passes:

- Do not repeat it without a concrete reason.
- Do not run broader tests merely to reconfirm an already established result.
- Do not rerun checks for code that has not changed.
- Do not repeat repository inspections that have already answered the relevant question.
- If a check fails, investigate the specific failure and run the smallest appropriate check after correcting it.

Expand verification only when new evidence reveals a previously unaddressed risk.

## 4. Reuse Existing Infrastructure

Prefer existing tests, scripts, build commands, and development environments.

Do not create a new testing framework, install additional dependencies, regenerate unrelated files, or modify project configuration solely to perform a low-value check.

If a relevant test cannot run because the environment or dependencies are unavailable, report the limitation. Do not spend excessive effort repairing the test environment unless that work is necessary for the requested task.

Never modify unrelated code simply to make a test pass.

## 5. Respect Mandatory Requirements

Minimal testing does not mean skipping essential safety checks.

Retain broader verification when it is justified by:

- Explicit user requirements.
- Repository-specific instructions.
- Required CI, release, or deployment procedures.
- Material security, data integrity, compatibility, or regression risks.
- Evidence that the focused check is insufficient.

When broader verification is necessary, select the smallest broader check that adequately addresses the risk. Explain the reason briefly when it is not obvious.

## 6. Define a Clear Stop Condition

Stop testing when all of the following applicable conditions are satisfied:

1. The requested change has been implemented.
2. The diff has been reviewed for relevant mistakes or unintended changes.
3. The smallest appropriate verification has passed, or any unavailable verification has been clearly identified.
4. No known, material, unresolved issue requires another check.

Do not pursue exhaustive certainty for a bounded, low-risk task.

Do not introduce extra tests, audits, benchmarks, or refactoring merely to make the result appear more thoroughly verified.

## 7. Report Results Concisely

At completion, report:

- What changed.
- The relevant check performed and its actual result.
- Any material limitation or unresolved risk.

Mention skipped tests only when their absence materially affects confidence or when the user expects them.

Never claim that a test, build, or validation passed if it was not actually performed.

## Final Rule

Prefer the smallest sufficient verification over exhaustive verification.

Every additional check must justify its cost by addressing a distinct, relevant risk. Once sufficient evidence has been obtained, stop and deliver the result.
