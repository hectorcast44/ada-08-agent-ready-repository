# Change Request

Change ID: CR-02

## Goal
Add an optional result limit parameter to customer search. The `CustomerSearchService.search` method must accept an optional `limit: int | None = None` argument. When `limit` is provided, it must be a positive integer (> 0). Invalid limit values must raise `ValidationError`. When `limit` is applied, the returned `SearchResult.customers` list contains at most `limit` customers, while `SearchResult.total_matches` preserves the total count of matching customers in the repository. The CLI in `src/customer_search/cli.py` must support an optional `--limit` flag forwarding the value to the service. When omitted, all matching customers are returned.

## Requirements Covered
- FR-05
- FR-06
- NFR-02
- AC-05
- AC-06
- AC-08

## Constraints
- Follow AGENTS.md.
- Use REQUIREMENTS.md and SPEC.md as the source of truth.
- Preserve architecture boundaries.
- Do not weaken existing tests.
- Do not add new external dependencies.
- Keep data/customers.json untouched.

## Verification
1. Service tests in `tests/test_service.py` verify that `limit=N` returns at most N customers while `total_matches` reflects the full count of matching records.
2. Validation tests in `tests/test_validator.py` verify that `limit <= 0` or non-integer values raise `ValidationError`.
3. CLI tests in `tests/test_cli.py` verify `--limit` constrains output rows and supports `--json`.
4. `./scripts/verify_repo.sh` passes with exit code 0 (`VERIFY_REPO: PASS`).

## Human Approval
Approved to implement: YES
Approved by: Tech Lead / Human Reviewer (Hector)
Date: 2026-11-05
