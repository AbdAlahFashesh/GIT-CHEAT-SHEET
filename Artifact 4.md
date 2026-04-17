# User Story Sheet (S4)

A personal reference to avoid weak user stories.  
Built around the **3 main failure modes**.

---

# 1. Vague Role

##  Problem
Stories like:
> “As a user, I want…”

→ No clear stakeholder  
→ No clear value  

---

##  What to watch for
- Generic roles (“user”, “person”, “someone”)
- No clear ownership
- No clear benefit

---

##  Questions to ask
- Who actually uses this feature?
- Who cares if this fails?
- What does this person gain?

---

##  How to fix
Replace vague roles with real stakeholders:
- Student
- Lab assistant
- Admin
- IT staff

---

##  Example

 Bad:
> As a user, I want to book a lab

 Good:
> As a chemistry student, I want to book a lab bench so that I can complete my practical course

---

##  Mistake I almost made
Used “user” instead of “student” because it felt easier.

---

#  2. Untestable Criteria

##  Problem
Criteria like:
- “fast”
- “user-friendly”
- “works well”

→ Cannot test → cannot say DONE

---

##  What to watch for
- No numbers
- No clear outcome
- No pass/fail condition

---

##  Questions to ask
- How fast is “fast”?
- What counts as success?
- What happens in edge cases?

---

## 🛠 How to fix
Make criteria measurable and testable.

---

## Example

 Bad:
> The system should be fast

 Good:
> The booking page loads in under 2 seconds

---

##  Good format (Given / When / Then)

```text
Given a student is logged in
When they book a lab bench
Then the booking is confirmed within 2 seconds
```

---

## Mistake I almost made
Wrote “easy to use” without defining what that means.

---

# 3. Too-Big Scope

##  Problem
Story is too large (epic disguised as a story)

→ Cannot finish in one sprint  
→ Cannot estimate  

---

##  What to watch for
- Multiple features in one story
- “and” used too often
- Covers entire system

---

##  Questions to ask
- Can this be done in a few days?
- Can I test it independently?
- Can I split it?

---

##  How to fix
Break into smaller stories.

---

##  Example

 Bad:
> As a student, I want to manage my bookings

Better:
> As a student, I want to cancel a booking so that I can free my slot
---

##  Mistake I almost made
Tried to include booking + cancelling + viewing in one story.

---

#  Example User Stories

##  Story 1

**Story:**
> As a chemistry student, I want to book a lab bench so that I can attend my practical session

### Acceptance Criteria

```text
Given a student is logged in
When they select a lab, bench, and time slot
Then the system confirms the booking

Given a bench is already booked
When another student tries to book it
Then the system prevents double booking

Given a booking is successful
Then it appears in the student’s schedule
```

---

##  Story 2

**Story:**
> As a lab assistant, I want to see the daily schedule so that I can prepare the required materials

### Acceptance Criteria

```text
Given a lab assistant is logged in
When they open the schedule
Then they see all bookings for the day

Given a booking exists
Then the required experiment details are visible

Given no bookings exist
Then the schedule shows empty slots
```

---

#  Quick Checklist (Before Writing Any Story)

- [ ] Is the role specific?
- [ ] Is the value clear?
- [ ] Can I test it?
- [ ] Is it small enough?
- [ ] Does it avoid vague words?

---

#  Core Thinking Model

- **F (Functional)** → What it does  
- **NFR (Non-Functional)** → How well it works  
- **C (Constraints)** → What limits it  

---

#  Common Red Flags

- “fast”
- “simple”
- “easy”
- “user-friendly”

 Always ask: *“What does that mean in numbers?”*

---

#  Common Conflict Patterns

- Offline vs consistency
- Speed vs reliability
- Simplicity vs advanced features

---
