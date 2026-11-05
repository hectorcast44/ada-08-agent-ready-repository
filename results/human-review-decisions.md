# Human Review Decisions

## Change
CR-02

### RF-01
Reviewer severity: MUST FIX
Human decision: ACCEPTED
Reason: Slicing before calculating total matches violates CR-02 and FR-05. The count must reflect all matches in the repository.
Required action: Capture `total_matches = len(matched)` before taking the slice `matched[:limit]`.

### RF-02
Reviewer severity: MUST FIX
Human decision: ACCEPTED
Reason: The unit test masked the bug by asserting the sliced count rather than the repository match count.
Required action: Update `tests/test_service.py:test_service_limit_results` to assert `result.total_matches == 3` and `len(result.customers) == 2`.

### RF-03
Reviewer severity: MUST FIX
Human decision: ACCEPTED
Reason: Business rule placement belongs in `src/customer_search/validator.py` as specified in `ARCHITECTURE.md`.
Required action: Implement `validate_limit(limit)` inside `SearchQueryValidator` and delegate to it from `CustomerSearchService`.

### RF-04
Reviewer severity: SHOULD FIX
Human decision: ACCEPTED
Reason: Boundary conditions (0, negative values, booleans) and CLI error handling need regression tests.
Required action: Add unit tests in `tests/test_validator.py` and test CLI error handling for `--limit 0` in `tests/test_cli.py`.

### RF-05
Reviewer severity: SHOULD FIX
Human decision: ACCEPTED
Reason: In-function imports add overhead and clutter method bodies.
Required action: Keep imports at module level in `src/customer_search/validator.py` and `src/customer_search/service.py`.

### RF-06
Reviewer severity: OPTIONAL
Human decision: MODIFIED
Final severity: SHOULD FIX
Reason: In Python, `isinstance(True, int)` is true. A one-line guard prevents `limit=True` from slipping through as `1`.
Required action: Check `if isinstance(limit, bool) or not isinstance(limit, int) or limit <= 0` in `validate_limit`.

## Human Gate
[X] Return to coder-agent
[ ] Ready for final re-review
[ ] Stop / escalate
