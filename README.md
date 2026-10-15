# BountyJudge V1

`BountyJudge` is a GenLayer Intelligent Contract for fixed bounty `BJ-001`, Developer Documentation Challenge. A caller supplies a public GitHub owner, repository, and full 40-character commit SHA. The contract reads raw GitHub URLs pinned to that SHA and records a compact result after consensus.

| ID | Requirement | Evidence / decision |
| --- | --- | --- |
| R1 | Nonempty `README.md` | Pinned raw-file fetch |
| R2 | Nonempty `LICENSE` | Pinned raw-file fetch; no legal-validity claim |
| R3 | Actionable setup or run instructions | Narrow JSON LLM classification of pinned README |

Stored record format: `BJ-001|owner|repo|sha|R1R2R3PASS`, e.g. `BJ-001|acme|widget|<sha>|1111`. Raw file bodies and LLM reasoning are not stored.

## Consensus and security

`evaluate` uses `gl.vm.run_nondet_unsafe`. A leader fetches README/LICENSE and classifies R3. Every validator independently refetches the same commit-pinned evidence, reruns R3, and accepts only a schema-valid result whose four decision flags exactly match. It never treats the leader's LLM output as trusted.

The R3 prompt treats README text as untrusted data and blocks README-embedded evaluator instructions. Owner/repo characters and the exact 40-character hexadecimal SHA are validated; README input is capped at 12,000 characters. This MVP does not assess alternate filenames, private repositories, redirects, dependency safety, or legal validity of a license.

## Compatibility target

The contract follows current stable GenLayer documentation: `from genlayer import *`, `gl.Contract`, `TreeMap`, public decorators, `gl.nondet.web.get`, `gl.nondet.exec_prompt`, and `gl.vm.run_nondet_unsafe`. Its dependency header is copied from the current official first-contract example. The target is stable Studionet (chain 61999), not Studio development preview (chain 61997); do not mix that stable family with Studio-dev release-candidate components.

## Local verification

Python 3.12+ is required. In an isolated environment:

```powershell
python -m pip install -r requirements.txt
genvm-lint check contracts/BountyJudge.py
pytest tests/direct -v
```

On Windows, invoke the project's `.venv\\Scripts\\python.exe` explicitly and
keep the GenLayer runner cache project-local. The validation report records the
exact environment variables and commands used.

Tests use official Direct Mode fixtures and mock every web/LLM response. They require no Docker, wallet, hosted Studio, validators, or funds. Integration tests are intentionally not configured because this delivery does not deploy anywhere.
