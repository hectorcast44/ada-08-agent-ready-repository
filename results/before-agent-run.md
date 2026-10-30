# BEFORE Agent Run

## Run Context
Agent: Antigravity Assistant
Model: Gemini 3.8 Flash
Tools: read_file, edit_file, run_command
Permissions: Workspace read and execute
Baseline commit/tag: ada08-baseline

## Clarification Questions
None requested. The instructions directed the agent to avoid asking questions unless necessary to proceed safely.

## Repository Exploration
Because no repository map existed, locating entry points and test conventions required exploratory directory queries:
- `ls -la` to list files in root directory.
- `AGENTS.md` to look for project instructions (found 24 lines of general guidance without command blocks).
- `README.md` to identify project context (found 226 lines of assignment prose).
- `src/customer_search/` to inspect implementation (`models.py`, `service.py`, `cli.py`, `validator.py`).
- `tests/` to inspect existing tests (`test_service.py`, `test_cli.py`, `conftest.py`).

## Incorrect Assumptions
1. The agent assumed a test fixture named `sample_data_file` existed in `tests/conftest.py`, leading to an initial test failure in `tests/test_cli.py`.
2. The agent assumed that running `pytest` was the only required validation, because no lint command, type checker, or repo contract verification was documented.

## Failed / Unknown Commands
1. Pytest initially failed on the new test with `ERROR at setup of test_cli_active_only_flag: fixture 'sample_data_file' not found`. The agent had to re-read `test_cli.py` to correct the call to use default parameters.
2. No standard lint or code style command existed to verify formatting consistency.

## Out-of-Scope Changes
None committed to source files outside the search service and CLI, but exploratory reads covered parts of the repository that were irrelevant to the change.

## Validation Evidence
- Command executed: `pytest -v`
- Result: 44 passed in 0.03s.
- Verified: `test_service_active_only_filter` and `test_cli_active_only_flag`.

## Final Result
PASS
