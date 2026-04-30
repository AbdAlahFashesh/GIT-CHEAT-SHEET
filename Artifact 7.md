# Assignment S7 – Architecture Decision Record

## Decision Scenario

For the Campus Lab Booking system, students and lab administrators must log in before they can book, cancel, approve, or manage lab slots. The system needs a safe way to remember who the user is after login.

The decision is between:

- **Option A:** Server-side session lookup
- **Option B:** Stateless JWT authentication

---

# Option Comparison

## Option A – Server-side Session Lookup

In this option, the server stores session data after login. The browser only keeps a session ID cookie. Every request sends the session ID, and the backend checks the session store to identify the user.

### Pros

1. **Easier to revoke access**  
   If a student logs out, loses access, or has a suspicious login, the server can delete the session immediately.

2. **Less sensitive data stored on the client**  
   The browser only stores a session ID, not user roles, permissions, or booking-related claims.

3. **Good fit for a simple web application**  
   The Campus Lab Booking system is likely a normal browser-based system, so cookie sessions are straightforward.

4. **Simpler security control**  
   The backend remains the source of truth for whether a session is valid.

### Cons

1. **Requires session storage**  
   The system needs a database table, Redis, or another session store.

2. **Can be harder to scale horizontally**  
   If there are multiple backend servers, they must all access the same session store.

3. **Backend must check session state on every request**  
   This adds a small performance cost.

4. **More server responsibility**  
   The backend must manage expiry, cleanup, logout, and session invalidation.

---

## Option B – Stateless JWT Authentication

In this option, the server gives the browser a signed token after login. The token contains claims such as user ID, role, and expiry time. The backend checks the signature instead of looking up a session.

### Pros

1. **No server-side session store required**  
   The backend does not need to save session records for every logged-in user.

2. **Scales well across multiple services**  
   Any backend service can validate the token if it knows the signing key.

3. **Useful for APIs and mobile clients**  
   JWTs are often convenient when the same backend serves web, mobile, or external API clients.

4. **Can reduce database/session lookups**  
   The server can validate the token without checking a session store every time.

### Cons

1. **Harder to revoke immediately**  
   If a token is valid for 30 minutes, the user may keep access until it expires unless extra blacklist logic is added.

2. **More security mistakes are possible**  
   Problems such as long token lifetimes, weak storage, or putting too much data in the token can create risk.

3. **Token contents can become outdated**  
   If a user role changes from student to admin or blocked, an old token may still contain the previous claims.

4. **More complex for this system than necessary**  
   The Campus Lab Booking system does not clearly need cross-service or mobile-first authentication yet.

---

# Architecture Decision Record

## ADR-007: Use Server-Side Sessions for User Authentication

**Status:** Accepted  
**Date:** 2026-04-30

---

## Context

The Campus Lab Booking system needs authentication because students must be identified before they can book or cancel lab slots, and administrators must be identified before they can approve, reject, or manage bookings.

This decision matters because authentication affects security, maintainability, user experience, and future development. If the system uses a weak or overly complex authentication design, future features such as role-based access, booking cancellation, admin approval, and audit history become harder to build safely.

The main criteria used for this decision were:

- **Security:** Can the system quickly stop access when needed?
- **Complexity:** Can a small student team implement and understand the solution?
- **Maintainability:** Will the decision still be understandable six months later?
- **Fit for system shape:** Does the solution match a normal browser-based booking system?
- **Reversibility:** Could the team change the decision later if the system grows?

---

## Decision

We will use **server-side session lookup** for the Campus Lab Booking system.

After login, the server will create a session and store it server-side. The browser will keep only a session ID cookie. On each protected request, the backend will check the session store to confirm the user identity and role.

JWT authentication is rejected for now because the system does not currently need stateless authentication across many services, mobile apps, or external API clients. The extra complexity and harder token revocation are not worth it for the current system.

---

## Consequences

### Positive Consequences

- The backend can immediately invalidate a session if the user logs out or access needs to be removed.
- User roles and permissions remain controlled by the server.
- The design is easier for the team to understand, debug, and explain.
- The authentication approach fits a browser-based booking system.
- Security behavior is easier to test because the server is the source of truth.

### Negative Consequences

- The system needs a session store.
- If the system later uses multiple backend servers, all servers must share session data.
- The backend must manage session expiry and cleanup.
- Each protected request needs a session lookup.

### Reversibility Note

This is mostly a **two-way door decision**. If the system later grows into multiple services, a mobile app, or external API access, the team can introduce JWT authentication later. The change would require updating login, request validation, logout behavior, and client storage, but it would not require rewriting the entire booking system.

---

# Trade-off Card

| Criterion | Option A Score: Server-side sessions | Option B Score: Stateless JWT | Why this criterion mattered here |
|---|---:|---:|---|
| Security and revocation | 5 | 3 | The system must be able to quickly stop access if a user logs out, loses permission, or performs suspicious activity. |
| Complexity | 5 | 3 | A student project team needs an authentication approach that is easy to implement, test, and explain. |
| Maintainability | 4 | 3 | Future teammates should understand where login state is stored and how protected requests are checked. |
| Scalability | 3 | 5 | JWTs scale more easily across many services, but this booking system is currently a simple web application. |
| Fit for browser-based system | 5 | 3 | A cookie-based server session fits the current shape of the Campus Lab Booking system better than API-first token authentication. |
| Reversibility | 4 | 4 | Both options can be changed later, but switching authentication still requires careful updates to login, logout, and request validation. |

---

# Personal ADR Approach for Future Decisions

When I write an ADR for any future technical decision, I will use this process:

1. **Name the decision clearly.**  
   If I cannot write the decision in one sentence, I probably do not understand it yet.

2. **Write the context before choosing.**  
   I should explain why this decision matters for the system, not just which option I like.

3. **Compare at least two real options.**  
   A good ADR should show that the rejected option was seriously considered.

4. **Use criteria that actually matter.**  
   I should not only list generic criteria. I should ask what matters here: security, cost, team skill, complexity, speed, maintainability, or reversibility.

5. **Write both positive and negative consequences.**  
   Every architecture decision has a cost. If I only write benefits, the ADR is one-sided.

6. **Add a reversibility note.**  
   I should say whether this is a one-way door or two-way door decision, and what would make the team change it later.

7. **Make it useful for someone absent.**  
   A teammate reading the ADR six months later should understand what was decided, why it was decided, and what trade-off was accepted.

---

# Quality Self-Check

- ADR contains context, decision, and consequences: **Yes**
- At least two pros and two cons for each option: **Yes**
- Trade-off Card has four columns and at least three rows: **Yes**
- The deciding criteria are included in the context: **Yes**
- Reversibility note included: **Yes**
- Format can be reused for another technical decision: **Yes**
