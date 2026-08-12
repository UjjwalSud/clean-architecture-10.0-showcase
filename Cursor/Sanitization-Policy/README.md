# Sanitization Policy

This showcase documents AI-assisted engineering practices without publishing the full internal Cursor configuration or sensitive operational detail.

Related: [Cursor overview](../README.md) · [Curated Rules](../Rules-Curated/README.md) · [Engineering Workflow](../Engineering-Workflow/README.md)

## What may be published

- High-level workflow narratives (plan-first, reference modules, layer responsibilities)
- Curated rule **categories** and short sanitized excerpts
- Architecture principles paraphrased from system architecture docs
- Sanitized evolution themes (what is intentionally deferred vs preferred for new work)
- Relative evidence paths into `.cursor/` and application source

## What is intentionally excluded

| Excluded | Reason |
|----------|--------|
| Full `.mdc` rule files | Internal agent enforcement artifacts / long implementation recipes |
| Full prompt templates | Operational paste-ready steering text |
| Candid architecture-decision backlog | Internal deviations, debt callouts, temporary weaknesses |
| Secrets & credentials | Keys, connection strings, tokens, passwords |
| Environment-specific values | Ports/hosts/timeouts that over-claim production posture when not needed |
| Customer / tenant PII | Names, emails, business data |
| Security-sensitive operational thresholds | Exact rate limits and similar abuse-relevant numbers |
| Complete internal playbooks | End-to-end generation checklists with sample code dumps |

## Selective excerpts policy

Small sanitized excerpts are allowed when they:

1. Demonstrate a real engineering rule, and
2. Do not reproduce an entire rule/prompt file, and
3. Do not include secrets, PII, or candid internal debt language.

If in doubt, paraphrase the principle and link a relative evidence path instead of quoting.

## Evidence

- `.cursor/AGENTS.md`
- `.cursor/rules/`
- `.cursor/docs/02_ARCHITECTURE_DECISIONS.md`
- `.cursor/prompts/`
