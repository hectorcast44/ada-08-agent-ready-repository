# Review Contract

## Change
CR-02

## Requirements / Acceptance Criteria
- FR-05: Results must accurately state total matches and details for each customer record.
- FR-06: Invalid inputs must produce descriptive errors and non-zero exit codes without tracebacks.
- NFR-02: Search service and business validation remain decoupled from CLI.
- AC-05: Output includes accurate total count and customer details.
- AC-06: Validation errors are trapped and reported cleanly.
- AC-08: Service operates independently without CLI dependencies.

## Architecture Constraints
- Expected dependency flow: CLI -> Service -> Validator and Repository -> Models.
- Business-rule location: Query and parameter validations live in `src/customer_search/validator.py`, not ad-hoc in CLI or dispersed across layers.
- Protected boundaries: `data/customers.json` is protected and must not be altered. Existing test assertions must not be weakened.

## Review Rules
- Verify requirement coverage against CR-02 and referenced ACs.
- Verify every called API and dependency exists in the codebase.
- Check architecture boundaries, especially input validation placement.
- Inspect tests for masking, over-mocking, or false confidence.
- Check regression behavior on existing commands and service calls.
- Identify unnecessary technical debt introduced by the change.
- Cite concrete evidence (file and line number) for every finding.

## Severity
MUST FIX:
Requirement violation, regression, security or integrity risk, non-existent API or dependency, broken architecture constraint, or defect that makes the change unsafe to approve.

SHOULD FIX:
Significant maintainability, coverage, design, or clarity issue that does not block the requested behavior.

OPTIONAL:
Non-blocking improvement outside the minimum acceptance threshold.
