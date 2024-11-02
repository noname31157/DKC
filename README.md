# DKC: Data-driven and Knowledge-guided Causal Discovery

DKC is a Python-based framework for causal discovery that utilizes observational data and knowledge constraints to estimate causal graphs. This project provides tools to load datasets, estimate causal structures, and evaluate results using metrics like Adjacency Confusion, Arrow Confusion, and Structural Hamming Distance (SHD).

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Main Functions](#main-functions)
- [Example Code](#example-code)

## Installation
1. **Clone the repository**:
    ```bash
    git clone https://github.com/noname31157/DKC.git
    cd DKC
    ```
2. **Install required packages**:
   Make sure you have installed the following packages:
    ```bash
    pip install numpy scipy scikit-learn pandas matplotlib statsmodels
    ```

## Usage

1. Place your data file (`.npy` format) and true graph file (`.graph.txt` format) in the directory.
2. Update the code to reference your dataset file (e.g., `data15.npy`) and ground truth file (e.g., `data15.graph.txt`).
3. Run the main code `run_dkc.py` to generate the causal graph.

## Project Structure

- `dkcmodels/`: Contains modules for causal discovery and evaluation.
  - `graph/`: Classes to handle confusion metrics and structural evaluations.
  - `search/`: Contains the main `DKC` search method for causal discovery.
  - `utils/`: Utilities for handling graphs and conversions.

## Main Functions

- **`dkc`**: Core function for causal graph estimation.
- **`AdjacencyConfusion`**: Computes adjacency-based recall and FDR.
- **`ArrowConfusion`**: Computes arrow-based true positives, false positives, and false negatives.
- **`SHD`**: Calculates the Structural Hamming Distance between estimated and true graphs.
- **Utility Function**:
  - **`txt2generalgraph`**: Loads a graph from a `.txt` file.

## Example Code

Here’s a basic code example to run DKC on a dataset and calculate metrics:

```python
import numpy as np
from dkcmodels.graph.AdjacencyConfusion import AdjacencyConfusion
from dkcmodels.graph.ArrowConfusion import ArrowConfusion
from dkcmodels.graph.SHD import SHD
from dkcmodels.search.ScoreBased.DKC import dkc
from dkcmodels.utils.DAG2CPDAG import dag2cpdag
from dkcmodels.utils.TXT2GeneralGraph import txt2generalgraph

# Load dataset and run DKC
dataset = np.load('data15.npy')
Record = dkc(dataset, 'local_score_BIC', None, None)
est = Record['G']

# Load true DAG and convert to CPDAG
truth_dag = txt2generalgraph("data15.graph.txt")
truth_cpdag = dag2cpdag(truth_dag)

# Evaluate Adjacency and Arrow Confusion
adj = AdjacencyConfusion(truth_cpdag, est)
arrow = ArrowConfusion(truth_cpdag, est)

# Metrics
arrowsTp = arrow.get_arrows_tp()
arrowsFp = arrow.get_arrows_fp()
arrowsFn = arrow.get_arrows_fn()
adjRec = adj.get_adj_recall()
adjFDR = adj.get_adj_FDR()
arrowRec = arrow.get_arrows_recall()
arrowFDR = arrow.get_arrows_FDR()
shd = SHD(truth_cpdag, est)

# Print Results
print(f"ArrowsTp: {arrowsTp}")
print(f"ArrowsFp: {arrowsFp}")
print(f"ArrowsFn: {arrowsFn}")
print(f"AdjTPR: {adjRec}")
print(f"AdjFDR: {adjFDR}")
print(f"SHD: {shd.get_shd()}")
print(f"ArrowTPR: {arrowRec}")
print(f"ArrowFDR: {arrowFDR}")
