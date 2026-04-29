# Assignment S5 – Feature Specification Cheat Sheet

## Feature Specification Example: Campus Lab Booking

### Chosen Feature
**Book a computer lab seat for a selected time slot.**

This feature allows a student to reserve an available lab seat for a specific date and time, as long as the booking follows the system rules.

---

## 1. Feature Goal

The goal of this feature is to let students reserve a lab seat in advance so they can use the campus computer lab without conflicts, double bookings, or unclear availability.

---

## 2. Actors

| Actor | Role |
|---|---|
| Student | Searches for available lab slots and creates a booking |
| Lab Booking System | Checks availability, validates rules, and confirms or rejects the booking |
| Admin / Lab Staff | Manages lab rooms, opening hours, and booking rules |

---

## 3. Inputs

The feature needs the following inputs:

| Input | Description |
|---|---|
| Student ID | Identifies the student making the booking |
| Lab room | The selected lab room |
| Date | The day the student wants to book |
| Start time | When the booking should begin |
| End time | When the booking should finish |
| Seat / workstation | The specific seat or computer being reserved |

---

## 4. Outputs

The system should produce one of these outputs:

| Output | Description |
|---|---|
| Booking confirmation | The booking is successful and the student receives the booking details |
| Error message | The booking is rejected because something is invalid |
| Updated availability | The booked slot is no longer shown as available |

---

## 5. Normal-Case Flow

1. The student opens the Campus Lab Booking system.
2. The student selects a lab room.
3. The student chooses a date and time slot.
4. The system displays available seats for that time.
5. The student selects an available seat.
6. The student confirms the booking.
7. The system checks that:
   - the student is allowed to book,
   - the seat is still available,
   - the time is within lab opening hours,
   - the student has not exceeded booking limits.
8. The system saves the booking.
9. The system shows a confirmation message.
10. The student can see the booking in their booking list.

---

## 6. Error Cases

| Error Case | System Response |
|---|---|
| Seat is already booked | Show message: “This seat is no longer available.” |
| Student selects a time outside opening hours | Show message: “Selected time is outside lab opening hours.” |
| Student already has a booking at the same time | Show message: “You already have a booking during this time.” |
| Student exceeds maximum booking limit | Show message: “You have reached the maximum number of bookings.” |
| Required input is missing | Ask the student to complete the missing field |
| System/database error | Show a clear failure message and do not create the booking |

---

## 7. Edge Cases

| Edge Case | Expected Behavior |
|---|---|
| Two students try to book the same seat at the same time | Only the first confirmed booking should succeed |
| Student books the last available seat | The slot should become unavailable after confirmation |
| Booking starts exactly when lab opens | Booking should be allowed |
| Booking ends exactly when lab closes | Booking should be allowed |
| Student refreshes the page after booking | The booking should not be duplicated |
| Student loses internet during confirmation | The system should not create duplicate or unclear bookings |
| Admin changes lab opening hours after a booking exists | Existing bookings should either stay valid or be flagged according to a clear rule |

---

## 8. Rules and Constraints

| Rule | Description |
|---|---|
| One seat cannot be booked by two students at the same time | Prevents double booking |
| Booking must be inside lab opening hours | Students cannot book closed times |
| A student cannot have overlapping bookings | Prevents unfair or impossible reservations |
| Booking duration must follow the allowed time limit | Example: minimum 30 minutes, maximum 2 hours |
| Booking must be connected to a valid student account | Anonymous bookings are not allowed |
| Confirmation should only appear after the booking is saved | Avoids false confirmations |

---

## 9. Gap Hunt

### Missing Case Found
I almost missed the case where **two students try to book the same seat at the same time**. This is important because the seat may appear available to both students before either confirms. The system must check availability again at the moment of confirmation.

### Unclear Rule Found
The booking limit rule was unclear. For example:

- How many bookings can one student have per day?
- Is the limit based on number of bookings or total hours?
- Can students book multiple different labs on the same day?

This rule needs to be clearly defined before the feature can be fully tested.

---

# Personal Feature Specification Cheat Sheet

## Purpose

This cheat sheet helps me describe any software feature clearly enough that a developer can build it and a tester can test it.

A good feature specification should explain:

- what the feature does,
- who uses it,
- what information it needs,
- what it produces,
- what happens normally,
- what can go wrong,
- what unusual cases must still work,
- what rules control the behavior.

---

## Step 1: Define the Feature Clearly

Before writing details, I should answer:

- What is the feature called?
- Who uses it?
- What problem does it solve?
- What is the final successful result?

**Simple format:**

> This feature allows [actor] to [action] so that [goal/value].

Example:

> This feature allows students to book a lab seat so that they can reserve computer access before arriving.

---

## Step 2: Identify the Actors

Ask:

- Who starts the action?
- Who receives the result?
- Does an admin, system, or external service take part?
- Who can approve, reject, change, or cancel the action?

Actors are not always only people. The system, database, payment service, email service, or admin panel can also be part of the feature.

