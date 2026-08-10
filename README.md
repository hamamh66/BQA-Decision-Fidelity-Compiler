# BQA Decision-Fidelity Compiler

Official research artifacts for **“Decision-Fidelity-Aware Compilation of Bounded Quantum Arithmetic for Amplitude Estimation.”**

This repository contains the Bounded Quantum Arithmetic compiler prototype (**BQA-C**), typed decision-fidelity certificates, an independent certificate verifier, quantum-arithmetic benchmarks, raw result tables, and reproducible analysis scripts associated with the manuscript.

## Overview

Quantum arithmetic circuits are essential to quantum search, counting, and amplitude estimation, but exact arithmetic can impose substantial depth, routing, and two-qubit-gate costs. BQA-C treats arithmetic accuracy as a task-dependent compilation decision.

Each program variable is assigned a contract describing:

- its admissible interval;
- its encoding and register width;
- its numerical tolerance;
- its overflow policy; and
- its role in the downstream computation.

The framework separates four error quantities that must not be conflated:

1. **Operator defect** `delta`: circuit-level approximation error.
2. **Probabilistic arithmetic contract** `(eta, alpha)`: arithmetic tolerance and failure probability.
3. **Threshold-band mass** `beta(eta)`: input probability mass near the decision threshold.
4. **Estimator uncertainty** `epsilon_AE`: uncertainty introduced by amplitude estimation.

For an ordered threshold decision, the core probabilistic guarantee is

```text
Pr[decision disagreement] <= beta(eta) + alpha.
```

The certificate records these quantities separately and reports the composed decision bound.

## Main contributions represented in this repository

- Contract-driven width synthesis for bounded integer variables.
- Explicit offset encoding and overflow obligations.
- Exact, ripple-carry, QFT, and banded-QFT arithmetic candidates.
- Probabilistic contracts for approximate quantum arithmetic.
- Margin-sensitive decision-fidelity certification.
- Typed JSON certificate schema: `bqa-certificate/0.2`.
- Independent certificate verification without rerunning the compiler.
- Predicate-first and sum-materialising threshold-oracle comparisons.
- BQA-Add, BQA-Decision, BQA-Risk, and margin-mass experiments.
- Repeated maximum-likelihood amplitude estimation and Monte Carlo comparisons.

## Evidence and scope

The current study establishes results on ideal and depolarising-noise simulators for small bounded domains. It does **not** claim hardware validation, practical quantum advantage, or universally superior predicate-first synthesis.

In the evaluated two-operand threshold instance, predicate-first synthesis remains more expensive than sum materialisation both per call and inside the tested Grover operators. BQA-C therefore treats predicate-first synthesis as a contract- and workflow-dependent candidate, not as a default optimization.

Current limitations include:

- evaluated widths up to `n = 6`;
- exhaustive contract extraction for small bounded domains;
- a two-operand implemented threshold predicate;
- a generic native-gate basis and depolarising noise model;
- no hardware or calibration-aware validation; and
- no claimed practical quantum advantage.

## Repository structure

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── bqa/
│   ├── contracts.py
│   ├── compiler.py
│   ├── arithmetic.py
│   ├── oracles.py
│   └── certificate.py
├── verifier/
│   ├── verify_certificate.py
│   └── README.md
├── schemas/
│   └── bqa-certificate-0.2.schema.json
├── certificates/
│   ├── moderate-banded-b2.json
│   └── verification-transcript.txt
├── benchmarks/
│   ├── bqa_add.py
│   ├── margin_mass.py
│   ├── bqa_decision.py
│   └── bqa_risk.py
├── notebooks/
│   └── reproduce_manuscript_results.ipynb
├── data/
│   ├── raw/
│   └── processed/
├── results/
│   ├── figures/
│   └── tables/
└── tests/
    ├── test_contracts.py
    ├── test_certificate.py
    └── test_decision_bound.py
