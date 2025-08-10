# FOUNDATION

## Build Philosophy
- **Simplicity First**: Minimal stack (Flask + HTML/CSS/JS). Prefer straightforward, human-readable code over cleverness.
- **Agent-Aware**: Written for agentic workflows. Decisions are pre-made and reused to avoid drift.
- **Performance Conscious**: Fast enough-by-default with basic caching and graceful degradation.
- **File Preservation**: Do not mutate source codebases; write outputs to dedicated dirs.
- **Human-Legible Outputs**: Structures and responses designed for non-experts to skim.
- **Debug-First**: Ad-hoc debugging during development; formal tests deferred until the end.

## Roles & Division of Labor
- **Cursor**: Backend logic, parsers, LLM integration, graph construction, API.
- **Replit**: Frontend (HTML/CSS/JS), forms, graph visualization, UX.
- **ChatGPT**: Project scoping, prompt refinement, doc grooming.

## Invariants (Do Not Change)
- **Response schema**: `{ success, data, error }` for all JSON endpoints.
- **Endpoints**: `/api/analysis/upload`, `/api/analysis/<id>/status`, `/api/analysis/<id>/result`, `/api/analysis/<id>/export`.
- **Progress**: Polling-based status updates (no WebSockets).
- **LLM Access**: Single centralized client with retries, backoff, caching, and fallback.
- **Directory Names**: Keep established module folders and file names stable.
- **Non-Goals**: No auth/DB/WebSockets/queues/custom ML in core variant.

## Decision Log (Terse)
Use this format for each decision: `YYYY-MM | Decision | Why | Status(Immutable/Tentative)`
- `2024-01 | Flask + HTML/CSS/JS stack | simplest viable | Immutable`
- `2024-01 | Polling for progress | simpler than WS | Immutable`
- `2024-01 | Single LLM client + cache | cost/latency control | Immutable`
- `2024-01 | Exports: json,yaml,csv,dot,html,mermaid | user needs | Tentative`

## Data Handling Policy
- **Allowed to LLM**: Non-sensitive code/text needed for analysis; redact secrets/API keys.
- **Redaction**: Mask `.env`, credentials, tokens, private keys, and obvious secrets before sending.
- **Retention**: Cache TTL default 24h; cache is file-based and purgeable.
- **Local-Only Mode**: If `LLM_DISABLED=true`, run AST-only analysis and skip remote calls.
- **Access Keys**: Scope keys to analysis-only usage; never persist keys in logs/exports.

## Output Goals
- **Codebase Analysis**: Hierarchical graph (HLD/LLD) with metadata.
- **Agent Detection**: Simple pattern recognition of common AI libraries.
- **Exports**: JSON, YAML, CSV, DOT, HTML, Mermaid.
- **UX**: Upload → progress → results → export, in one page.

