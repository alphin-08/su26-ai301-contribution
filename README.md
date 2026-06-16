# Contribution [1]: Replace Flowsheet2Action direct-response return nulls with NONE

**Contribution Number:** 1  
**Student:** Alphin Shajan  
**Issue:** https://github.com/carlos-emr/carlos/issues/2885 
**Status:** Phase I

---

## Why I Chose This Issue

I picked this issue because I get to work on the real backend code of a medical records system. My job is to check a specific Java file and make sure certain methods return the right value instead of returning null. This fits my skills perfectly since I already know Java. I can practice reading the code to find out which parts send data directly to the user. I also need to write some tests to prove my code changes actually work. This builds right on top of the testing skills I learned during my senior capstone project. Fixing this issue will help me prepare for my new engineering job. It will show me exactly how big teams keep their code organized and secure.

---

## Understanding the Issue

### Problem Description

The Flowsheet2Action class had methods that write JSON directly to the HTTP response and then ended with return null. The project guidelines in CLAUDE.md say that any action which writes directly to the response should end with return NONE instead. Using return null is not reliable enough to stop Struts from trying to resolve a result after the response is already written, which could cause an HTML error page to get mixed into a response that is supposed to be pure JSON.

### Expected Behavior

Every method in Flowsheet2Action that writes JSON directly to the HTTP response should end with return NONE to explicitly tell Struts that the response is already handled and no further result processing is needed.

### Current Behavior

20 out of 27 return null statements in Flowsheet2Action were sitting right after a JSON write to the response. This means Struts was not being explicitly told the response was already handled, leaving room for unintended result resolution.

### Affected Components

The only file directly affected is src/main/java/io/github/carlos_emr/carlos/flowsheet/Flowsheet2Action.java. The new test class src/test/java/io/github/carlos_emr/carlos/flowsheet/Flowsheet2ActionTest.java was also added as part of the fix.

---

## Reproduction Process

### Environment Setup

I set up the development environment using Docker Desktop and VS Code with the Dev Containers extension on Windows. The biggest challenge was enabling WSL integration in Docker Desktop, since without it the container could not find the Docker daemon. Once that was enabled under Settings, Resources, and WSL Integration, everything connected properly. I also had to run git config --global --add safe.directory /workspace inside the container because Git flagged an ownership mismatch that was blocking the build.

### Steps to Reproduce

1. Open Flowsheet2Action.java and search for return null.
2. Look at the lines right before each occurrence and check if the method wrote JSON to the response using writeJsonResponse() or objectMapper.writeValue().
3. You will find 20 places where the method writes JSON and then returns null instead of NONE, which goes against the project convention in CLAUDE.md.

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/alphin-08/carlos/commit/3cefe624cf1cce0ee9b9351c0e974608a910d103 
- **Screenshots/logs:** Not applicable since this is a code convention issue and not a visible crash.
- **My findings:** A full audit of all 27 return null statements confirmed that 20 of them follow a direct JSON write and should return NONE. The remaining 7 were left unchanged because 4 are private helper methods where null means "not found" and has nothing to do with Struts, and 3 are action methods that do not write anything to the response.

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
