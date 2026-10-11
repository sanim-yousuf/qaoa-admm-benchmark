# When Does QAOA Help Inside ADMM? — code, data, and hardware records

Companion repository for S. Y. Fahim and M. A. Hossain, "When Does QAOA Help Inside ADMM? A Matched-Budget Benchmark of Scoring Rules, Angle Transfer, and Hardware Noise," submitted to *IEEE Transactions on Quantum Engineering*.

Every number, table, and figure in the manuscript and its supplementary material comes from the result files in `results/`. The experiments were computed by successive versions of the notebook on Kaggle, the last of them by V10 (2 October 2026). The final execution, `notebook/quantum-experiment-V11.ipynb`, reloaded every cached result, reproduced every printed table and test of V10 unchanged, and added Section 50, which computes the revision analyses (files `RV1_*` to `RV5_*`) without new experiments or hardware jobs: from the cached results, except that the circuit-sampler check re-runs the first subproblem of five instances and the device-matched simulation of the first n = 12 submission is re-executed.

## Contents

| Path | Contents |
|---|---|
| `notebook/quantum-experiment-V11.ipynb` | Final notebook, with the outputs of the execution the manuscript reports (earlier sections: experiments, reloaded from the cached results; Section 50: revision analyses) |
| `results/` | All cached per-instance results (CSV/JSON), the test log (`S25_test_registry.csv`, 417 tests), tuned configurations (`E36_selected_configs.json`), timings, and the revision analyses (`RV1_*` to `RV5_*`) |
| `results/` (hardware) | Raw counts, circuits (QASM), job identifiers, calibration time, and readout calibration for every IBM Quantum job: `S22_hardware_counts.json` (n = 8), `R29_hardware_n12.json` (first n = 12 submission), `E41_hardware.json` (second n = 12 submission), `R29_hw_admm_*` and `E42_hw_admm_*` (hardware-driven ADMM) |
| `results/manuscript_figures/` | The figure files used in the manuscript (`fahim*.pdf`) |
| `PROVENANCE_MANIFEST.csv` | For every result table (CSV/JSON; the `__keys.json` cache indexes are not listed): the notebook version that first computed it and the SHA-256 checksum of the released file |
| `provenance/` | Executed notebooks of the earlier versions (V6.0 to V10) that computed the cached results |
| `requirements.txt` | Package versions |

## How the results were produced

The notebook caches every per-instance result. When a later execution is given the earlier outputs (`RESUME = True`), it reloads them unchanged and runs only the experiments that are missing. The experiments therefore accumulated over successive versions of the notebook:

| Version (date) | Results first computed in this version |
|---|---|
| V6.0 and V6.1 (29 Sep 2026) | Main comparison, transfer, mechanism and ablation, size, budget, noise, MaxCut, density, SA sensitivity, Hamiltonian defect, landscape mitigations (S10–S21); n = 8 hardware (S22). Both are complete executions from scratch; their simulated results agree bit for bit (wall-clock timings aside). The transfer from n = 6 and 8 (S10b) was added in V6.1 and computed only there. |
| V7.0 (30 Sep 2026) | Stronger baselines, fixed budget, penalty sweep, general family, first n = 12 hardware submission, cost (R25–R30) |
| V8 (1 Oct 2026) | Planted family, landscape diagnostics, direct QUBO, shared trajectory, first time-to-solution study (H31–H37) |
| V9 (1 Oct 2026) | Angles retrained at n = 16, second n = 12 hardware submission, hardware-driven ADMM (E38, E41, E42) |
| V10 (2 Oct 2026) | Tuning on extended grids, stronger heuristics, time to solution with held-out budgets and timings, hardware uncertainty analysis, general family with planted couplings, test log (E36–E43, S25) |
| V11 (Oct 2026) | No new experiments. Section 50: revision analyses computed from the cached results (RV1–RV5) |

