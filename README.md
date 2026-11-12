The Full Processor is in zip file CPPU_Full_Power_Reconstruction_2026-11-11.zip

and then the rest of these zips are Historical Archives of previous CPPU versions.

How to Use CPPU

The Computational Physics Processing Unit (CPPU) is an experimental computational framework supporting physics-inspired optimization, stochastic dynamics, and classical optimization research.

This guide covers the Full-Power Reconstruction (November 2026).

The main program is "full_cppu.py", which provides access to the recovered original CPPU engine and newer optimization algorithms.

1. System Requirements

Recommended environment:

- Windows 10 or 11
- Python 3.10
- Intel or AMD processor
- NumPy, Numba, PyTorch, SciPy, and tqdm
- NVIDIA GPU with CUDA-compatible PyTorch (optional)

CPPU does not require specialized hardware. The original processor can use CPU or supported CUDA hardware. The newer Phase 7 and Phase 9 optimization engines are CPU-based.

Installation

Download the complete repository or release ZIP from GitHub.

Extract the files into a folder, then open Command Prompt or PowerShell inside the folder containing "full_cppu.py".

Install dependencies:

py -3.10 -m pip install -r requirements.txt

For compatible Linux or macOS environments:

python3 -m pip install -r requirements.txt

Windows Automatic Setup

Run:

RUN_WINDOWS.bat

This script installs dependencies, checks release integrity, and launches the hybrid CPPU demonstration.

It is a command-line demonstration, not a graphical application.

2. Verify the Processor

Before running experiments, verify the installation.

py -3.10 full_cppu.py --mode verify

This checks:

- SHA-256 integrity of the complete release
- Authenticity of the recovered original Plunder source
- SAT solver correctness
- Ising energy calculations
- Optimization result verification

Successful tests print "PASS" messages.

Note: Full verification requires every file listed in "MANIFEST_SHA256.json". If the historical archives were moved into separate GitHub downloads, the full integrity check will fail until the distribution and manifest are updated.

For a narrower check of the original source and optimized solvers:

py -3.10 optimized\verify_release.py

3. Your First CPPU Experiment

Run the integrated processor demonstration:

py -3.10 full_cppu.py --mode hybrid --demo --steps 800 --agents 16 --polish --output results\hybrid_demo.npz

This command:

1. Generates a synthetic 96-spin optimization problem.
2. Runs the original CPPU/Plunder dynamics.
3. Runs the newer Phase 7 Ising optimization engine.
4. Independently verifies both results.
5. Selects the lower-energy solution.
6. Applies optional conventional one-spin refinement.
7. Saves the result for inspection.

Understanding the Output

A completed experiment saves:

results/
    hybrid_demo.npz
    hybrid_demo.json

The ".npz" file contains the solution and energy.

The ".json" file records the selected backend, candidate energies, parameters, and other metadata.

Hybrid mode runs multiple algorithms and therefore consumes additional computational resources. It is not automatically faster than using either solver independently.

4. Available Processing Modes

CPPU provides six command-line modes.

Mode| Function
"original"| Recovered original Plunder stochastic dynamics
"sat"| Phase 9 event-driven 3-SAT optimizer
"ising"| Phase 7 refractory-memory Ising optimizer
"hybrid"| Original CPPU combined with Phase 7 Ising search
"stability"| Archived experimental momentum/stability variant
"verify"| Integrity and correctness checks

A. Original CPPU Processor

Run the recovered original processor:

py -3.10 full_cppu.py --mode original --demo --profile sk96_tuned --output results\original_demo.npz

This uses the original physics-inspired stochastic evolution model with a tested parameter profile.

The original algorithm evolves interacting computational states using projected dynamics, diffusion, memory, and energy evaluation.

Historical configuration

py -3.10 full_cppu.py --mode original --demo --profile historical_sk_tuned --steps 2000 --agents 64

This example overrides the historical profile's original 50,000-step setting with 2,000 steps for a shorter demonstration.

B. SAT Optimization

CPPU includes an event-driven optimizer for exactly-three-literal Boolean satisfiability problems.

Run the included example:

py -3.10 full_cppu.py --mode sat --cnf optimized\examples\example.cnf --starts 16 --steps 10000 --output results\sat_solution.npz

Or generate a synthetic test:

py -3.10 full_cppu.py --mode sat --demo --starts 16 --steps 10000

SAT input uses DIMACS CNF formatting:

c Example SAT problem
p cnf 4 3
1 -2 3 0
-1 2 4 0
-3 -4 2 0

The SAT solver reports the number of violated clauses.

- "0" violations means every clause is satisfied.
- A positive number means some clauses remain unsatisfied.

The current solver requires exactly three distinct variables per clause and does not directly support arbitrary weighted MaxSAT or mixed-integer programming.

C. Ising Optimization

Ising optimization searches for low-energy spin configurations.

Run:

py -3.10 full_cppu.py --mode ising --ising optimized\examples\example_J.npy --steps 8000 --output results\ising_solution.npz

