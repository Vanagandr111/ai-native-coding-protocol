# Examples

## Function Passport

```python
# intent: calculate invoice total with discounts and tax
# allies: billing_total, order_total, invoice_amount
# sidefx: none
# purity: pure
# errors: InvalidInvoiceError
def calculate_invoice_total(invoice: Invoice) -> Money:
    ...
```

## Module Navigation Header

```python
# module-purpose: user session validation and session-derived identity
# entry-points: validate_user_session, resolve_current_user
# main-flows: request -> session cookie -> session lookup -> user id
# related-modules: auth.tokens, auth.errors, users.models
```

## Architecture Manifest

```md
## Architecture Manifest

Goal:
Add password reset without duplicating email or token logic.

Reuse:
- `EmailSender`
- `TokenGenerator`
- `UserRepository`

Files:
- `auth/password_reset.py`: reset request and confirmation flow
- `auth/tokens.py`: extend token purpose enum
- `api/auth_routes.py`: HTTP boundary

Flow:
request -> validate email -> create reset token -> send email -> confirm token -> update password

Verification:
- unit tests for token purpose
- flow test for reset confirmation
```

## Temporary Code

```python
# TEMP:
# reason: accept legacy reset tokens during migration
# remove-after: 2026-08-01 or all reset emails generated after v3 deploy expire
def parse_legacy_reset_token(raw_token: str) -> ResetToken:
    ...
```

## Bad Pattern

```text
Need password reset.
Create:
- new mailer
- new token table
- new error type
- new route helper
- new config loader
```

Problem: parallel infrastructure. The change ignores existing extension points.

## Better Pattern

```text
Need password reset.
Search existing auth/email/token code.
Extend token purpose.
Reuse mailer.
Add focused reset flow.
Test the new purpose and endpoint behavior.
```
