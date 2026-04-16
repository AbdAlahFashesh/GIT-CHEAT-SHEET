# Campus Lab Booking – Requirements Analysis Cheat Sheet

---

## Stakeholders

### Main
- Students
- Lab assistants
- Dr. Miriam Kasper (requester)
- Teaching staff
- University IT department

### Hidden Stakeholder
- Health & Safety / Compliance officers

### Actor vs Non-Actor
**Actors:**
- Students
- Lab assistants
- Staff

**Non-Actors:**
- IT department
- Legal / GDPR
- Safety regulators

---

## F / NFR / C Classification

### Functional Requirements (F)
- Students can book a lab bench
- Students can cancel bookings
- Staff can view schedules
- Prevent double-bookings
- Show cleaning status
- Block contaminated benches
- Show required materials to assistants

---

### Non-Functional Requirements (NFR)
- Mobile-friendly
- Fast (ambiguous)
- Always available during teaching weeks
- GDPR compliant
- Easy to use
- Works offline

---

### Constraints (C)
- Must run on university Kubernetes cluster
- No external SaaS
- Data must stay in EU
- Open source only
- German + English support
- Deadline: 3rd week of summer term

---

##  Four-Colour Markup

###  Ambiguity
**“Fast”**
- What response time is considered fast?

---

### Hidden Assumption
**“Works offline”**
- Assumes syncing later
-  What functionality must work offline?

---

###  Contradiction
~~Works offline~~ vs ~~Never lose bookings~~

-  What happens if conflicts occur during sync?

---

## Trouble Spots

- Offline + booking → conflicts
- Easy to use vs power-user features
- Always available → high reliability needed
- Zero budget + constraints → technical pressure

---

## 🧾 Personal Cheat Sheet (Reusable)

### Questions to Ask
- What does “fast” mean?
- What does “user-friendly” mean?
- What happens on failure?
- What happens on conflicts?
- What must NEVER happen?
- What consequences should be put? 

---

### Red Flags
- “fast”
- “simple”
- “easy”
- “user-friendly”

---

### Hidden Assumptions
- Offline implies sync
- Integration needs clarification
- No training → UX must be simple

---

### Conflict Patterns
- Offline vs consistency
- Speed vs reliability
- Simplicity vs complexity

---

