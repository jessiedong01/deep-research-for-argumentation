# Deep Research for Argumentation by Query Decomposition

Deep research systems such as OpenAI Deep Research and STORM search the web and write a cited survey of a question. Many tasks need an argument instead, a position with evidence behind each claim that supports it.

Planning the argument before searching makes that possible. A language model first decomposes the question into a directed acyclic graph of sub-questions. The system then answers each leaf with retrieval-augmented generation and composes every parent from its children under generated instructions, adding new sub-questions wherever a composed answer is incomplete. The final report argues the answer at the root. Every source keeps one citation number across the whole graph, so each claim traces back to the page it came from.

The [paper](paper/main.pdf) compares this approach with OpenAI Deep Research and STORM on ten contested questions and measures whether each citation points to the source its claim came from.

## Setup

```bash
python -m venv .venv && .venv/bin/pip install -e .
cp .env.example .env   # add an OpenAI (or Azure OpenAI) key and a Serper key
```

## Usage

```bash
.venv/bin/python src/deepcomposition/main.py
```

The pipeline asks for a research question and writes the DAG, every node's answer, and the final report to `output/`. Every source gets one citation number for the whole run (`src/utils/citations.py`), and `src/utils/attribution_audit.py` checks whether each cited sentence matches the source it cites.

## Reproducing the paper's analysis

```bash
.venv/bin/python scripts/fetch_sources.py          # text of every source in the evaluated bibliographies
.venv/bin/python scripts/text_analysis.py --check-links
.venv/bin/python scripts/attribution.py
.venv/bin/python scripts/draw_dag.py output/results.json paper/figs/example_dag.pdf
cd paper && tectonic -X compile main.tex
```

## Data

`data/reports/` holds the 20 retained evaluation reports (this pipeline and STORM, 10 motions), the motions, and the stance labels. `output/results.json` and `examples/` hold the logs of two runs. `data/link_check.json` records the link check.

## Authors

Justin Blumencranz and Jessie Dong, with Yucheng Jiang and Monica Lam.
