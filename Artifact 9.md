# Assignment S9 – QA and CI Triage Decision Tree

## 1. Understanding the Assignment

This assignment is asking me to practice how to react when a CI pipeline, automated test, or code review finds a problem. Instead of randomly changing code until the build turns green, I need to follow a triage process.

Triage means deciding:

- what actually failed,
- what is only a symptom,
- what the root cause might be,
- how serious the issue is,
- how urgently it should be fixed,
- and what concrete action should happen next.

The final deliverable is a personal decision tree that I can reuse later when something breaks in CI or during review.

---

## 2. Scenario Analysis

### What failed?

The failing test is:

```text
tests/test_booking_confirmation.py::test_confirmation_message_contains_slot_id
```

The test expected the notification body to contain:

```text
slot=lab3-bench2-14:00
```

But the actual notification body was:

```text
Booking confirmed for lab3 bench2 at 14:00
```

So the test failed because the booking confirmation message format changed.

---

### Symptom vs. Root Cause

#### Symptom

The visible symptom is that one test failed because the expected text no longer appears in the notification body.

#### Likely root cause

The root cause is probably the change to the shared notification format helper. The format helper is used by multiple notification types, not only booking confirmations.

Because password reset emails, calendar invites, and weekly summary digests also use the same helper, the problem may be wider than one test.

---

### Missing information

Before fixing anything, I would need to know:

- Was the new readable message format intentional and approved?
- Is the slot ID still required somewhere for users, logs, integrations, or downstream systems?
- Do other notification tests fail if the full test suite runs?
- Did the CODEOWNER for `apps/notifications/*` review the helper change?
- Are production notifications currently missing important machine-readable data?
- Should the test be updated, or should the code preserve the old `slot=` format somewhere?

---

## 3. Triage Failures in the First-Draft Transcript

| Step | Failure Type | Why it is a problem | Better defensive move |
|---|---|---|---|
| Step 6: “Edit the assertion to match. Push.” | Chasing the symptom | The teammate changed the test only because the output changed, without checking whether the new output is correct or whether important information was lost. | Trace the failing assertion back to the notification format helper and confirm the expected behavior before editing the test. |
| Step 7: “Build goes green. Close the issue.” | Deploy-and-pray | A green build does not prove the system is safe. The change is already in production and may have affected other notification types. | Run the full notification test suite, check related production behavior, and verify other shared-helper users. |
| Step 6: “Edit the assertion to match. Push.” | Fixing in isolation | The fix only touches one booking confirmation test, even though the shared helper affects password resets, calendar invites, and weekly digests. | Search for all users of the shared helper, update or add tests for each affected notification type, and request review from `@notifications-team`. |

---

## 4. Severity and Priority Assessment

### Issue 1: Booking confirmation test failure

- **Severity:** Medium
- **Priority:** High

The system may still send a readable confirmation, but the failure shows that expected behavior changed. Because it fails on every push, it blocks CI and should be handled quickly.

---

### Issue 2: Shared notification helper changed

- **Severity:** High
- **Priority:** High

The helper is shared by booking confirmations, password resets, calendar invites, and weekly summary digests. A change here can break several user-facing workflows at once.

---

### Issue 3: Change already in production

- **Severity:** High
- **Priority:** Very High

The issue is more urgent because the changed behavior is already live. Even if users have not complained yet, important notification content may already be wrong or incomplete.

---

### Issue 4: Missing CODEOWNER review

- **Severity:** Medium
- **Priority:** High

The CODEOWNERS file says notification changes belong to `@notifications-team`. If they did not review the change, the workflow failed and similar mistakes could happen again.

---

## 5. Personal QA and CI Triage Decision Tree

## Entry Point 1: Build Broke

1. **Build broke in CI.**
   1.1. **Did the build fail before tests started?**
   - Yes → Check dependency install, environment setup, missing secrets, and workflow syntax.
     - If dependencies failed → Compare `requirements.txt` or lockfile changes, then re-run install locally.
     - If workflow syntax failed → Open `.github/workflows/ci.yml`, validate the YAML, and fix the workflow file.
   - No → Go to 1.2.

   1.2. **Did the build fail during test execution?**
   - Yes → Open the first failing test, read the assertion and actual output, then go to Entry Point 2.
   - No → Check packaging, linting, type checking, or deployment steps depending on the failed CI stage.

   1.3. **Is the failure related to the current change?**
   - Yes → Trace the failure to the changed files and inspect the nearest shared component.
   - No → Check recent merged commits, dependency updates, and CI image changes.
     - If an upstream commit caused it → Revert or patch the upstream commit.
     - If the CI image changed → Pin the environment version and re-run.