CPPU uses the objective:

[
E(s)=-(s^TJs+h^Ts)
]

Where:

- "s" represents binary spins taking values −1 or +1.
- "J" represents interactions between spins.
- "h" represents external field values.
- Lower energy represents a better solution.

Custom matrices must be finite, square, symmetric, and have a zero diagonal.

D. Hybrid Optimization

Hybrid mode combines the original Plunder processor and the newer Phase 7 Ising optimizer.

py -3.10 full_cppu.py --mode hybrid --ising optimized\examples\example_J.npy --steps 8000 --polish --output results\hybrid_solution.npz

The program evaluates both candidate solutions and returns the one with lower verified energy, optionally refined by a conventional greedy search.

This is useful for testing complementary approaches, although it requires more computation than either method alone.

E. Experimental Stability Processor

Run the archived stability variant:

py -3.10 full_cppu.py --mode stability --ising optimized\examples\example_J.npy --steps 1000 --agents 16 --device cpu

This invokes a separate historical Plunder implementation with experimental momentum and stability mechanisms.

Its output behavior differs from the primary processing modes.

5. Custom Optimization Problems

Custom Ising Problems

To solve a custom Ising problem, prepare a NumPy ".npy" file containing a symmetric interaction matrix.

Example:

import numpy as np

J = np.array([
    [0.0, 1.0, -0.5],
    [1.0, 0.0, 0.25],
    [-0.5, 0.25, 0.0]
])

np.save("custom_J.npy", J)

Run the processor:

py -3.10 full_cppu.py --mode hybrid --ising custom_J.npy --polish --output results\custom_solution.npz

An optional external field vector may be supplied using "--h":

py -3.10 full_cppu.py --mode original --ising custom_J.npy --h custom_h.npy

If omitted, "h" defaults to zero.

6. Adjusting Processor Parameters

CPPU exposes several configuration parameters for experiments.

Parameter| Description
"--steps"| Number of computational iterations or search moves
"--agents"| Original Plunder agent population
"--dt"| Original processor integration timestep
"--D0"| Initial diffusion strength
"--adaptive-gain"| Original diffusion adaptation
"--seed"| Random seed
"--starts"| Independent SAT search attempts
"--profile"| Original Plunder parameter preset
"--device"| CPU, CUDA, or automatic selection
"--polish"| Conventional single-spin local improvement
"--output"| Solution output path

Not every parameter applies to every solver.

Example: Custom Original CPPU Configuration

py -3.10 full_cppu.py --mode original --demo --agents 64 --steps 10000 --dt 0.02 --D0 0.05 --adaptive-gain 0.995 --seed 1234

Researchers can experiment with these values to examine convergence, stability, and optimization quality.

Parameter changes should be evaluated on independent test problems against appropriate conventional controls.

For the complete command reference:

py -3.10 full_cppu.py --help

7. Reading Results

The original, Ising, and hybrid modes save NumPy ".npz" solution files.

For example:

import numpy as np

with np.load("results/hybrid_demo.npz") as data:
    print("Fields:", data.files)
    print("Solution:", data["assignment"])
    print("Energy:", float(data["energy"]))

The SAT solver instead saves:

- "assignment": Boolean variable values
- "clauses": Original clauses
- "violations": Number of unsatisfied clauses

Original and hybrid modes also produce JSON metadata files.

For reproducible research, preserve the input, random seed, configuration, assignment, elapsed time, and hardware information.

8. Research and Benchmarking

The reconstruction includes historical benchmarks covering:

- Sherrington–Kirkpatrick spin glasses
- Ising energy minimization
- MAX-3SAT optimization
- Parameter tuning
- Temporary flip-memory
- Event-driven search
- Comparisons with locally implemented classical algorithms

Results and research documentation are available in:

benchmarks/
research/
historical_archives/

Some external benchmark workflows are included for future D-Wave comparisons. Their presence does not mean those official comparisons have already been executed.

Air-Traffic Research

Historical CPPU-related experiments included nationwide live aircraft-state monitoring and synthetic simulations involving up to 25,000 aircraft.

The recovered reconstruction does not currently contain the original nationwide aviation programs or an executable aviation mode.

Those applications must be restored and independently validated before they can be used through this release.

9. Directory Structure

CPPU/
│
├── full_cppu.py
├── RUN_WINDOWS.bat
├── requirements.txt
├── verify_all.py
│
├── original/
│   └── Original Plunder implementations
│
├── optimized/
│   ├── Phase 7 Ising engine
│   ├── Phase 9 SAT engine
│   ├── Test inputs
│   └── Verification tools
│
├── legacy/
│   └── Earlier CPPU prototypes
│
├── benchmarks/
│   └── Research and test results
│
├── research/
│   └── Operator papers
│
└── historical_archives/
    └── Preserved research archives

The historical archives may be distributed separately because of their size.

10. Troubleshooting

