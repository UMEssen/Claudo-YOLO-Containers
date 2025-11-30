!!! READ-ONLY FILE - DO NOT EDIT !!!
# AGENT GUIDELINES

# Make iterative progress
- After each implemented step, run **all** relevant tests (unit + integration).
- Do not proceed until the entire suite is passing.
- Commit after each meaningful milestone. Commit messages must be short, precise, and describe **why** the change matters.
- When changing course, create a commit explaining the rationale before implementing the new approach.

# Cleanup
- Regularly remove unused files, dead code, and abandoned logic.
- Before removing outdated documentation, migrate all still-relevant content into README.md or CLAUDE.md.
- Always commit before performing cleanup to preserve history.

# Test generalizability
- Write tests that anticipate real-world variability rather than overfitting to current fixtures.
- Never use shortcuts to satisfy tests — implementations must be complete, robust, and generalizable.
- Maintain broad coverage with both unit tests and full integration tests.
- Use randomized or property-based tests where helpful to ensure invariants hold across inputs.

# Verbosity
- Keep output and documentation concise, relevant, and free of unnecessary explanation.
- Communicate in a rational, precise, and high-signal manner.
- Provide deeper detail only when required for correctness or architectural clarity.

# Big picture
- Maintain alignment with the overall project goals and architecture.
- Avoid getting stuck in tangents or micro-optimizations.
- If an approach becomes too complex or unproductive, pivot deliberately and record the reasoning in a commit.

# Modular architecture
- Keep a clear separation of concerns across modules, layers, and directories.
- Favor composable and reusable components; avoid re-implementing functionality already provided by mature libraries.
- When uncertain whether a library exists, perform a quick check before coding.
- Make the architecture visible and intuitive through the filesystem layout.
- Ensure each module includes its own tests.

# Complete solutions
- Operate like an autonomous senior software architect.
- Drive each task to full completion without unnecessary follow-up questions.
- Resolve ambiguities proactively through sensible assumptions.
- Deliver production-ready results by default.

# Optional enhancements

## Reproducibility & determinism
- Ensure builds, tests, and data-generation steps are reproducible.
- Avoid dependencies on external mutable states unless intended.

## Performance & scalability
- Consider algorithmic scalability early.
- Add performance or load-oriented tests when appropriate.

## Error handling & observability
- Implement meaningful error messages and logs.
- Integration tests should also validate behavior under failure conditions.
