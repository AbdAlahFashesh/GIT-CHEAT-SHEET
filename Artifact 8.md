# S8 – Test Thinking Reference

## Feature chosen to test

**Feature:** Student books a Campus Lab slot.

A student selects an available lab, date, time slot, and confirms the booking.  
The system should only create the booking if the student is authenticated, the slot exists, the slot is still available, and the booking rules are satisfied.

---

## Test cases derived from the feature

| # | Test type | What I am testing | Input / setup | Expected result |
|---|---|---|---|---|
| 1 | Normal | Student successfully books an available slot | Authenticated student selects an available lab slot and confirms | Booking is created, slot becomes unavailable, and confirmation is shown |
| 2 | Invalid | Unauthenticated user tries to book | User is not logged in and tries to confirm a slot | Booking is rejected and user is asked to log in |
| 3 | Invalid | Student tries to book an already-booked slot | Slot was available on screen but another student booked it first | System rejects the booking and shows that the slot is no longer available |
| 4 | Edge | Student books the last available slot in a lab | Only one slot remains available | Booking succeeds and the lab/time now shows no remaining availability |
| 5 | Invalid | Missing required booking data | Student confirms without selecting a lab, date, or time | Booking is not created and the missing field is shown |
| 6 | Edge | Booking at the boundary of allowed time | Student books the earliest or latest allowed slot | Booking is accepted only if it is inside the allowed time range |
| 7 | Invalid | Student tries to book outside allowed rules | Student tries to book a slot in the past or outside lab opening hours | Booking is rejected with a clear message |
| 8 | Invalid | Student tries to exceed booking limit | Student already reached the maximum number of active bookings | Booking is rejected and the limit is explained |

---

## Most likely test to catch a real bug

**Test #3 – Already-booked slot / race condition**

This is the most likely to catch a real bug because two students may see the same slot as available at the same time.  
If the system only checks availability when displaying the slot, but not again when confirming, it could accidentally double-book the same lab slot.

---

## Test idea from S7 ADR consequences

If the architecture uses an external authentication service or Campus IdP, then one consequence is that booking depends on successful authentication.

**Test idea:** If the Campus IdP/AuthService is unavailable, the system should not create a booking. It should fail safely and show a clear message instead of crashing or allowing unauthenticated booking.

---

# My Personal Test Thinking Reference

## 1. Start from the feature’s promise

Before writing tests, I ask:

- What is the user trying to do?
- What should the system allow?
- What should the system reject?
- What state should change after success?
- What should stay unchanged after failure?

For this feature, the promise is:  
**A valid student can book an available lab slot, and invalid bookings must be prevented.**

---

## 2. Derive tests from three main categories

### A. Normal cases

These test the expected successful path.

Questions I ask:

- What does success look like?
- What is the most common user flow?
- What confirmation or output should appear?
- What data should be saved or updated?

Example:  
An authenticated student books an available slot and receives confirmation.

---

### B. Edge cases

These test boundaries and special limits.

Questions I ask:

- What happens at the first or last allowed value?
- What happens when only one item remains?
- What happens at maximum capacity?
- What happens near deadlines or closing times?

Example:  
A student books the last available slot.

---

### C. Invalid cases

These test things the system should reject.

Questions I ask:

- What if required data is missing?
- What if the user is not allowed?
- What if the input is wrong or outdated?
- What if the action breaks a rule?

Example:  
A student tries to book a slot that is already taken.

---

## 3. Separate spec-based expectations from implementation results

A good test should describe what the specification says should happen, not what the current implementation happens to do.

If the spec does not say what should happen, I should not invent the answer.  
Instead, I mark it as a **question for the feature specification**.

Example:

- Assertion: “Unauthenticated users cannot book.”
- Spec question: “What exact message should appear when authentication fails?”

---

## 4. Think about state changes

For every feature, I check what changes before and after the action.

Questions I ask:

- What existed before the action?
- What should exist after success?
- What should not change after failure?
- Could two users change the same data at the same time?

For the booking feature, the important state change is:  
**available slot → booked slot**

---

## 5. Think about permissions

Many bugs happen because the system allows the wrong person to do something.

Questions I ask:

- Who is allowed to use this feature?
- Who is not allowed?
- Does the system check permissions before changing data?
- What happens if the user is logged out?

Example:  
A student must be authenticated before booking.

---

## 6. Think about timing and concurrency

Some features fail when multiple users act at the same time.

Questions I ask:

- What if two users try the same action together?
- What if data changes between viewing and confirming?
- What if an external service is down?
- What if the user acts too late?

Example:  
Two students try to book the same slot at the same time.

---

## 7. Use ADR consequences as test sources

Architecture decisions often create risks.  
Risks can become tests.

Examples:

- If the system depends on AuthService, test AuthService failure.
- If JWT/session logic is used, test expired login.
- If booking uses a database transaction, test duplicate booking prevention.
- If a component is replaceable, test that its contract stays the same.

---

## 8. My quick testing checklist for any future feature

When I need to test any feature, I will ask:

1. What is the happy path?
2. What required input could be missing?
3. What wrong input could be entered?
4. What is the minimum allowed value?
5. What is the maximum allowed value?
6. What permissions are needed?
7. What state changes after success?
8. What should remain unchanged after failure?
9. What could happen at the same time from another user?
10. What external service or dependency could fail?

---

## 9. What I almost missed

I almost focused only on simple form validation, like missing fields or invalid inputs.

The more important risk is **double booking**, because it affects the main purpose of the system.  
This taught me that strong tests should not only check the form, but also check the system rules and data consistency.

---

## 10. My reusable testing process

For future features, I will follow this process:

1. Read the feature and identify the main promise.
2. Write one normal test for the happy path.
3. Write edge tests for limits, boundaries, and “last available” situations.
4. Write invalid tests for missing data, wrong users, and broken rules.
5. Check what data should change or not change.
6. Check permission and authentication behavior.
7. Look at ADR consequences and turn risks into tests.
8. Mark unclear behavior as a question for the specification.
9. Identify the test most likely to catch a real bug.
10. Keep the reference general enough to reuse on another feature.

---

## Summary

Testing is not only about checking whether the program runs.  
It is about asking what the feature promises, what can go wrong, and what the system must protect.

For the Campus Lab Booking system, the most important tests are not only about the form.  
They are about preventing invalid bookings, protecting availability, handling authentication, and avoiding double booking.
