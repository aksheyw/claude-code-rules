# Performance Optimization

> Model routing table: ~/.claude/docs/model-selection.md

## Context Window

Avoid last 20% for large refactors, multi-file features, complex debugging. Single-file edits and simple fixes are fine at high context.

## Deep Reasoning

Use `ultrathink` + Plan Mode + split role sub-agents for complex tasks.

## Build Failures

Use **build-error-resolver** agent. Analyze errors, fix incrementally, verify after each fix.
