# Assignment S6 – System Decomposition Guide

## Campus Lab Booking System

**Objective:** Break the Campus Lab Booking system into understandable components, trace one feature through them, and create a reusable guide for decomposing future systems.

---

# Part 1 – Component Sketch

## System Overview

The Campus Lab Booking system allows students to find available lab slots, book them, and receive confirmation. Admins or lab staff can manage labs, time slots, and booking rules.

---

## Component Decomposition

| Component | Responsibility | Talks To |
|---|---|---|
| **Student Interface** | Provides the screen where students search for labs, view slots, and submit bookings. | Booking Controller, Authentication Component |
| **Admin Interface** | Provides the screen where staff manage labs, schedules, and booking rules. | Lab Management Component, Slot Management Component, Rule Management Component |
| **Authentication Component** | Checks whether the user is logged in and identifies their role as student, admin, or staff. | User Database, Student Interface, Admin Interface |
| **Booking Controller** | Receives booking requests and coordinates the booking process. | Student Interface, Availability Component, Rule Checker, Booking Storage, Notification Component |
| **Availability Component** | Checks which lab slots are free, full, blocked, or already booked. | Slot Database, Booking Storage |
| **Rule Checker** | Verifies that the booking follows system rules, such as one booking per slot and valid time limits. | Booking Controller, Booking Storage, Rule Management Component |
| **Booking Storage** | Saves, updates, and retrieves booking records. | Booking Controller, Availability Component, Rule Checker, Booking Database |
| **Lab Management Component** | Stores and updates information about labs, rooms, capacity, and equipment. | Admin Interface, Lab Database, Slot Management Component |
| **Slot Management Component** | Creates and updates bookable time slots for each lab. | Admin Interface, Lab Management Component, Slot Database |
| **Notification Component** | Sends confirmation, rejection, or cancellation messages to users. | Booking Controller, Email/Message Service |
| **Databases** | Store users, labs, slots, rules, and bookings. | Authentication Component, Booking Storage, Availability Component, Lab Management Component, Slot Management Component |

---

## Simple Component Sketch

```text
Student
   |
   v
Student Interface
   |
   v
Authentication Component
   |
   v
Booking Controller
   |--------------------|
   v                    v
Availability Component  Rule Checker
   |                    |
   v                    v
Slot Database       Booking Storage
                         |
                         v
                  Booking Database
                         |
                         v
               Notification Component
                         |
                         v
                 Email/Message Service
```

Admin-side management:

```text
Admin / Lab Staff
      |
      v
Admin Interface
      |
      |-------------------------|
      v                         v
Lab Management Component   Slot Management Component
      |                         |
      v                         v
Lab Database              Slot Database
      |
      v
Rule Management Component
```

---

# Part 2 – Feature Trace

## Feature Chosen: Student Books a Lab Slot

### User Action

A student selects an available lab slot and clicks **Book Slot**.

---

## End-to-End Trace

| Step | Component | What Happens |
|---|---|---|
| 1 | **Student Interface** | The student chooses a lab, date, and time slot, then submits the booking request. |
| 2 | **Authentication Component** | The system checks that the student is logged in and has permission to book. |
| 3 | **Booking Controller** | The request enters the main booking workflow. |
| 4 | **Availability Component** | The system checks whether the selected slot still exists and is still available. |
| 5 | **Rule Checker** | The system checks rules, such as whether the student already has a booking at that time or whether the slot is blocked. |
| 6 | **Booking Storage** | If all checks pass, the booking is saved in the booking database. |
| 7 | **Availability Component** | The slot availability is updated so it is no longer shown as fully open. |
| 8 | **Notification Component** | The system sends a confirmation message to the student. |
| 9 | **Student Interface** | The student sees a success message with the booking details. |

---

## Trace Summary

```text
Student clicks Book Slot
        |
        v
Student Interface
        |
        v
Authentication Component
        |
        v
Booking Controller
        |
        v
Availability Component
        |
        v
Rule Checker
        |
        v
Booking Storage
        |
        v
Booking Database
        |
        v
Notification Component
        |
        v
Student receives confirmation
```

---

## Possible Error Cases in the Trace

| Error Case | Where It Is Caught | System Response |
|---|---|---|
| Student is not logged in | Authentication Component | Ask student to log in before booking. |
| Slot was already booked by someone else | Availability Component | Show message: “This slot is no longer available.” |
| Student already has a booking at the same time | Rule Checker | Reject request and explain the conflict. |
| Slot is blocked by admin | Availability Component / Rule Checker | Reject request and hide or mark the slot as unavailable. |
| Database save fails | Booking Storage | Show error message and do not send confirmation. |
| Confirmation message fails | Notification Component | Keep the booking saved, but show a warning that confirmation could not be sent. |

---

# Part 3 – Smell Check

## Responsibility Smells

| Smell | Issue Found | Fix |
|---|---|---|
| **Mixed responsibility** | Booking Controller could become too large if it handles rules, database saving, and notifications itself. | Keep it as a coordinator and move checks into Rule Checker, storage into Booking Storage, and messages into Notification Component. |
| **Hidden coupling** | Availability depends on booking data, but this connection may be forgotten in a simple diagram. | Show clearly that Availability Component talks to Booking Storage or Booking Database. |
| **Orphaned responsibility** | Notifications are easy to forget because they happen after the booking is saved. | Add Notification Component as its own component. |
| **Unclear ownership** | Booking rules may be mixed into the user interface or database queries. | Create a Rule Checker so booking logic has one clear place. |
| **Admin gap** | Student booking depends on slots existing, but the admin side creates those slots. | Add Admin Interface, Lab Management, and Slot Management components. |

---

# Part 4 – Personal System Decomposition Guide

## Reusable Questions for Decomposing Any System

When I decompose a system, I first ask: **who uses it, and what actions do they perform?**  
Then I look for the main entry points, such as screens, APIs, forms, or commands.  
After that, I separate components by responsibility: interface, coordination, rules, storage, and external communication.  
I check whether each component has one clear job and whether its name explains what it owns.  
I trace one important user action from start to finish to see if any component is missing.  
I look for hidden coupling, especially when one component secretly depends on another component’s data.  
I also check for orphaned responsibilities, such as notifications, logging, permissions, or error handling.  
The hardest parts to place are usually rules, because they can accidentally spread across the interface, controller, and database.  
A good decomposition should make the normal flow clear, but also show where errors are caught.  
If I can explain the system to another person using the sketch, then the decomposition is probably useful.

---

# Final Self-Check

| Question | Answer |
|---|---|
| Did I run the trace through the sketch end-to-end? | Yes |
| Did the trace get stuck? | No |
| Did each smell pass surface at least one issue? | Yes |
| Did I include mixed responsibility, hidden coupling, and orphaned responsibility? | Yes |
| Did I write the required 5–10 line annotation? | Yes |
| Is the annotation general enough for another system? | Yes |
| Can this guide help me in S7 for an Architecture Decision Record? | Yes |

---

