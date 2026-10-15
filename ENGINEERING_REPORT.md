# BountyJudge V1 engineering report

## Delivered

- `contracts/BountyJudge.py`: fixed-BJ-001 Intelligent Contract.
- `tests/direct/test_bounty_judge.py`: Direct Mode and consensus tamper-rejection tests.
- `README.md`: architecture, security boundaries, compatibility, and runbook.

## Compatibility research

Official GenLayer documentation was reviewed on 2026-10-14. Its current contract convention is `from genlayer import *`, `gl.Contract`, `TreeMap`, and public decorators. `genvm-linter` (`genvm-lint check`) is the documented linter, while pytest-based `genlayer-test` Direct Mode is the recommended first test layer and provides `mock_web`, `mock_llm`, and `run_validator`.

Stable Studionet is chain 61999. Studio development preview is chain 61997 and needs matching release-candidate tooling. This project targets the stable SDK family only and contains no Studio-dev configuration. The dependency header comes from the current first-contract documentation example.

## Consensus review

R3 is an LLM classification and GitHub web data is non-deterministic, so the contract uses custom `run_nondet_unsafe`. A validator rejects malformed leader data, independently refetches both SHA-pinned files, independently re-runs R3, then compares all decision flags. It does not accept mere JSON shape or a leader assertion. Transient source/provider disagreement safely prevents state mutation.

## Verification actually performed

- Official documentation/source review: performed.
- Python 3.12.10 was located at `C:\\Users\\USER\\AppData\\Local\\Python\\pythoncore-3.12-64\\python.exe`; a project-local `.venv` was created without modifying Python 3.14 or global defaults.
- Syntax compilation: passed.
- Direct Mode: passed, 7 tests.
- AST lint, full `genvm-lint check`, and ABI/schema extraction: passed using a project-local cache and pinned `v0.2.12` runner artifact.
- The installed GenLayer CLI reports `0.40.0-rc2`. It was used only for a read-only network-preset inventory; it is not treated as compatibility evidence for this stable-family contract.
- Studio, GLSim, hosted-network, wallet, and transaction testing: intentionally not performed. This task did not authorize deployments, signing, funds, provider credits, or resets.

## Release gate

Before deployment, run isolated GLSim or local Studio with multiple validators and pin the dependency header only after confirming it matches the chosen stable runner image.
