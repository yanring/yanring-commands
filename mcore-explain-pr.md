# Megatron-LM PR Deep Dive Prompt

You are a senior Megatron-LM and PyTorch expert. I am a Megatron-LM developer seeking to deeply understand a PR's implementation.

## Methodology

Use **"Top-Down + Execution Path Tracing"** approach:
1. First establish the big picture (architecture changes)
2. Then trace through the code execution path
3. Finally examine module relationships and edge cases

---

## Phase 1: Context Gathering

When given a PR number, execute two commands at once:
```bash
curl -s https://api.github.com/repos/NVIDIA/Megatron-LM/pulls/<pr-number> && curl -sL https://github.com/NVIDIA/Megatron-LM/pull/<pr-number>.diff
```

Then proactively explore related files in the codebase to understand:
- How the modified code fits into the larger system
- What existing abstractions/patterns are being used or extended
- The before/after state of the implementation

---

## Phase 2: Analysis & Output

Generate a technical document with the following **mandatory sections** in order:

### 1. TL;DR (3-5 sentences max)

Provide a concise summary answering:
- **What**: What does this PR change? (one sentence)
- **Why**: What problem does it solve? (one sentence)
- **Impact**: Who/what is affected? (one sentence)

### 2. Big Picture

#### 2.1 Before vs After Architecture

Draw a **Mermaid flowchart or block diagram** showing the architectural difference:

```mermaid
graph LR
    subgraph Before
        A1[Component A] --> B1[Component B]
    end
    subgraph After
        A2[Component A] --> C2[New Component C]
        C2 --> B2[Component B]
    end
```

Clearly label what's NEW, MODIFIED, or REMOVED.

#### 2.2 Change Scope Summary

Create a table categorizing all changed files:

| Category | Files | Description |
|----------|-------|-------------|
| New | `path/to/new.py` | Brief purpose |
| Modified | `path/to/existing.py` | What changed |
| Deleted | `path/to/old.py` | Why removed |

### 3. Design Rationale

#### 3.1 Problem Background

Explain the **original limitation or issue**:
- What was the previous behavior?
- Why was it insufficient?
- Include minimal code snippets showing the problematic pattern if applicable.

#### 3.2 Solution Approach

Explain **why this implementation was chosen**:
- What alternatives exist? Why weren't they chosen?
- What are the trade-offs of this approach?
- Key design decisions and their justifications.

#### 3.3 Key Design Points

List the core abstractions, interfaces, or data structures introduced/modified:
- New classes/functions and their responsibilities
- Interface contracts (inputs, outputs, side effects)
- Important invariants that must be maintained

### 4. Execution Path Deep Dive

**This is the most critical section.** Walk through the code as if you're debugging it.

#### 4.1 Entry Point

Identify WHERE the new/modified code gets triggered:
```python
# Example: This change is triggered when...
def train_step(...):
    # ... eventually calls ...
    new_function()  # <-- Entry point to new code
```

#### 4.2 Call Chain Visualization

Draw a **Mermaid sequence diagram** showing the execution flow:

```mermaid
sequenceDiagram
    participant Caller
    participant NewModule
    participant ExistingModule
    
    Caller->>NewModule: method_a(args)
    NewModule->>NewModule: internal_processing()
    NewModule->>ExistingModule: delegate_work()
    ExistingModule-->>NewModule: result
    NewModule-->>Caller: transformed_result
```

#### 4.3 Data Flow

Draw a **Mermaid flowchart** showing how data transforms through the pipeline:

```mermaid
graph TD
    A[Input: raw_tensor shape=BxSxH] -->|preprocess| B[Normalized tensor]
    B -->|split| C[Tensor chunks]
    C -->|parallel_op| D[Processed chunks]
    D -->|gather| E[Output: result shape=BxSxH]
```

Include:
- Data shapes/types at each stage
- Key transformations
- Where parallelism (TP/PP/DP) affects the data

#### 4.4 Core Code Walkthrough

For each critical code block, provide **annotated code** with inline comments:

```python
def critical_function(input_tensor, config):
    # Step 1: Validate inputs
    # Why: Ensures tensor is on correct device before distributed ops
    assert input_tensor.is_cuda, "Must be on GPU"
    
    # Step 2: Reshape for parallel processing
    # Key insight: We split along sequence dim for TP
    chunks = input_tensor.chunk(config.tp_size, dim=1)
    
    # Step 3: Process each chunk
    # Note: This is where the main compute happens
    results = []
    for chunk in chunks:
        # ... detailed logic explanation ...
        processed = self._process(chunk)
        results.append(processed)
    
    # Step 4: Gather results
    # Important: Order matters for correctness
    return torch.cat(results, dim=1)
```

Use `# ...` to skip non-essential lines, but ALWAYS explain:
- **Why** each step exists (not just what it does)
- **Edge cases** the code handles
- **Assumptions** the code makes

### 5. Module Relationships

Draw a **Mermaid class diagram** showing how components relate:

```mermaid
classDiagram
    class ExistingBase {
        +existing_method()
    }
    class NewClass {
        +new_field: Tensor
        +new_method()
        -_helper()
    }
    class ModifiedClass {
        +changed_method()
    }
    
    ExistingBase <|-- NewClass : extends
    NewClass --> ModifiedClass : uses
    ModifiedClass ..> ExistingHelper : depends
```

Highlight:
- Inheritance relationships
- Composition/aggregation
- New dependencies introduced

### 6. Risks & Edge Cases

Analyze potential issues:

| Risk Category | Specific Concern | Mitigation/Status |
|---------------|------------------|-------------------|
| **Correctness** | Does it handle PP+TP combination? | Tested in `test_xxx.py` / NOT TESTED |
| **Performance** | Extra memory allocation in hot path? | Negligible / Needs profiling |
| **Compatibility** | Breaks existing checkpoint format? | Backward compatible / Migration needed |
| **Edge Cases** | What if sequence_length=1? | Handled in line X / Potential bug |

### 7. Quick Reference

#### 7.1 File Change Summary

```
path/to/file1.py  [+150, -30]  # Brief description
path/to/file2.py  [+50, -10]   # Brief description
```

#### 7.2 Key Functions Index

| Function | File | Purpose |
|----------|------|---------|
| `new_function()` | `module/new.py:42` | One-line description |
| `modified_func()` | `module/old.py:100` | What changed |

#### 7.3 Related Code Pointers

List files/functions a reader should explore next for deeper understanding:
- `path/to/related.py` - Related feature X
- `docs/design.md` - Original design doc if exists

---

## Output Requirements

1. **Language**: Match the PR's primary language (English PR → English doc, Chinese PR → Chinese doc)
2. **Code blocks**: Always include actual code snippets, not just descriptions
3. **Diagrams**: Every section that requests a Mermaid diagram MUST include one
4. **Specificity**: Use exact function names, line numbers, and file paths
5. **Completeness**: Do not skip any mandatory section; write "N/A - [reason]" if truly not applicable
