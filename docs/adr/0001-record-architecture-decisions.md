# 1. Record architecture decisions

Date: 2026-10-05

## Status

Accepted

## Context

This project involves many technical choices. Months from now I will not
remember why each one was made, and future changes are safer when the original
reasoning is written down.

## Decision

Significant decisions are recorded as Architecture Decision Records (ADRs) in
`docs/adr/`, numbered in order, each with a context, the decision, and its
consequences. An ADR is never edited after it is accepted; a new ADR
supersedes it instead.

## Consequences

- There is a lightweight, searchable history of why the system looks the way it does.
- Writing an ADR forces me to state alternatives and trade-offs explicitly.
- It adds a small amount of overhead to each significant decision.
