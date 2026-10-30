# Implicit Knowledge Found

## IK-01
What:
The test execution relies on an ambient `pytest` installation without a tracked `requirements.txt` or `pyproject.toml`.
How was it discovered:
Inspecting root files revealed no dependency manifests. Running `pytest` only worked because it was pre-installed in the local environment.
Should this remain implicit?
NO
Where should it become explicit?
AGENTS.md / scripts/verify_repo.sh / requirements.txt
Risk:
A new developer or a fresh agent container without pre-installed pytest cannot run tests or verify changes.
Remediation:
Add a `requirements.txt` file and document reproduction commands in `AGENTS.md` and `scripts/verify_repo.sh`.

## IK-02
What:
`data/customers.json` contains test data that CLI tests and default searches rely upon directly.
How was it discovered:
Examining `cli.py` and `tests/test_cli.py` showed hardcoded dependencies on `data/customers.json`.
Should this remain implicit?
NO
Where should it become explicit?
AGENTS.md and docs/REPO_MAP.md under protected paths.
Risk:
An agent or developer could edit or truncate this data file to pass an ad-hoc test, breaking existing test suites.
Remediation:
Document `data/customers.json` as a protected read-only path in `AGENTS.md` and add automated path checks in `scripts/check_repo_contract.py`.

## IK-03
What:
No linter, code formatter, or contract verification script existed to validate repository hygiene.
How was it discovered:
`AGENTS.md` only mentioned running tests, omitting linting and formatting targets.
Should this remain implicit?
NO
Where should it become explicit?
AGENTS.md, scripts/verify_repo.sh, and .github/workflows/verify.yml
Risk:
Different agents and developers produce irregular formatting and unvalidated repository structure.
Remediation:
Create `scripts/verify_repo.sh` to run tests, ruff, and contract validation in a single command.