---

## Entry Point 2: Test Broke

2. **A test failed.**
   2.1. **Does the test fail every time?**
   - Yes → Treat it as a real failure and continue to 2.2.
   - No → Treat it as possible flakiness.
     - Re-run the test multiple times.
     - Check timing, randomness, external services, and test order dependence.
     - If flaky → Quarantine the flaky test, open a fix task, and add a stable replacement test if needed.

   2.2. **Is the assertion checking required behavior or old behavior?**
   - Required behavior → Fix the production code, then re-run the failing test.
   - Old behavior → Update the test only after confirming the new expected behavior with the requirement, spec, or owner.

   2.3. **Is the failing code shared by other features?**
   - Yes → Search for all callers of the shared code.
     - Run related tests.
     - Add missing tests for affected features.
     - Ask the CODEOWNER to review the shared behavior.
   - No → Fix the local bug, add or update the focused test, and re-run the relevant suite.

   2.4. **Is the change already in production?**
   - Yes → Check production impact, logs, and user-visible workflows.
     - If users are affected → Roll back or hotfix.
     - If no visible impact yet → Still add tests and verify affected paths before closing.
   - No → Fix before merge and block the PR until CI is green.

---

## Entry Point 3: Review Caught Something

3. **Code review caught a problem.**
   3.1. **Is the review comment about correctness, security, or data loss?**
   - Yes → Treat it as high priority.
     - Reproduce the issue.
     - Add a test that would fail before the fix.
     - Fix the code.
     - Re-run the related tests.
   - No → Go to 3.2.

   3.2. **Is the review comment about maintainability, readability, or style?**
   - Yes → Check whether the issue affects future changes or only appearance.
     - If it affects future maintenance → Refactor before merging.
     - If it is cosmetic → Fix if quick, otherwise document and prioritize lower.
   - No → Go to 3.3.

   3.3. **Does the review involve a file with a CODEOWNER?**
   - Yes → Make sure the CODEOWNER reviewed and approved the change.
     - If approval is missing → Request CODEOWNER review before merging.
   - No → Make sure at least one relevant teammate reviewed the risky area.

---

## Entry Point 4: Production Behavior Looks Wrong

4. **A bug is noticed in production.**
   4.1. **Is the bug blocking core user workflows?**
   - Yes → Mark priority as urgent.
     - Reproduce the issue.
     - Identify the last related deployment.
     - Roll back or hotfix.
   - No → Continue to 4.2.

   4.2. **Is the bug caused by a shared component?**
   - Yes → Search for all affected features and run their tests.
   - No → Fix the local feature and add a regression test.

   4.3. **Was there a missing test or review step?**
   - Yes → Add the missing test or review rule so the same issue is caught earlier next time.
   - No → Document the investigation and close only after verification.

---

## 6. My General Triage Rules

1. Do not fix the first visible symptom without checking the cause.
2. Do not make tests match new output unless the new output is confirmed to be correct.
3. If a helper or shared component changed, check every feature that uses it.
4. A green build is not always enough; verify the risky behavior directly.
5. Severity means how bad the bug is. Priority means how soon it should be fixed.
6. If the issue is already in production, raise the priority.
7. If CODEOWNERS exists, use it before merging risky changes.
8. Every fix should end with a concrete verification step: re-run the failing test, run related tests, or check production behavior.

---

## 7. Final Concrete Move for This Scenario

For this specific CI failure, I would not simply edit the test assertion. I would first inspect the shared notification format helper, confirm whether the readable format is the new required behavior, check all notification types that use the helper, run the full notification test suite, and request review from `@notifications-team`. If the new format is correct, I would update the booking confirmation test and add tests for password reset emails, calendar invites, and weekly summary digests. If the old `slot=` value is still required, I would fix the notification code to preserve it.
