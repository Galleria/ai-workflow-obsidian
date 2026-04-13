---
pattern: auth-otp-flow
applies-to: registration, login, sensitive-action-reauthentication
---

# Pattern — OTP authentication (phone + email)

Reusable pattern for KYC-lite flows: registration, password reset, sensitive transaction re-auth.

## When to use
- Customer-facing apps where full KYC is overkill
- Markets where SMS + email are ubiquitous (TH, SEA)

## Canonical sequence

```mermaid
sequenceDiagram
  actor U as User
  participant C as Client (app/web)
  participant API as Auth API
  participant SMS as SMS Gateway
  participant Mail as Email Provider
  participant Store as OTP Store (Redis, TTL)

  U->>C: submit phone + email + consent
  C->>API: POST /auth/otp/request {phone, email}
  API->>API: rate-limit check (per phone, per IP)
  API->>Store: SET otp:phone=<code>, TTL=5min
  API->>Store: SET otp:email=<code>, TTL=5min
  par
    API->>SMS: send code to phone
  and
    API->>Mail: send code to email
  end
  API-->>C: 200 {request_id}

  U->>C: enter both codes
  C->>API: POST /auth/otp/verify {request_id, phone_code, email_code}
  API->>Store: GET & compare (constant-time)
  alt both match
    API->>Store: DEL
    API-->>C: 200 {verified_token}
  else mismatch
    API->>Store: INCR fail_count
    API-->>C: 401 {attempts_left}
  end
```

## Rules of thumb

| Concern | Recommendation |
|---|---|
| Code length | 6 digits numeric (balance usability vs entropy) |
| TTL | 5 minutes |
| Retry limit | 3 attempts per request; 3 requests per 15 min per phone |
| Delivery parallel | send SMS + email in parallel; don't block on SMS gateway |
| Idempotency | `request_id` unique per OTP session |
| Storage | Redis with EXPIRE, never in primary DB |
| Comparison | constant-time, never log the code |
| Rollout pauses | circuit-breaker on SMS gateway failure → fallback to email-only with UX note |

## PDPA notes
- OTP itself is not PII but phone/email are → storage policy applies
- Consent capture **must** happen **before** OTP send (otherwise processing without lawful basis)

## Anti-patterns to avoid
- Single OTP for both channels → weaker than two-channel; pick one or do two
- OTP in URL query string → leaks in logs/referrers
- No rate limit → SMS flooding / cost attack
- Logging OTP for "debugging" → compliance breach

## Related
- `library/patterns/consent-capture.md` (to be added)
- SRS Thai template §3.2 FR-001