```

The structure above is the expected public-release layout. Every file claimed in the manuscript should be committed before the repository URL is inserted into the final paper.

## Software environment

The manuscript experiments were conducted with:

- Python 3;
- Qiskit 2.5; and
- Qiskit Aer 0.17.

Create an isolated environment and install the recorded dependencies:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

On Windows PowerShell, activate the environment with:

```powershell
.venv\Scripts\Activate.ps1
```

## Reproducing the manuscript results

After installing the dependencies, run:

```bash
python benchmarks/bqa_add.py
python benchmarks/margin_mass.py
python benchmarks/bqa_decision.py
python benchmarks/bqa_risk.py
```

Alternatively, execute:

```text
notebooks/reproduce_manuscript_results.ipynb
```

The reproduction workflow should regenerate the manuscript tables and figures from the released raw records. Fixed seeds, transpilation settings, circuit basis, input distributions, and oracle-call accounting should be stored with every experiment.

## Certificate verification

The populated certificate used in the manuscript is expected at:

```text
certificates/moderate-banded-b2.json
```

Verify it independently with:

```bash
python verifier/verify_certificate.py \
  certificates/moderate-banded-b2.json
```

The verifier should check, without invoking the compiler:

- register widths from declared intervals;
- encodings and overflow obligations;
- `alpha` from released per-input output distributions;
- `beta(eta)` from the declared input law;
- the composed decision bound;
- the exactly computed misclassification probability;
- artifact hashes; and
- the final pass/fail status.

The expected verification transcript should be committed as:

```text
certificates/verification-transcript.txt
```

## Benchmark groups

### BQA-Add

Compares Cuccaro ripple-carry, exact QFT, and banded-QFT adders using logical and transpiled resources together with ideal arithmetic correctness.

### Margin-mass experiment

Tests the decision theorem across distributions with negligible, moderate, heavy, and adversarial threshold-band mass. Certified bounds are compared with exactly computed decision errors.

### BQA-Decision

Compares predicate-first and sum-materialising threshold circuits both per call and inside semantically equivalent Grover constructions.

### BQA-Risk

Evaluates bounded tail-probability estimation using maximum-likelihood amplitude estimation and matched-budget classical Monte Carlo over repeated seeds.

## Reproducibility checklist

Before the repository is cited from the submitted manuscript, confirm that it contains:

- [ ] the compiler source code;
- [ ] the certificate schema;
- [ ] the populated certificate cited in the manuscript;
- [ ] the independent verifier;
- [ ] the verifier transcript;
- [ ] raw per-input output distributions;
- [ ] raw MLAE and Monte Carlo trial records;
- [ ] all experiment seeds;
- [ ] the complete dependency file;
- [ ] scripts or notebooks regenerating every table and figure;
- [ ] a license;
- [ ] `CITATION.cff`; and
- [ ] a permanent archival release, preferably with a DOI.

## Authors

- **Naif A. Alsharabi** — University of Ha'il, Saudi Arabia
- **Rahma Zayoud** — Université de Moncton, Canada
- **Sghaier Guizani** — Université de Moncton, Canada; Alfaisal University, Saudi Arabia
- **Habib Hamam** — Université de Moncton, Canada; University of Johannesburg, South Africa; International Institute of Technology and Management, Gabon

Corresponding author: **Sghaier Guizani** (`sguizani@alfaisal.edu`)

## Citation

If this repository supports your research, please cite the associated manuscript:

```bibtex
@article{alsharabi2026bqa,
  title   = {Decision-Fidelity-Aware Compilation of Bounded Quantum Arithmetic for Amplitude Estimation},
  author  = {Alsharabi, Naif A. and Zayoud, Rahma and Guizani, Sghaier and Hamam, Habib},
  year    = {2026},
  note    = {Manuscript under review}
}
```

The bibliographic record should be updated after journal acceptance. A matching `CITATION.cff` file is recommended for GitHub citation support.

## Responsible use and interpretation

This repository is intended for research and reproducibility. Simulator-based resource or query-count improvements should not be interpreted as evidence of practical quantum advantage. Hardware conclusions require device-specific calibration, routing, noise, and end-to-end cost evaluation.

## Contact

Questions about the manuscript or repository may be directed to the corresponding author:

**Prof. Sghaier Guizani**  
Alfaisal University  
Email: `sguizani@alfaisal.edu`

