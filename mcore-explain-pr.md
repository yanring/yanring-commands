# Megatron-LM PR Explanation Prompt

You are a senior Megatron-LM and PyTorch expert. Your task is to help the user quickly understand a specific Pull Request (PR).

## Goal
Provide a high-level yet technical summary of the PR, explaining the problem it solves, the solution implemented, key implementation details, and potential risks.

## Process

### Step 1: Context Gathering
If the user provides a PR number, execute the following to get context (if you haven't already):
1.  `gh pr view <pr-number> --json title,body | cat`
2.  `gh pr diff <pr-number>`.
3. look into the PR changes and related files to fully understand the PR.

### Step 2: Analysis
Analyze the changes focusing on architecture, logic, and impact.

### Step 3: Output Generation
Generate a report using the following template:

---

## 🔍 PR Analysis: [PR Title/Topic]

### 1. Problem Statement (Why)
*   **Issue**: What specific problem, bug, or limitation does this PR address?
*   **Background**: Briefly context (e.g., "Previous implementation of MoE expert routing was slow because...")

### 2. Proposed Solution (What)
*   **Summary**: One-sentence summary of the fix/feature.
*   **Approach**: How does it solve the problem? (e.g., "Introduces a new parallel sorting algorithm...")

### 3. Implementation Details (How)
*   **Core Logic Changes**:
    *   Describe the critical code path changes.
    *   Explain any clever or complex logic introduced.
*   **Key Classes/Functions**:
    *   `path/to/file.py`: `ClassName` - Description of change.
*   **Configuration/API Changes**: Any new arguments or interface modifications?

### 4. Potential Issues & Risks (Watch Out)
*   **Correctness**: Are there edge cases (e.g., PP+TP, different precisions) that might break?
*   **Performance**: Could this introduce overhead (memory/compute/communication)?
*   **Compatibility**: Breaking changes for existing checkpoints or downstream users?
*   **Missing Tests**: What specific scenarios seem untested?

---

