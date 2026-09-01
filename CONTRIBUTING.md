# Contributing

## Applying the method to a new scope

See `README.md` for the concept and `AGENTS.md` for the orchestration commands. Copy
`config/scope.example.md` to `config/scope.md` and fill it in per run.

## Changing the method itself

The three subagent `.md` files in `.claude/agents/` **are** the method — edit them directly rather
than maintaining a separate spec. Keep them scope-agnostic: if a change only makes sense for one
specific run, it belongs in that run's `config/scope.md`, not here. The ethical boundary in
`build-pattern-scanner.md` is load-bearing — any edit to that spec must preserve it, not just avoid
contradicting it.

`scripts/verify_sourcing.py` is the one part of this repo that's genuinely testable code, not
instructions for an agent to follow — it's ported byte-identical from
[`agentic-grounded-persona-eval`](https://github.com/qte77/agentic-grounded-persona-eval), the
sibling repo this one's output feeds into. Any change to it needs its test in
`tests/test_verify_sourcing.py` updated first — TDD: red, green, refactor.

```sh
uv run pytest
```

## Commit style

Branch per topic; commit by topic (`feat`/`fix`/`test`/`docs`/`chore`); push + squash-merge only if
checks pass; delete stale branches after merge.