---

## Step 3: List Inputs

For every feature, ask:

- What information does the user enter?
- What does the system already know?
- What fields are required?
- What fields are optional?
- What format should the input have?

Useful input questions:

- Can this field be empty?
- Can the value be too long, too short, or invalid?
- Does the value need to be unique?
- Does the value depend on another field?

---

## Step 4: List Outputs

Ask:

- What should the user see after success?
- What should the system save or update?
- What message appears after failure?
- Does the feature send an email, notification, receipt, or confirmation?

A feature is not complete unless the result is clear.

---

## Step 5: Write the Normal-Case Flow

Describe the happy path step by step.

A good normal flow should show:

1. how the user starts,
2. what the user selects or enters,
3. what the system checks,
4. what the system saves or changes,
5. what confirmation the user receives.

Keep the steps short and ordered.

---

## Step 6: Add Error Cases

Error cases are situations where the feature cannot continue normally.

Ask:

- What if required information is missing?
- What if the user enters invalid data?
- What if the user is not allowed to do this?
- What if the selected item is no longer available?
- What if the database or network fails?
- What message should the user see?

Every error case should have a system response.

Bad example:

> Invalid input.

Better example:

> If the student selects a time outside lab opening hours, the system rejects the booking and shows: “Selected time is outside lab opening hours.”

---

## Step 7: Search for Edge Cases

Edge cases are unusual situations that may still happen.

Useful edge-case questions:

- What happens at the exact boundary?
- What happens if two users do the same thing at the same time?
- What happens if the user refreshes, goes back, or clicks twice?
- What happens if the last available item is selected?
- What happens if something changes while the user is using the feature?
- What happens if the action is interrupted?

Common edge case patterns:

| Pattern | Example Question |
|---|---|
| Boundary | What if the booking starts exactly at opening time? |
| Duplicate action | What if the user clicks confirm twice? |
| Concurrency | What if two users book the same resource? |
| Last item | What if this is the final available slot? |
| Interrupted action | What if the internet disconnects during confirmation? |
| Changed data | What if an admin changes the rule after the user starts? |

---

## Step 8: Define Rules and Constraints

Rules explain what is allowed and what is not allowed.

Ask:

- Who is allowed to use this feature?
- Are there limits?
- Are there time, quantity, size, or permission constraints?
- What must always be true?
- What should never be allowed?

Rules should be testable.

Bad rule:

> Students can book fairly.

Better rule:

> A student can have a maximum of two active bookings per day.

---

## Step 9: Hunt for Gaps

After writing the specification, re-read it and ask:

- What did I assume but not write down?
- Which rule is still vague?
- Which error case is missing?
- What would a tester ask me?
- What would a developer misunderstand?
- What could cause a bug if ignored?

I should always write down at least:

1. one missing case,
2. one unclear rule.

This makes the specification stronger.

---

## Step 10: Turn the Specification Into Test Ideas

Since specifications are used later for testing, I should check if each behavior can become a test case.

For each rule or flow, ask:

- Can I test this?
- What input would I use?
- What result should I expect?
- What would count as pass or fail?

If I cannot test it, the specification is probably too vague.

---

# Quick Feature Specification Template

## Feature Name

[Write the name of the feature]

## Feature Goal

This feature allows [actor] to [action] so that [goal/value].

## Actors

- [Main actor]
- [System/admin/other actor]

## Inputs

- [Input 1]
- [Input 2]
- [Input 3]

## Outputs

- [Success output]
- [Failure output]
- [System update]

## Normal Flow

1. [Step 1]
2. [Step 2]
3. [Step 3]
4. [Step 4]
5. [Success result]

## Error Cases

| Error | Expected System Response |
|---|---|
| [Error 1] | [Response 1] |
| [Error 2] | [Response 2] |

## Edge Cases

| Edge Case | Expected Behavior |
|---|---|
| [Edge case 1] | [Expected behavior 1] |
| [Edge case 2] | [Expected behavior 2] |

## Rules and Constraints

- [Rule 1]
- [Rule 2]
- [Rule 3]

## Gap Hunt

### Missing Case

[Write one missing case you found]

### Unclear Rule

[Write one unclear rule you found]

---

# Final Self-Check Before Uploading

Before submitting, I should check:

- Is this cheat sheet useful for another feature, not only Campus Lab Booking?
- Did I include a real feature specification example?
- Did I include inputs, outputs, normal flow, errors, edge cases, and rules?
- Did I find at least one missing case?
- Did I find at least one unclear rule?
- Can this specification help me create test cases later in S8?
- Would I open this cheat sheet again before specifying a new feature?

---

# Git / Upload Reminder

Upload one of the following:

- Markdown file in `artifacts/s5/` and paste the Git URL in Google Classroom, or
- Direct document upload to Google Classroom.

Suggested file path:

```text
artifacts/s5/S5_Feature_Specification_Cheat_Sheet.md
```

