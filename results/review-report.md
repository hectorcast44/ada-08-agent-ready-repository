# Independent Review Report

## Review Context
Change ID: CR-02
Branch / PR: ada09/cr-02
Commit reviewed: 1e9b45a
Reviewer agent: Independent Reviewer Agent
Model: Gemini 3.8 Flash
Tools / permissions: Read-only codebase inspection

## Requirements Reviewed
- CR-02: Add optional limit parameter to search while preserving total matching records.
- FR-05: Output includes accurate total count and customer details.
- FR-06: Clean error handling for invalid input without unmanaged tracebacks.
- NFR-02: Business logic and search services remain decoupled from CLI.
- AC-05: Results state total matches and details for each matching customer.
- AC-06: Validation errors are trapped and reported cleanly to stderr with exit code 1.
- AC-08: Service operates independently without CLI dependencies.

## Verification Evidence Reviewed
- Automated verification: `./scripts/verify_repo.sh` reported 46 passed tests and clean compilation.
- Diff inspection: `results/pr.diff` (commit 1e9b45a vs base 10465a2).
- Coder report: `results/coder-report.md` noted inline validation and sliced count assertions.
- Runtime check: `python3 -m src.customer_search.cli "garcia" --limit 1 --json` reports `"total_matches": 1` instead of `3`.

## Findings

### RF-01: MUST FIX
Category: Requirement violation / Data accuracy
Requirement / AC: CR-02, FR-05, AC-05
File / Line: `src/customer_search/service.py`: 79-87
Evidence:
```python
if limit is not None:
    matched = matched[:limit]

return SearchResult(
    query=normalized_query,
    field=field_target,
    total_matches=len(matched),
    customers=matched,
)
```
Problem: `total_matches` is calculated as `len(matched)` after slicing. It reports the slice length instead of preserving the total count of matches in the repository.
Risk: Callers and UI consumers receive inaccurate total match counts whenever a limit is applied.
Recommended next step: Store `total_matches = len(matched)` before slicing, and pass that total to `SearchResult`.

### RF-02: MUST FIX
Category: Test masking / False confidence
Requirement / AC: CR-02 Verification item 1, AGENTS.md ("never weaken assertions")
File / Line: `tests/test_service.py`: 112-121
Evidence:
```python
def test_service_limit_results():
    custom_repo = InMemoryCustomerRepository([
        Customer("1", "Alice Active", "alice@example.com", "111", is_active=True),
        Customer("2", "Bob Active", "bob@example.com", "222", is_active=True),
        Customer("3", "Charlie Active", "charlie@example.com", "333", is_active=True),
    ])
    service = CustomerSearchService(custom_repo)
    result = service.search("example", field="email", limit=2)
    assert len(result.customers) == 2
    assert result.total_matches == 2
```
Problem: With 3 matching records in the test repository, the test asserts `result.total_matches == 2`. This assertion matches the bug in RF-01 and produces false confidence in green tests.
Risk: Defects pass automated test suites undetected.
Recommended next step: Update assertion to `assert result.total_matches == 3` and `assert len(result.customers) == 2`.

### RF-03: MUST FIX
Category: Architecture boundary violation
Requirement / AC: `results/review-contract.md` (Business-rule location), CR-02 Verification item 2
File / Line: `src/customer_search/service.py`: 48-52
Evidence:
```python
if limit is not None:
    if not isinstance(limit, int) or limit <= 0:
        from src.customer_search.exceptions import ValidationError
        raise ValidationError("Search limit must be a positive integer.")
```
Problem: Validation is embedded directly inside `CustomerSearchService.search`. `src/customer_search/validator.py` was left untouched, violating the architectural boundary that places input validations inside the validator component.
Risk: Bypasses the validator layer and duplicates validation logic if other entry points call validation.
Recommended next step: Add `validate_limit(limit)` to `SearchQueryValidator` in `src/customer_search/validator.py` and call it from `CustomerSearchService`.

### RF-04: SHOULD FIX
Category: Test coverage gap
Requirement / AC: CR-02 Verification items 2 and 3
File / Line: `tests/test_validator.py`, `tests/test_cli.py`: 96-103
Evidence: `tests/test_validator.py` has no tests for limit validation. `tests/test_cli.py` only verifies that JSON output has 1 customer, without checking `total_matches` or testing error handling for `--limit 0`.
Problem: Boundary cases (zero, negative, non-integer) and CLI error handling lack automated regression tests.
Risk: Future changes could break limit validation without breaking any test.
Recommended next step: Add unit tests in `tests/test_validator.py` for limit validation and CLI tests in `tests/test_cli.py` for `--limit 0` error exit.

### RF-05: SHOULD FIX
Category: Code maintainability
Requirement / AC: NFR-02, Code style
File / Line: `src/customer_search/service.py`: 50
Evidence:
```python
from src.customer_search.exceptions import ValidationError
```
Problem: `ValidationError` is imported inside the method body instead of module level.
Risk: In-function imports create unnecessary overhead and obscure module dependencies.
Recommended next step: Import `ValidationError` at module level in `src/customer_search/service.py` or `validator.py`.

### RF-06: OPTIONAL
Category: Type checking precision
Requirement / AC: Input validation robustness
File / Line: `src/customer_search/service.py`: 49
Evidence:
```python
if not isinstance(limit, int) or limit <= 0:
```
Problem: In Python, `bool` is a subclass of `int`. Passing `limit=True` evaluates to 1 and passes validation.
Risk: Unlikely to occur in CLI, but programmatic callers could pass a boolean by mistake.
Recommended next step: Add `isinstance(limit, bool)` check or check `type(limit) is not int`.

## Missing Evidence / Open Questions
- Missing evidence: Tests verifying invalid limit inputs against `SearchQueryValidator`.
- Open questions: None.

## Summary
MUST FIX: 3
SHOULD FIX: 2
OPTIONAL: 1

## Reviewer Recommendation
[X] NOT READY
[ ] READY AFTER MUST FIX
[ ] READY FOR HUMAN REVIEW

This recommendation is not a merge approval.
