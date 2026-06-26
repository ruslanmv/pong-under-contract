# Evidence — this game was written by an LLM, under contract

This file records the actual run, so the claim *"coded by Claude Opus 4.8, under a Matrix
Builder contract"* is verifiable rather than asserted.

## Environment

| | |
|---|---|
| Contract engine | `agent-generator` 0.2.0 (the `mb` CLI) |
| Coder driver | GitPilot (`gitcopilot`) — `gitpilot generate` |
| LLM provider | `claude` (`GITPILOT_PROVIDER=claude`) |
| Model | **`claude-opus-4-8`** (Anthropic Claude Opus 4.8) |
| Endpoint | `https://api.anthropic.com/v1/messages` (`anthropic-version: 2023-06-01`) |

## 1. The contract (Matrix Builder)

```text
$ mb init "A retro Pong arcade game … single self-contained HTML file …" --quality standard --title "Pong Under Contract v2"
Initialized .mb/ for Standard Matrix Bundle
  project   bp-d91790c1e6ea   ·   version v1.0.0   ·   quality standard

$ mb next "Build the complete Pong game in one self-contained frontend/index.html"
Batch 01  Build the complete Pong game  (add-feature)

$ mb prompt --coder gitpilot
# → coder-prompts/gitpilot.md   (allowed files: frontend/index.html only)
```

The contract-bound prompt the model received is committed verbatim at
[`coder-prompts/gitpilot.md`](coder-prompts/gitpilot.md).

## 2. The LLM wrote the code (GitPilot + Claude Opus 4.8)

```text
$ export GITPILOT_PROVIDER=claude
$ export GITPILOT_CLAUDE_MODEL=claude-opus-4-8
$ export ANTHROPIC_API_KEY=sk-ant-…          # redacted
$ gitpilot generate -m "$(cat coder-prompts/gitpilot.md)" -o .

Provider: claude · Model: claude-opus-4-8
Output: /home/user/pong-under-contract

Generating... done

  Created: frontend/index.html (15027 bytes)

Generated 1 file(s)
```

**Scope check:** the model created exactly **one** file — `frontend/index.html` — and nothing
outside the contract's allow-list.

## 3. The contract validated the output (fail-closed)

```text
$ mb check frontend/index.html
MATRIX_STATUS: approved  score=100
  committed mc-caf49981d56a        # immutable, versioned Matrix Commit
```

Exit code `0` (approved). Had the model touched a file outside the allow-list, `mb check`
returns `needs-repair` (exit 1) or `rejected` (exit 2) and the change is blocked.

## 4. Independent sanity checks

- `node --check` on the extracted `<script>` → **valid JavaScript**, no syntax errors.
- Structural grep confirmed: `<canvas>`, `requestAnimationFrame`, keyboard + touch handlers,
  `WIN_SCORE = 11`, paddle/ball physics, scoring, and a win screen are all present.

## Notes

- The model output is non-deterministic; re-running may produce a different (still
  contract-valid) implementation. The governance layer — blueprint, allow-list, `mb check`
  verdict — is deterministic and unchanged across runs.
- The Anthropic API key used for this run was temporary and has been rotated.