Every later version reloads the cached files; the final execution (V11) reproduced every printed table and test of V10 unchanged, including the shot-sampled n = 8 noisy simulation, which is recomputed at each execution. Code changes made after a result was cached only added options whose defaults leave that result unchanged. Every version pins `qiskit==2.5.2`, `qiskit-aer==0.17.2`, and `qiskit-ibm-runtime==0.49.0`. NumPy, SciPy, CVXPY, and SCS are the versions of the Kaggle Python image at the time of each execution; they were not recorded. The experiments have not been executed on a second machine.

## Known limitation: the circuit sampler's shared random stream

In `run_qaoa` with `sim="qiskit"`, one Qiskit Aer `SamplerV2(seed=...)` is created per subproblem and called once per optimizer evaluation, for the read-out, and for the top-up. The Aer sampler restarts its seeded stream at every call, so these calls reuse the same random numbers: identical angles return identical shots, and the read-out at the returned angles repeats the optimizer evaluation there. This affects optimized QAOA in the experiments drawn with the circuit sampler (S10–S21; Supplementary Table S1, column "Sampler"). Transferred-angle QAOA (one call per subproblem), every experiment with `sim="fast"` (R25–R28, H31–H37, E36–E43), and all hardware runs are unaffected. The penalty sweep (R27) repeats the main optimized-QAOA arm on the same instances with independent draws; Section 50.1 of the notebook compares the two (`RV1_scoring_replicate.csv`; Supplementary Section S4). A fix is to pass a fresh seed to every call, for example `seeded_sampler(seed * 1000 + call_index)`, or to use `sim="fast"`.

## Analyses added in revision (notebook Section 50)

Section 50 reuses the notebook's own functions and the cached results; it runs no new experiments and submits no hardware jobs. Only 50.3 (device-matched simulation) and 50.4 (first subproblem of five instances) re-execute recorded computations.

| Subsection | Output | Contents |
|---|---|---|
| 50.1 | `RV1_scoring_replicate.csv` | Scoring rules with the circuit sampler and with independent draws, at every penalty and at n = 16 and 20 (Supplementary Table S3) |
| 50.2 | `RV2_planted_complement_*.csv` | Planted family re-scored with criteria that credit near-degenerate competitors (Supplementary Table S6) |
| 50.3 | `RV3_hw_threshold_retention.csv` | Threshold- and Hamming-resolved hardware retention from the recorded counts, and the re-executed device-matched simulation of the first n = 12 submission, which reproduces its recorded retention exactly (Supplementary Tables S12 and S13; set `RV_DEVICE_SIM = False` to skip the re-execution, about one hour) |
| 50.4 | `RV4_readout_replay.csv` | Check of the circuit sampler's shared random stream |
| 50.5 | `RV5_angle_symmetry.csv` | Distance between the planted-family angle sets up to the symmetries of the QAOA output |
| 50.6 | printed | Time-to-solution ratios and checks quoted in the text |

## Reproducing the results

**1. Inspect.** Open `notebook/quantum-experiment-V11.ipynb`. Every printed table and test is the output of the reported execution.

**2. Re-run from the released results (minutes).** Attach `results/` as the notebook's input, or place it in the working directory, and keep `RESUME = True`. The notebook reloads every result and regenerates all tables, statistical tests, the test log, the figures, and the revision analyses of Section 50.

**3. Regenerate the simulations (many hours).** Set `RESUME = False` and supply only the hardware files listed above. Hardware jobs cannot be re-executed; the notebook always reuses the recorded counts, and it submits jobs only if they are missing and an IBM Quantum token is present. Quantities whose floating-point path depends on the machine, such as the SCS solution of the SDP relaxation and the SDP-based solver outputs that depend on it, and the wall-clock timings may differ from the released files; we have not measured these differences on a second machine.

No IBM Quantum token is needed for options 1–3. If you do submit hardware jobs, provide the token through the `IBM_QUANTUM_TOKEN` environment variable or a Kaggle secret; never write it into the notebook.

## License

Code: MIT. Data and hardware records: CC BY 4.0.

## Citation

If you use this benchmark, please cite the article (citation to be added on publication).
