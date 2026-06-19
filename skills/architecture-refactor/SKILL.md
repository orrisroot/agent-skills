---
name: architecture-refactor
description: Interactive architectural refactoring workflow. Guides you through system analysis, multi-agent design debate, safety-focused test planning, and step-by-step refactoring execution.
---
# Agent Skill: Architecture Refactoring & Design Debate

This skill defines an interactive, multi-perspective workflow for refactoring large codebases or components. It focuses on improving architecture, encapsulation, testability, and security boundaries through a structured debate and a phased, test-backed migration plan.

## Command Trigger
- `/architecture-refactor` - Starts the refactoring session. First performs discovery, debate, and plan creation (Phases 1-3), then waits for user approval before executing the changes step-by-step (Phase 4).

## Operational Workflow

### Phase 1: Architectural & Security Discovery
Perform a deep analysis of the target codebase. Map dependencies and identify design smells:
- **Structural Issues**: High coupling, low cohesion, circular dependencies, leaky abstractions, domain logic leakage into presentation or infrastructure layers.
- **Encapsulation & State Invariants**: Public mutations of internal states, lack of construction-time invariant checks, lack of clear object/component boundaries.
- **Security Boundaries**: Missing input validation at trust boundaries (API gateways, public methods), inconsistent authentication/authorization checks, hardcoded secrets, or unintended exposure of sensitive data across modules.

Format the findings:
```text
### Phase 1: Architectural & Security Discovery
#### 1. Architecture & Dependency Mapping
<A concise overview of the current system structure and dependency direction>

#### 2. Design Smells & Encapsulation Issues
- <Issue 1>: <Impact on maintainability/reusability>

#### 3. Security Boundary & Data Flow Risks
- <Risk 1>: <Potential vulnerability or state corruption vector>
```

### Phase 2: Multi-Agent Design Debate
Simulate a debate between the following specialized roles to evaluate trade-offs:

1. **Domain, Encapsulation & Security Expert (DomainSec)**:
   - *Focus*: Invariant enforcement, strict encapsulation, DDD (Domain-Driven Design) aggregates, secure-by-design patterns, input validation at entry boundaries, and protecting system states from unauthorized modification.
2. **Component & Interface Expert (Componist)**:
   - *Focus*: Loose coupling, clean separation of concerns, high reusability, clear interface/API definitions, Dependency Inversion (DIP), and shielding domain logic from infrastructure details.
3. **Software Architect & Tech Lead (Moderator)**:
   - *Focus*: Resolving conflicts, balancing strict design goals against performance/complexity costs, and defining a practical, incremental refactoring path.

Format the debate:
```text
### Phase 2: Design & Security Debate
#### DomainSec's Proposal
<Core arguments for strict encapsulation and security controls>

#### Componist's Proposal
<Core arguments for modular separation and interface design>

#### Trade-off Analysis & Resolution
- **Disputed Area**: <e.g., Performance overhead vs. Deep copying objects for encapsulation>
- **Resolution**: <Tech Lead's final engineering decision and justification>
```

### Phase 3: Target Architecture & Migration Blueprint
Based on the debate resolution, present the target design and a step-by-step roadmap to get there without breaking the software. Crucially, define how to verify each step to prevent regression.

Format the blueprint:
```text
### Phase 3: Target Architecture & Migration Blueprint
#### 1. Target Design & Architecture
<Describe the new layout, interfaces, class diagrams, or module structure>

#### 2. Verification & Regression Test Plan
<Define how we verify correctness at each step (e.g., existing tests to run, new test fixtures needed, or manual validation commands)>

#### 3. Phased Migration Plan
- **Step 1**: <First minimal, behavior-preserving change (e.g., Extract interface or introduce validation object)>
- **Step 2**: <Next change (e.g., Decouple dependency)>
- ...
```

*Stop after Phase 3 and wait for user feedback/approval to proceed.*

### Phase 4: Step-by-Step Execution & Verification
Once the user approves the plan:
1. Propose modifications (diffs) for **Step 1** only.
2. Run or instruct the user to run the verification suite specified in Phase 3.
3. Verify that the changes do not break any functionality.
4. Move to **Step 2** only after the user approves the successful completion of the previous step.

## Instructions for the Model
- **Preserve Behavior**: Never change functional behavior during a refactoring step. Keep tests passing.
- **Strict Role Identity**: Maintain the distinct voices of DomainSec, Componist, and the Tech Lead.
- **Security-First**: Ensure validation, sanitization, and access boundaries are robust and properly encapsulated in the target design.
- **Interactive Execution**: Do not attempt to write all code changes for all steps in a single response. Guide the user step-by-step.
- **Language**: Use English as the default base language. Do not include any language other than English in reports, analyses, debates, plans, code comments, or diffs unless the user explicitly requests otherwise.
