# Wikispeedia: BFS vs Bidirectional BFS vs A* Search

Finding the shortest click-path between Wikipedia articles using three search algorithms, and comparing them against real human players.

**Course:** Artificial Intelligence
**Team:** Mahanaz Afrin (23549009023), Sumiya Afrin (23549009033), Sabiqun Naher Samia (23549009045)
**Instructors:** Jasiya Fairiz Raisa (Lecturer) and Nazneen Akter (Assistant Professor), Department of ICT, BUP

---

## Overview

The Wikispeedia game asks a player to travel from a start article to a target article using only hyperlinks. This project models the task as graph search:

- **State:** a Wikipedia article
- **Action:** follow one outgoing link
- **Cost:** 1 per click
- **Goal:** reach the target article

Three algorithms are implemented. Each returns `(path, nodes_expanded)`.

| Algorithm | Type | Notes |
|---|---|---|
| BFS | Uninformed | Baseline, always optimal |
| Bidirectional BFS | Uninformed | Searches from both ends using forward and reverse links, expands full levels, optimal |
| A* Search | Informed | `f(n) = g(n) + W * h(n)`, `W = 2.5` by default, not guaranteed optimal |

**A\* heuristic:** `h(n) = 1 - (0.5 * category_similarity + 0.5 * text_similarity)`, where category similarity is the Jaccard overlap of category sets and text similarity is the TF-IDF cosine similarity to the target.

## Dataset

Downloaded automatically by the notebook from the [Stanford SNAP Wikispeedia page](https://snap.stanford.edu/data/wikispeedia.html):

- `wikispeedia_paths-and-graph` (about 9.5 MB): links, categories, human game paths, official distance matrix
- `wikispeedia_articles_plaintext` (about 35 MB): article text

After loading: 4,604 articles with text, 4,587 with outgoing links, 119,882 links, average 26.0 links per article.

## Requirements

- Python 3
- numpy, pandas, matplotlib
- scikit-learn
- scipy
- networkx
- sentence-transformers (only for Sections 17 and 18)

The notebook installs `sentence-transformers` itself with `pip`. Its download cells use `!wget` and `!tar`, so it is meant to run in Google Colab or Jupyter on Linux or macOS. A GPU runtime is optional but speeds up Section 17.

## How to Run

1. Open `Wikispeedia_Final_Project_Rearranged_3.ipynb` in Google Colab or Jupyter.
2. Run all cells from top to bottom.
3. The datasets download on the first run only. Later runs reuse the extracted folders.

Test routes use random seed 42, so results are reproducible. Timings will vary by machine.

## Notebook Structure

| Section | Content |
|---|---|
| 1 to 2 | Download the dataset; build `adj`, `rev_adj`, categories and the TF-IDF matrix |
| 3 | Dataset overview (link degree histograms) |
| 4 | BFS, Bidirectional BFS and A* implementations |
| 5 | Example routes and their solution paths |
| 6 | Main experiment on 200 routes from real human games |
| 7 | Comparison charts |
| 8 | Correctness check against BFS |
| 9 | Verification against the official distance matrix |
| 10 | Unfinished games analysis |
| 11 | Mean ± SD and paired Wilcoxon signed-rank tests |
| 12 | Human vs shortest path lengths |
| 13 | Results by path length |
| 14 | Peak memory usage (`tracemalloc`, first 30 routes) |
| 15 | Heuristic weight `W` experiment (50 routes) |
| 16 | Solution path graph visualization (NetworkX) |
| 17 | NLP extension: SBERT sentence embeddings in A* |
| 18 | NLP extension: embedding-guided beam for Bidirectional BFS |

Running the notebook also saves these images: `dataset_overview.png`, `comparison.png`, `unfinished.png`, `path_lengths.png`, `memory.png` and `paths.png`.

## Key Results (200 routes)

| Method | Avg path length (clicks) | Avg nodes expanded | Avg time (ms) | Optimal paths (%) |
|---|---|---|---|---|
| Human players | 5.30 | – | – | – |
| BFS | 2.77 | 1408.94 | 7.60 | 100.0 |
| Bidirectional BFS | 2.77 | 13.72 | 0.28 | 100.0 |
| A* | 2.82 | 64.94 | 33.62 | 95.5 |

- Bidirectional BFS was the most efficient: optimal on every route, fewest nodes, fastest, lowest memory (35.5 KB vs 144.4 KB for BFS and 192.2 KB for A*).
- A* expanded far fewer nodes than BFS but was optimal on only 95.5% of routes and was the slowest.
- Higher `W` makes A* faster but less optimal (on 50 routes: 100% optimal at `W = 0` and `W = 1`, 98% at `W = 2.5`, 72% at `W = 5`).
- Replacing TF-IDF with sentence embeddings in A* gave 58.29 nodes and 19.93 ms, with 92.5% optimal paths.
- All pairwise differences in nodes expanded and time are significant (Wilcoxon, p < 0.0001).

## Limitations

- The project is notebook-only and has no graphical interface.
- A* with `W = 2.5` is not guaranteed to return the shortest path.
- Memory was measured on only 30 routes, and the `W` experiment used 50 routes.

## Project Report

The full academic report (`AI_Project_Report_Wikispeedia.docx`) accompanies this notebook.
