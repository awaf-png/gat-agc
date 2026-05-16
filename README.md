# GAT-AGC

Graph Attention Network for sustainable, multi-objective Automatic
Generation Control (AGC) in IoT-enabled hybrid AC/DC microgrids.

This repository contains the simulation environment, the GAT-AGC
controller, all baseline controllers, the training and evaluation
pipeline, and the random seeds and trained checkpoints needed to
reproduce every result, table and figure in the paper.

## Overview

The microgrid is modelled as a dynamic attributed graph. Each bus is a
node with an eight-dimensional IoT feature vector; each line is an edge
with impedance and power-flow attributes. A three-layer multi-head
graph attention encoder maps the graph to a fixed embedding that feeds
a shared actor-critic head trained with Proximal Policy Optimization.
Sensor readings are corrupted by delay, Gaussian noise and Bernoulli
packet loss so that the learned policy is robust to realistic
communication conditions.

Six controllers are compared under identical conditions: PID-AGC,
PI-AGC, MPC-AGC, DDPG-AGC, GCN-AGC and the proposed GAT-AGC.

## Repository layout

```
src/gatagc/
  environment.py   6-bus hybrid AC/DC microgrid simulator (Gym-style API)
  networks.py      GAT / GCN / MLP encoders and the actor-critic
  ppo.py           PPO trainer with Generalized Advantage Estimation
  ddpg.py          flat-vector DDPG baseline
  baselines.py     PID, PI and MPC controllers
  evaluate.py      evaluation harness and metrics
  seeding.py       reproducibility helpers and the published seed list
configs/           experiment configuration (matches Tables 2 and 3)
scripts/           train / evaluate / experiments / figure generation
tests/             unit tests
checkpoints/       trained policy weights and run metadata
results/           CSV outputs (Tables 4-6, scalability, ablation)
```

## Installation

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install -e .
```

PyTorch Geometric may require a wheel matching your PyTorch and CUDA
versions; see the PyTorch Geometric installation guide.

## Reproducing the results

Train the learned controllers (one seed shown; the published runs use
the ten seeds in `src/gatagc/seeding.py`):

```bash
python scripts/train.py --controller gat  --episodes 1000 --seed 13
python scripts/train.py --controller gcn  --episodes 1000 --seed 13
python scripts/train.py --controller ddpg --episodes 1000 --seed 13
```

Evaluate all six controllers and write Tables 4 and 5:

```bash
python scripts/evaluate.py --episodes 200
```

Run the scalability, topology-robustness and ablation experiments:

```bash
python scripts/experiments.py --experiment all
```

Generate the figures from the CSV outputs:

```bash
python scripts/make_figures.py
```

Run the unit tests:

```bash
pytest -q
```

## Reproducibility

* The ten evaluation seeds and the ablation seed are listed in
  `src/gatagc/seeding.py`.
* Trained checkpoints and per-run metadata are in `checkpoints/`.
* All operating data is generated online from the models in
  `environment.py`; no external dataset is required.

## Citation

If you use this code, please cite the associated paper. A `CITATION.cff`
file is provided in the repository root.

## License

Released under the MIT License. See `LICENSE`.
