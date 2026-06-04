---
name: review-debate
description: >-
  A multi-agent debate code review workflow involving Security/Performance and Clean Architecture/Readability experts, moderated by a Tech Lead, to produce high-quality, balanced code reviews.
---

# Agent Skill: Multi-Agent Debate Code Review

This skill enables a multi-perspective debate workflow between two virtual expert reviewers to ensure high-quality code reviews, even when using a single LLM under a BYOK environment.

## Command Trigger
- `/review-debate` - Triggers the multi-agent consensus review process for the target diff or code snippet.

## Operational Workflow
Before initiating the review process:
1. Identify the target code to review. The target can be:
   - A specific code snippet provided by the user.
   - The contents of the active/open file.
   - The changes in the current Git repository (`git diff` or `git diff --cached`).
2. If the target is ambiguous, ask the user to clarify which file or code snippet they want reviewed.

## Core Reviewers Definition
The agent must split its cognitive process into three distinct roles sequentially:

1. **Reviewer Alpha (SecPerf):**
   - **Focus:** Security vulnerabilities, performance bottlenecks, resource leaks, edge cases, and optimization.
   - **Tone:** Rigorous, defensive, and analytical.

2. **Reviewer Beta (CleanArch):**
   - **Focus:** Code readability, maintainability, clean architecture, adherence to design patterns, naming conventions, and documentation.
   - **Tone:** Constructive, structured, and developer-experience-oriented.

3. **Tech Lead (Moderator):**
   - **Focus:** Synthesizing both perspectives, resolving conflicts, determining the final consensus, and formulating the final patch.

## Execution Workflow

When `/review-debate` is invoked, the agent MUST execute the following four phases inside a single inference session (or chain of thought), ensuring each perspective is explicitly articulated.

### Phase 1: Independent Assessments
Analyze the code diff independently from both perspectives. Do not let one perspective bias the other in this phase.

```text
[Output Format]
### Phase 1: Initial Findings
#### Reviewer Alpha (SecPerf)
- <Finding 1>
- <Finding 2>

#### Reviewer Beta (CleanArch)
- <Finding 1>
- <Finding 2>
```

### Phase 2: Cross-Evaluation & Challenge
Reviewer Alpha must challenge Reviewer Beta's findings, and vice versa. Specifically look for:
- Contradictions (e.g., Alpha demands an optimization that destroys Beta's readability goal).
- Overlooked points or false positives.

```text
[Output Format]
### Phase 2: Cross-Evaluation
#### Alpha's Response to Beta
- [Agree/Disagree/Modify] because ...

#### Beta's Response to Alpha
- [Agree/Disagree/Modify] because ...
```

### Phase 3: Consensus & Resolution
The Tech Lead role takes over to resolve remaining disagreements, weighting the tradeoffs based on production-ready engineering standards.

```text
[Output Format]
### Phase 3: Consensus & Resolution
- **Trade-off Analysis:** <Synthesize disagreements, weight performance vs readability, etc.>
- **Resolution:** <State the final decision on each disputed point>
```

### Phase 4: Final Recommendation
Output the unified, agreed-upon review points and the exact code modifications needed.

````text
[Output Format]
### Phase 4: Final Consensus Report
#### Agreed Improvements
1. **Description:** ...
   - **Reason:** ...
   - **Target File/Line:** ...

#### Suggested Code Fixes
```diff
# Provide the final consolidated diff here with English comments.
# If no code modifications are needed, explicitly state: "No changes required."
```
````

## Instructions for the Model
- **Do not mix identities:** Maintain strict separation between Alpha, Beta, and Tech Lead.
- **Do not skip the debate:** Even if the code looks good, both reviewers must proactively look for subtle edge cases or alternative architectural patterns.
- **Output comments in code:** Any generated source code or code snippets within the final patch MUST use English for comments.
- **Report Language:** Write the review comments, findings, and consensus in the same language as the conversation/user request (e.g. Japanese if the user asked in Japanese). However, any comments inside the code diff itself MUST be in English.
