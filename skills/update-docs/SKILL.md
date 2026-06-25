---
name: update-docs
description: >-
  Analyze the current codebase state, update project documentation to reflect the latest status,
  and maintain a dedicated release-based changelog only for tagged release versions.
---

# Update Docs Operator

## 1. Purpose
Keep project documentation (such as README.md, API references, architecture guides, and configuration schemas) up-to-date with the latest state of the codebase. Maintain a clean, professional change history under a dedicated section, summarizing changes per released version (based on Git tags) and omitting changelogs for unreleased code or individual atomic commits.

## 2. Command Trigger
* `/update-docs` - Triggers the document update operator to analyze the repository, update main documentation files, and update/create the release-based changelog.

## 3. Goals
* **Source Code Alignment:** Deeply inspect the source code to ensure that installation steps, API signatures, options, schema types, and setup parameters exactly match the latest implementation.
* **Latest State Only:** The main body of the documentation must represent the current, actual specifications. Never mix deprecated or historical feature descriptions in the main usage or setup sections.
* **Target Scope Default:** If target documents are not explicitly specified by the user, include all project documentation files in the update target scope.
* **Language Consistency & Default:** Unless explicitly instructed by the user, write updates in the same language as the existing document. When creating a new document (no pre-existing file), write it in English.
* **Unified Tone & Style:** Enforce a consistent writing style across all updates, ensuring that differences in underlying LLMs or agents do not lead to fragmented writing styles.
* **Continuous Execution:** Perform the update process from analysis to verification as a single, continuous run. Do not halt the workflow for intermediate confirmations between steps unless explicit clarification is required.
* **Dedicated Changelog:** Restrict all historical summaries to a dedicated, clearly labeled section (e.g., `## Changelog` or `## 変更履歴`).
* **Release-Based History:** Group change history strictly by tagged release versions. Do not list atomic, developer-centric commit messages.
* **No Unreleased Changelogs:** Prevent inclusion of changes made after the latest release tag. Keep the changelog strictly limited to official, tagged releases.

## 4. Writing Style & Tone Standards
To ensure a consistent, professional, and unified style regardless of the AI agent model utilized, strictly adhere to the following standards:

* **Tone & Voice:**
  * **Objective & Technical:** Use clear, matter-of-fact language. Avoid marketing jargon, exclamation marks, conversational fillers, or subjective adjectives (e.g., write "Adds authentication support" instead of "Introduces a powerful, modern authentication mechanism!").
  * **Concise & Direct:** Focus on clarity. Remove redundant phrasing.
* **Tense & Mood:**
  * **Current State:** Use the present tense to describe how the system functions (e.g., "The API returns..." rather than "The API will return...").
  * **Instructions:** Use the imperative mood for installation, configuration, or usage steps (e.g., "Run the build command" rather than "You should run the build command").
* **Vocabulary & Case Consistency:**
  * Match the exact case, capitalization, and naming conventions used in the source code (e.g., exact class names, variable names, environment variables like `PORT` or `dbConnection`).
  * Ensure consistent translation of technical terms within the same language context (e.g., do not mix "repo" and "repository" or "DB" and "database" within the same document).

## 5. Operational Workflow
You must follow these steps in sequence when updating documentation. **Execute the workflow continuously from Step 1 through Step 4 without interrupting the process to ask for permission or confirmation between steps**, unless you encounter a critical ambiguity that cannot be resolved without user input.

### Step 1: Codebase and Release Analysis
1. **Analyze Codebase State:**
   - Inspect build config files (e.g., `package.json`, `Cargo.toml`, `pyproject.toml`) and source code to verify the active version, configuration definitions, and public API interfaces.
   - Search for newly added, modified, or deprecated files, modules, classes, and environment variables.
2. **Fetch Release Tags & Dates:**
   - Execute git commands to extract tags and their creation dates:
     * `git tag -l` to list all tags.
     * `git log --tags --simplify-by-decoration --pretty="format:%d (%as)"` to view tags along with their author dates.
3. **Determine Release History:**
   - **No Tags Case:** If no git tags exist, treat the codebase as "unreleased." Do not generate or modify any changelog or version history.
   - **Tags Exist Case:**
     * Identify the latest released tag.
     * Determine differences between consecutive tags (e.g., `git log <prev-tag>..<tag> --oneline` or `git diff <prev-tag>..<tag> --stat`) to understand the changes introduced in each release.
     * Treat any commits made after the latest tagged release (i.e., `git log <latest-tag>..HEAD`) as "unreleased" and exclude them from the changelog.

### Step 2: Update Main Documentation Content
1. **Locate Target Files:** Find documentation files (e.g., `README.md`, `docs/`, config guides).
   - **Target Scope Policy:** If the user does not specify target files, you must include all project documentation files in the update scope.
2. **Synchronize Specifications:**
   - Update installation commands, requirements, and dependencies to match the latest build configurations.
   - Review and update public API signatures, parameters, response formats, and example payloads.
   - Update tables listing configuration options, environment variables, or schema fields.
3. **Refine Snippets & Diagrams:**
   - Ensure all code blocks and usage examples are functional and use the current API/syntax.
   - Use Mermaid diagrams where useful to illustrate complex system interactions, architectures, or execution flows.
4. **Remove Stale Information:** Delete instructions or warnings that apply only to outdated versions, keeping the main sections clean.

### Step 3: Update Changelog Section
1. **Locate or Create Section:** Ensure there is a dedicated `## Changelog` or `## 変更履歴` section. Create it if it is missing and release tags are present.
2. **Structure the Changelog:**
   - Group changes strictly by tagged release version, ordering from newest to oldest.
   - Display the release version and the release date (e.g., `## [1.2.0] - 2026-06-25`).
   - Categorize changes within each release using standard labels where appropriate:
     * **Added:** For new features.
     * **Changed:** For changes in existing functionality.
     * **Deprecated:** For soon-to-be-removed features.
     * **Removed:** For now-removed features.
     * **Fixed:** For any bug fixes.
     * **Security:** In case of vulnerabilities addressed.
   - Write clear, high-level summaries of the changes. Do *not* output raw commit messages, author names, or branch merges.
3. **Filter Out Unreleased Changes:** Ensure changes since the latest tag are not documented in the changelog.

### Step 4: Verification and Quality Control
1. **Check Internal & External Links:** Ensure all relative markdown links (`[link text](./other-file.md)`) and code symbol links (`[ClassName](file:///path/to/file#L12-L25)`) are valid.
2. **Verify Code Blocks:** Make sure code snippets have the correct syntax highlighting language specified and contain syntactically valid code.
3. **Validate Tone, Style & Language:**
   - Verify that all updates conform to the **Writing Style & Tone Standards** (Section 4).
   - **Language Selection Policy:** Unless explicitly instructed by the user, use the same language as the existing document for updates. If a document is being newly created (no pre-existing file), write it in English.

## 6. Common Pitfalls to Avoid
* **Mixing History into Main Content:** Inserting "Note: prior to version 1.1.0, this option was..." inside the main installation or usage guide. Keep the main sections focused strictly on the latest code.
* **Including Unreleased Commits:** Adding a "Changelog" entry for the current work-in-progress modifications that have not been tagged yet.
* **Verbosity in Changelog:** Listing minor, atomic commits (e.g., "fixed typo", "fixed lint issue") in the changelog. Summarize modifications at a feature/functional level instead.
* **Broken Code Examples:** Outdating the code examples when refactoring APIs, leading to user confusion.
* **Formatting Violations:** Mixing styling conventions, using broken HTML tags in Markdown, or leaving placeholder texts/TBDs.
