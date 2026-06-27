# Contribution [1]: Replace Flowsheet2Action direct-response return nulls with NONE

**Contribution Number:** 1  
**Student:** Alphin Shajan  
**Issue:** https://github.com/carlos-emr/carlos/issues/2885 
**Status:** Completed

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

The root cause was that Flowsheet2Action was written before the project adopted the explicit return NONE convention for direct-response actions. The methods were functional but did not follow the current guidance in CLAUDE.md, which says that any action writing directly to the response must end with return NONE so Struts knows not to attempt any further result resolution.

### Proposed Solution

The fix was to audit every return null in the file, identify which ones follow a direct JSON write, and change only those to return NONE. The ones that are private helpers or non-writing action methods were left unchanged to keep the PR focused.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** 
Flowsheet2Action had 27 return null statements and 20 of them came right after writing JSON to the HTTP response, which violates the project convention in CLAUDE.md.

**Match:**
The pattern of using return NONE after writing directly to the response is already established across other action classes in the project. This fix brings Flowsheet2Action in line with that same convention.

**Plan:** 
1. Audit all 27 return null statements in Flowsheet2Action.java and categorize each one as either a direct-response branch or a non-writing branch.
2. Change the 20 direct-response branches from return null to return NONE.
3. Leave the 7 remaining occurrences unchanged, 4 private helpers where null means "not found" and 3 action methods that do not write to the response.
4. Add Flowsheet2ActionTest.java with regression tests covering representative direct-response paths.

**Implement:** 
https://github.com/alphin-08/carlos/commit/3cefe624cf1cce0ee9b9351c0e974608a910d103

**Review:**
The change follows the convention in CLAUDE.md, stays within the scope of the issue, includes a signed off commit, targets the develop branch, and adds regression tests as required by the acceptance criteria.

**Evaluate:**
The fix was verified by running mvn test -Dtest=Flowsheet2ActionTest. All 3 tests passed with 0 failures and the build completed with BUILD SUCCESS.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: list() returns NONE and writes JSON with the correct content type when called as a direct-response path.
- [ ] Test case 2: getValidations() returns NONE and writes JSON with the correct content type when called as a simple read path.
- [ ] Test case 3: removeItem() returns NONE and writes JSON with the correct content type when called as a mutator-style response path.

### Integration Tests

I didn't need to write any integration tests for this update. The fix was really just about adjusting how return values are formatted in the code. It doesn't touch the database or change how different parts of the app talk to each other.

### Manual Testing

Since this was just fixing code style and not an actual glitch you'd see on the screen, I didn't need to manually click around and test it in the browser. I know everything works correctly because all the unit tests passed and the whole project compiled perfectly with a clean BUILD SUCCESS.

---

## Implementation Notes

### Week [1] Progress

This was the first issue I worked on as a new contributor so most of the week was spent getting the development environment working. The main challenges were enabling WSL integration in Docker Desktop, fixing a Git ownership mismatch inside the container with git config --global --add safe.directory /workspace, and dealing with VS Code disconnecting from the container during heavy operations like builds and tests. I fixed the disconnects by disabling Resource Saver mode in Docker Desktop and creating a .wslconfig file to give WSL more memory and CPU.

### Week [2] Progress

Once the environment was stable I picked up issue 2885 and used Claude Code to audit all 27 return null statements in Flowsheet2Action.java. I categorized each one and made the 20 targeted changes from return null to return NONE in the direct-response branches. I also wrote the regression tests in Flowsheet2ActionTest.java covering three representative paths. The build passed and all 3 tests came back green. After that I committed the changes with a DCO sign off and opened the pull request targeting the develop branch on the original repo.

### Week [3] Progress

The pull request was accepted and merged into the develop branch. This was my first open source contribution getting merged into a real project. I also spent a lot of time writing documentation for this file. I am currently looking through the open issues to find my next task. I am trying to pick something that is a manageable size and fits what I already know about the code.

### Code Changes

- **Files modified:** src/main/java/io/github/carlos_emr/carlos/flowsheet/Flowsheet2Action.java, src/test/java/io/github/carlos_emr/carlos/flowsheet/Flowsheet2ActionTest.java
- **Key commits:** https://github.com/alphin-08/carlos/commit/3cefe624cf1cce0ee9b9351c0e974608a910d103 
- **Approach decisions:** I only changed the return null statements that came directly after a JSON write to the response. The other 7 were left unchanged on purpose. Four of them are private helper methods where null means "not found" and is a real return value, not a Struts result. The other three are addMeasurement, addPrevention, and reload, which do not write anything to the response so they fall outside the scope of this issue. I mentioned this in the PR description so the reviewers knew it was on purpose and not a mistake.

---

## Pull Request

**PR Link:** https://github.com/carlos-emr/carlos/pull/2914

**PR Description:** The Flowsheet2Action file had methods that write JSON directly to the response and then return null. According to the CLAUDE.md file, these should return NONE instead. This stops the Struts framework from doing extra work after the response is already finished. This update changes 20 places to return NONE instead of null. I also added a test file called Flowsheet2ActionTest to check three of the main paths. I left the other seven null returns alone on purpose. Four of them are private helper methods, and three do not write anything to the response.

**Maintainer Feedback:**
- [06/17/2026]: No further feedback was given but the PR got approved and merged. 

**Status:** Merged

---

## Learnings & Reflections

### Technical Skills Gained

I got much more comfortable working in a Docker dev container. I learned how to connect Windows WSL with Docker Desktop and configure it properly. I also gained real experience seeing how big Java projects are built using Spring, Struts, and Maven. I finally understood how Struts processes results and why returning NONE is important. For testing, I learned how to write unit tests to check HTTP responses and verify the output. I also practiced the whole open source process from branching my code to getting a pull request merged.

### Challenges Overcome

My biggest challenge was just getting the setup to work. I had to fix Docker connection issues, Git errors, and VS Code crashing before I even wrote any code. I had to really figure out what was broken instead of just restarting my computer. Once the setup was done, the hardest part was knowing which null returns to change. I could not just replace all of them. I had to read every method carefully and explain my choices so the reviewers understood my logic.

### What I'd Do Differently Next Time

Next time, I will set up my environment before picking an issue so I do not waste time. I will also remember to sign my commits from the start instead of fixing them later. Overall, the process went really well and I feel much more confident for my next task. 
---

## Resources Used

- Link to helpful documentation: https://github.com/carlos-emr/carlos/blob/develop/CONTRIBUTING.md, https://github.com/carlos-emr/carlos/blob/develop/CLAUDE.md, https://github.com/carlos-emr/carlos/blob/develop/.devcontainer/README.md