Python is not recognized

Install Python 3.10 and ensure the Windows Python launcher is available.

Missing Python modules

Run:

py -3.10 -m pip install -r requirements.txt

CUDA unavailable

Use:

--device cpu

CUDA acceleration requires compatible NVIDIA hardware and a CUDA-enabled PyTorch installation.

First run is slow

Numba may compile functions during the first execution. For performance comparisons, use warmed-up runs with consistent hardware and timing conditions.

Verification reports missing files

Check whether files referenced by "MANIFEST_SHA256.json" were excluded from the GitHub upload. A reduced repository requires an updated manifest.

SAT input rejected

Confirm that the input uses DIMACS CNF formatting with exactly three distinct variables in every clause.

Ising input rejected

Confirm that the NumPy interaction matrix is symmetric, finite, square, and has a zero diagonal.

11. Important Research Limitations

CPPU is an experimental software research platform.

The original physics-inspired processor, newer classical SAT/Ising optimizers, and hybrid execution modes are distinct implementations.

Performance improvements in a newer classical algorithm do not automatically demonstrate improvements in the original CPPU dynamics.

The complete original 32-operator implementation and some historical application source files remain unrecovered.

CPPU has not established a unique hardware acceleration advantage, quantum advantage, or superiority over leading external solvers.

The software is not certified for operational air-traffic control or other safety-critical applications.

---

Developed by Abiah D. Avila

Computational Physics Processing Unit | Experimental Computational Architecture

The system identified potential conflicts and selected aircraft requiring further attention. It was not an operational air-traffic-control system, and the results do not establish verified collision-free rerouting.

2. Large-Scale Flight Simulation

CPPU-related aviation research also explored high-density simulated airspace.

Recorded stress-test results:

Metric| Result
Simulated aircraft| 25,000
Candidate aircraft pairs evaluated| 9,209,014
Conflict edges identified| 89,296
Aircraft flagged for adjustment| Approximately 17,114
Processing time| 41.8–46.5 seconds

These experiments explored computational scalability, spatial conflict detection, and graph-based conflict selection.

3. Mathematical Optimization

CPPU has been researched using several mathematical optimization problems, including:

- Ising spin systems and spin glasses
- Quadratic Unconstrained Binary Optimization (QUBO)
- Boolean satisfiability and MAX-3SAT
- Continuous-state energy minimization
- Adaptive stochastic optimization
- Event-driven discrete search

Later research introduced improved objective representations, temporary flip-memory, incremental event tracking, and independently verified solution scoring.

Some newer implementations demonstrated measurable performance improvements over earlier versions. These improvements should be distinguished from the original CPPU dynamics, and superiority over leading external solvers has not been established.

4. Artificial Intelligence: Project Frankenstein

CPPU is also being investigated as a computational foundation for Frankenstein, an experimental artificial intelligence architecture.

The research explores integrating physics-inspired dynamics with:

- Neural processing and learned representations
- Adaptive memory and feedback mechanisms
- Internal prediction and world modeling
- Physical-state estimation
- Causal reasoning and experimental verification
- Autonomous scientific discovery

The long-term objective is to develop systems capable of constructing hypotheses, investigating unfamiliar environments, conducting experiments, and independently evaluating evidence.

These capabilities remain active research objectives rather than established general-purpose achievements.

Core Computational Principles

CPPU research investigates several interacting mechanisms:

Stochastic Dynamics: Controlled randomness helps computational states explore possible solutions.

Adaptive Diffusion: Adjustable noise and state evolution support exploration and convergence.

Energy-Based Processing: Candidate solutions are evaluated against mathematical objective functions.

Memory-Driven Search: Previous computational states influence later decisions, potentially reducing repeated or unproductive exploration.

Hybrid Optimization: Continuous dynamics and discrete search algorithms can be combined for different computational tasks.

Physical-State Integration: Structured mathematical representations provide interfaces between simulation, optimization, and machine-learning systems.

Research Philosophy

CPPU investigates a central question:

Can physics-inspired computational architectures provide measurable advantages in efficiency, adaptability, and problem-solving when implemented on conventional digital hardware?

The project emphasizes experimental development, reproducible testing, and mathematical investigation.

Historical demonstrations establish that the software can perform substantial computational tasks. Further controlled benchmarking is required to determine which advantages arise specifically from CPPU's proposed mechanisms rather than established algorithms or hardware capabilities.

Development Status

Status: Active experimental research and development.

This repository preserves recovered CPPU implementations, experimental improvements, benchmark tools, and supporting research.

Certain historical components, including the complete original operator collection and portions of the aviation simulation software, remain subject to recovery and validation.

The project is not a certified aviation safety system, a demonstrated quantum computer, or a verified replacement for specialized computational hardware.

---

Author

Abiah D. Avila
Independent Researcher and Developer
Computational Physics Processing Unit (CPPU)

Exploring the intersection of physical dynamics, computation, optimization, and artificial intelligence.
