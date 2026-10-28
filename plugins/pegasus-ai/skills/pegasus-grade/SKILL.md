---
name: pegasus-grade
description: Grade a Pegasus WMS workflow from 1 to 10 using evidence for completeness, documentation, a small test, and reproducibility
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
---

# Pegasus Workflow Grader

Grade an existing Pegasus WMS workflow when the user asks for a score, grade, or assessment. Give one integer grade from **1 (lowest) to 10 (highest)** and explain it with evidence from the workflow. Do not assume that a workflow follows the `pegasus-scaffold` layout; assess equivalent layouts and other Pegasus APIs fairly.

## Gather evidence

1. Use the workflow path supplied by the user. If none is supplied, use the current directory when it contains one workflow, or identify the sole workflow project beneath it. If several are plausible, ask which one to grade.
2. Read the workflow definition or generator, transformation and replica catalogs (or code that creates them), scripts called by jobs, dependency and container files, README, and any test data or test instructions. Follow references to files outside conventional names such as `workflow_generator.py`, `bin/`, and `run_manual.sh`.
3. Trace at least one path from input registration through jobs and dependencies to a declared output. Check whether referenced scripts, inputs, and dependencies exist, and whether command arguments match the files the jobs stage. Inspect all distinct step types where practical. A failure that prevents a usable DAG or output is a completeness issue, even if all expected filenames exist.
4. Look for a small, repeatable test: bundled or generated input, a command that runs the workflow or a representative end-to-end path, and a check of the expected output. If execution tools and dependencies are available, run the documented small test and record the command and result. If they are unavailable, grade the test from its files and instructions and say that execution was not verified. Never claim a test passed based only on reading it.
5. Classify the software stack before scoring the environment. A **complex stack** has multiple specialized tools or system libraries, conflicting runtimes, difficult native dependencies, or site-specific setup that makes a clean install hard to reproduce. Ordinary Python or shell scripts with a small, straightforward dependency set are **simple**. State which classification you used and the evidence for it. A workflow's scientific complexity alone does not make its software stack complex.

Read `references/PEGASUS.md` from the `pegasus-ai` plugin directory when a Pegasus API or catalog pattern is unclear. The neighboring `pegasus-review` skill offers a deeper correctness checklist; use it to investigate suspected defects, not as an additional set of mandatory grading criteria.

## Score

Start at **1 point**. Add the points below for a total of 1–10. Award only supported points; explain missing or uncertain evidence. A different but working project structure can earn full credit.

| Dimension | Points | Evidence required |
| --- | ---: | --- |
| Completeness | 0–4 | **0:** no usable Pegasus workflow definition. **1:** a partial definition exists, but core steps, inputs, or outputs are missing. **2:** a plausible DAG and scripts exist, but a major reference, dependency, or output path is broken or unspecified. **3:** the main input-to-output path is complete on inspection, with minor gaps or unverified details. **4:** all inspected paths are coherent: jobs, dependencies, staged inputs, executables, and declared outputs agree. |
| Documentation | 0–2 | **0:** absent or too sparse to run. **1:** purpose, inputs, and a basic run command are documented, but setup or outputs are unclear. **2:** a reader can identify prerequisites, inputs, exact setup and run steps, expected outputs, and important configuration choices. |
| Small test | 0–2 | **0:** no small test case. **1:** small input or test instructions exist, but the test is incomplete or has no output check. **2:** a repeatable small test has obtainable data, a run command, and an expected result or automated output check. A test can earn 2 from inspection when it cannot be executed locally; disclose that limitation. |
| Reproducible environment | 0–1 | **Simple stack:** award 1 when required dependencies and installation or environment setup are clear; a container is optional and its absence has no effect. **Complex stack:** award 1 when a usable container definition or image is supplied, its required tools are represented, and build/use instructions are documented. Otherwise award 0. |

Grade = `1 + completeness + documentation + small test + reproducible environment`. Do not add other bonuses or penalties. In particular, never subtract for a missing container in a simple stack. If evidence is unavailable because a file cannot be read, say so and score only what can be supported; distinguish that uncertainty from a confirmed defect.

## Report

Use this compact format:

```text
## Pegasus workflow grade: <name> — N/10

| Dimension | Points | Evidence |
| --- | ---: | --- |
| Completeness | X/4 | <specific files and findings> |
| Documentation | X/2 | <specific files and findings> |
| Small test | X/2 | <specific files and findings> |
| Reproducible environment | X/1 | <simple or complex stack and why; container assessment only if complex> |

Test verification: <command and result, or why it was not run>.

Priority improvements:
1. <highest-impact concrete fix, with file path>
2. <next fix, if needed>
```

Use repository-relative paths and line numbers when available. Give a short explanation for each point deduction and prioritize fixes that would raise the grade. Omit the improvements list when the workflow earns 10/10. Do not change workflow files unless the user separately asks for fixes.
