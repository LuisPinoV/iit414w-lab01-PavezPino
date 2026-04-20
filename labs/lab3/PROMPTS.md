# PROMPTS.md - Lab 3 AI Traceability Log

## Entry 1
- Date: 2026-04-19
- Goal: Build a complete Lab 3 notebook from an empty file with comparable models under the same temporal validation.
- Prompt used (summary): "Create Lab 3 notebook with regression framing, MAE metric, train 2022-2023 and test 2024, include at least 2 baselines and 3 models, and report train vs test gap."
- AI suggestion: Use two baselines (global mean and grid heuristic), then compare Linear Regression, Ridge, and Random Forest using identical split and MAE.
- What I executed: Notebook cells 2, 3, 5, and 6.
- Evidence from outputs:
	- Cell 3 loaded data: 2022=440 rows, 2023=440 rows, 2024=479 rows.
	- Cell 5 produced comparison metrics: Random Forest test MAE=2.760, Grid baseline test MAE=3.092, Ridge test MAE=3.377, Linear Regression test MAE=3.399, Global mean baseline test MAE=5.912.
- Decision: Keep Random Forest as best model by test MAE; keep both baselines in final table.

## Entry 2
- Date: 2026-04-19
- Goal: Ensure reproducible execution and visible outputs in notebook.
- Prompt used (summary): "Run notebook and verify outputs are visible for methodology grading."
- Operational issue encountered:
	- Initial execution attempts did not run because notebook code cells had metadata language `code` instead of `python`.
	- Also required notebook kernel configuration before execution.
- Exact fix applied:
	- Updated notebook metadata language for code cells to `python`.
	- Configured notebook kernel (`.venv`, Python 3.12.3), then re-ran cells.
- Evidence from outputs:
	- Cell 2 prints Python/NumPy version and Seed=414.
	- Cell 5 prints `Train rows: 880 | Test rows: 479` and shows full comparison table.

## Entry 3
- Date: 2026-04-19
- Goal: Generate narrative deliverables consistent with notebook results.
- Prompt used (summary): "Write framing_decision, comparison_table, memo, and README aligned with executed notebook metrics and business framing."
- AI assistance accepted:
	- Structured framing around constructor points planning.
	- Non-technical memo wording for Head of Strategy.
- Human validation performed:
	- Cross-checked all reported numbers against notebook cell 5.
	- Ensured same metric and same temporal split in every row.

## Critical Distance (specific to this lab)
AI was helpful for structuring workflow and narrative, but it initially produced an execution issue (wrong code-cell language metadata). Without manual verification and re-execution, the notebook could have looked complete while failing the "visible outputs" requirement. This lab required checking real outputs and not trusting generated structure alone.
