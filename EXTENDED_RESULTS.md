# Extended results

This file gives, unchanged, results that the article summarizes and does not tabulate in full: the tuning grids and selected configurations of the classical solvers, the run budgets of the time-to-solution study, the n = 8 hardware study, the hardware-driven ADMM runs, and the mechanism studies of per-instance angle optimization. Every value comes from the result files named below, which the notebook writes; nothing here is new.

## A1. Tuning grids and selected configurations

Source: `results/E36_selected_configs.json`, `results/E36_tuning_*.csv`, `results/H37_sa_tuning_*.csv`, `results/R25_sa_tuning.csv` (article: Appendix G).

Tuning grids and selected configurations. Ties: number of configurations at the minimum held-out mean gap.

| Solver | Grid | Main family (ties) | Planted family (ties) |
|---|---|---|---|
| SA, tuned (small grid) | T0∈{0.5,1,2,5}, T_min∈{0.005,0.02,0.1}, 1/2/4 restarts (36) | T0=1, 0.1, 4 (1) | n/a |
| SA, tuned (extended) | T0∈{0.25,…,5}, T_min∈{0.005,…,1}, 1–16 restarts (125) | 0.25, 0.005, 16 (27) | 0.5, 0.005, 16 (1) |
| SA, tuned (wide grid) | T0∈{0.1,…,1}, T_min∈{0.0005,…,0.02}, 8–128 restarts (100) | n/a | 0.5, 0.005, 16 (1) |
| Tabu, restarts | tenure {1,3,6,9,12}, restart after {6,…,96} steps (25) | 3, 6 (9) | 9, 12 (1) |
| PT | M∈{2,…,32}, T_min∈{0.005,…,0.1}, T_max∈{0.2,…,3} (100) | 8, 0.005, 3 (16) | 16, 0.02, 0.3 (1) |

## A2. Run budgets of the time-to-solution study

Source: `results/E39_tts_budget_choice.csv`, `results/E39_tts_tts.csv` (article: Section VII-F and Appendix G).

Run budget b* selected on held-out instances and, in parentheses, the fraction of test instances on which all 1,024 runs succeeded (so that TTS99=b* is an upper bound).

| Family | Solver | n=12 | 14 | 16 | 18 | 20 |
|---|---|---|---|---|---|---|
| Main | Tabu, restarts | 192 (0.60) | 256 (0.50) | 384 (0.45) | 384 (0.25) | 768 (0.45) |
|  | PT | 512 (0.45) | 512 (0.10) | 768 (0.10) | 1,024 (0.15) | 1,536 (0.20) |
|  | Local search | 384 (0.45) | 512 (0.40) | 1,024 (0.30) | 1,536 (0.25) | 1,536 (0.20) |
|  | SA, tuned | 64 (0.00) | 64 (0.00) | 96 (0.00) | 96 (0.00) | 128 (0.00) |
|  | SDP+LS | 16 (0.00) | 16 (0.00) | 24 (0.00) | 24 (0.00) | 24 (0.00) |
| Planted | Tabu, restarts | 384 (0.00) | 3,072 (0.00) | 8,192 (0.00) | 2,048 (0.00) | 2,048 (0.00) |
|  | PT | 768 (0.00) | 1,536 (0.00) | 3,072 (0.00) | 6,144 (0.00) | 12,288 (0.00) |
|  | Local search | 3,072 (0.00) | 6,144 (0.05) | 512 (0.00) | 12,288 (0.00) | 32,768 (0.10) |
|  | SA, tuned | 48 (0.00) | 128 (0.00) | 96 (0.00) | 256 (0.00) | 256 (0.00) |
|  | SDP+LS | 16 (0.05) | 16 (0.00) | 256 (0.15) | 24 (0.00) | 1,536 (0.40) |

## A3. Hardware validation at n = 8

Source: `results/S22_hardware_counts.json`, `results/S22_hardware_analysis.csv`, `results/S22_hardware_analysis_session1.csv` (article: Section VII-I and Appendix H).

*Validation at n=8.* We executed p=2 circuits for five n=8 instances on `ibm_marrakesh` (4,096 shots each) in two sessions, submitted from the two complete executions of the notebook, each with and without dynamical decoupling (XY4) and gate and measurement twirling (Table A3). Transpiled at optimization level 3 for the device's coupling map and native gates, with the same settings for the submitted circuits and the device-matched simulation, each simulated circuit contains 188 two-qubit gates, against 112 in the unrouted logical circuit. Each hardware distribution is compared with the noiseless distribution at the same angles, a noisy simulation (8,192 shots) of the transpiled circuit with a snapshot noise model of the device (gate, readout, and relaxation errors), a noisy simulation of the unrouted logical circuit, and the uniform distribution. Random-angle circuits use the unnormalized Hamiltonian with angles uniform on [-π/4,π/4]; transferred-angle circuits use the normalized Hamiltonian and the n=12 angles of Table 11 of the article.

With random angles, the per-instance noiseless gap ranges from about 0.35 to 1.65 of the uniform value, but these biases point in arbitrary directions and average out: the means over instances of the noiseless, simulated, and hardware distributions all lie between 0.955 and 1.045 of it, so by Proposition 3(c) of the article agreement of means carries almost no information. On two instances the random angles are anti-aligned with the cost (ḡ_ideal>1); there noise pulls the output toward uniform and improves the gap in all but one hardware run, so a simulation with less noise than the executed circuit appears to underperform the hardware. This mechanism accounts for a spurious hardware-versus-model discrepancy in our initial implementation, which simulated the unrouted circuit.

With transferred angles, the noiseless output is far from uniform, and the comparison is informative. The device-matched simulation retains 90% of the noiseless improvement over uniform sampling, hardware 68% and 73% in the first session and 49% and 57% in the second, and the total variation distance to the simulated distribution rose from 0.16 (noiseless) to 0.24 to 0.42 (hardware). The noise model is therefore substantially optimistic, and the drift between sessions exceeds the agreement that a mean-based validation would report. Hardware output nonetheless stayed well below uniform on every instance in both sessions (0.38 to 0.65), so the transferred-angle state survives execution, although heavily degraded.

Per instance, the first two predictions of Proposition 3 of the article hold (computed from the per-instance mean gaps underlying Table A3). Under the device-matched simulation, the retained fraction η lies between 0.89 and 0.91 on all five transferred-angle instances and between 0.86 and 0.91 on all five random-angle instances, whose average carries no information. On hardware, η for transferred angles ranges from 0.64 to 0.78 in the first session and from 0.42 to 0.75 in the second. For random angles, it ranges from 0.60 to 0.82 in the first session and from 0.39 to 0.63 in the second on the two instances with  |1 − ḡ_ideal|>0.6, close to the transferred-angle values of the same session, and scatters from 0.23 to 0.92 and from 0.03 to 1.06, respectively, on the three instances with  |1 − ḡ_ideal|<0.2, where Proposition 3(b) of the article predicts an ill-conditioned estimate.

Table A3. Hardware (n=8, p=2, five instances). Mean expected gap ḡ relative to the uniform distribution (0: optimal; 1: uniform) and total variation distance (TVD) to the device-matched simulation. S1, S2: two hardware sessions; DD + twirling: dynamical decoupling (XY4) with gate and measurement twirling.

| Condition | Mean gap / uniform, random | Mean gap / uniform, transferred | TVD to simulation, random | TVD to simulation, transferred |
|---|---|---|---|---|
| Noiseless | 0.970 | 0.204 | 0.125 | 0.162 |
| Device simulation | 0.969 | 0.285 | 0 | 0 |
| Logical-circuit simulation | 0.968 | 0.236 | 0.105 | 0.106 |
| Hardware S1, DD + twirling | 0.971 | 0.462 | 0.181 | 0.244 |
| Hardware S1, none | 0.955 | 0.420 | 0.215 | 0.251 |
| Hardware S2, DD + twirling | 0.975 | 0.606 | 0.272 | 0.420 |
| Hardware S2, none | 1.045 | 0.547 | 0.303 | 0.415 |
| Uniform | 1.000 | 1.000 | 0.398 | 0.687 |

## A4. Hardware-driven ADMM

Source: `results/R29_hw_admm_*.csv`, `results/E42_hw_admm_*.csv`, `results/E42_references_*.csv`, `results/R29_hw_admm_references_*.csv` (article: Appendix H).

*Hardware-driven ADMM.* In the first study, three main-family instances were run for six ADMM iterations with the binary step on `ibm_marrakesh` (768 shots per iteration, best sample, XY4 dynamical decoupling). Hardware solved 17 of the 18 subproblems and reached the same best feasible solutions as noiseless sampling, but the reference trajectories never changed their bitstring and the hardware trajectory changed only at its one missed subproblem, so the hardware mostly had to resample one subproblem optimum. The second study therefore selected, in noiseless simulation and before submission, the first three planted instances whose transferred-angle ADMM trajectory changes its bitstring at least twice in six iterations (instances 0, 34, and 42), and ran them with the binary step on hardware (768 shots per iteration, no suppression, job mode). The hardware changed the bitstring two to four times per instance (noiseless sampling: two or three) but solved only 5 of its 18 subproblems exactly, against 15 for noiseless sampling and 10 for the device-matched simulation. It found the problem optimum on one of the three instances (best feasible gap 0.017 and 0.044 on the others), whereas noiseless sampling, the device-matched simulation, and the exact solver found it on all three. With three instances this is an illustration rather than a measurement: when the ADMM trajectory depends on the subproblem solutions, hardware errors can change the ADMM outcome.

## A5. Mechanism studies of per-instance angle optimization

Source: `results/S12_mechanism_*`, `results/S13_ablation_*`, `results/S21_alpha_selection_*`, `results/S21_landscape_*`, `results/S21_warmstart_maxcut_*`; figure `results/figures/fig03_mechanism_ablation.pdf` (article: Appendix D, which summarizes these results). All optimized-QAOA arms here were drawn by the circuit sampler (article: Section V-C and Appendix C).

*Variants.* Section V-B of the article lists the variants, which include depths p∈{1,2,3}. SPSA uses standard gain sequences (a=0.2, c=0.1, stability constant equal to one tenth of the evaluation count, exponents 0.602 and 0.101); a second COBYLA configuration uses up to 47 evaluations of 16 shots and a 16-shot read-out; and multi-start runs four restarts of 192 evaluations each (11 COBYLA evaluations of 16 shots and a 16-shot read-out) and pools their read-outs (R=64). The budget control runs single-start QAOA with one restart's budget.

Depth had no detectable effect (Friedman χ²₂=0.025, P=0.99), but because the shared stream changes the noise that the optimizer sees, this null result and the optimizer comparison below are inconclusive, and we draw no conclusion from them. Of three optimizers in five configurations on the standalone subproblem (Fig. A5b), the three COBYLA configurations are best (g_unif between 0.032 and 0.048) and the only ones ahead of random search (g_unif=0.064; r between -0.480 and -0.311, uncorrected P≤0.027); Nelder–Mead is worse than random search (g_unif=0.128, r=+0.473, P=3.4×10^-4) and SPSA indistinguishable from it (P=0.84). The 16-shot COBYLA configuration terminated early, using on average 626 of its 768 evaluations before the top-up. Coefficient normalization, which transferred angles require, raises the gap of optimized QAOA in the embedded comparison (Table 10 of the article), plausibly because it rescales the landscape region that the fixed initial range samples; on the standalone subproblem the difference is smaller (0.038 versus 0.032). Neither difference was formally tested.

A decomposition experiment (Fig. A5a, 80 instances) separates two explanations for the benefit of multi-start. At an equal read-out size of 32 or 64 shots, multi-start with one read-out from its best restart still beats single-start (r=-0.328, P=0.014; r=-0.363, P=0.010), so restart diversity contributes. Doubling the single-start read-out from 32 to 64 shots also helps, with a smaller mean effect but far greater consistency (g_unif reduced by 0.021 against 0.032 for restart diversity; r=-0.972, P=1.8×10^-8, 31 non-tied pairs), as Proposition 1(b) of the article predicts when B/R is halved; pooling four read-outs instead of reading one state gives no benefit (P=0.92). Under best-sample scoring, multi-start beats single-start modestly (r=-0.384, P=0.018, 49 non-tied pairs). Of multi-start's read-out-scored advantage over single-start (0.053 in g_unif), about 40% thus comes from the larger final sample rather than from restart diversity. This share should be read as indicative: with the circuit sampler, a 32-shot read-out repeats the optimizer evaluation at the returned angles, whereas a 64-shot read-out adds 32 new shots. The equal-read-out comparison is affected too: multi-start's best-restart read-out is a fresh call, whereas single-start's repeats the evaluation that COBYLA selected. This favors single-start, so the benefit attributed to restart diversity is, if anything, understated.

Landscape mitigations did not help (standalone subproblem, 80 instances; circuit-sampled null results, subject to the caveat above). CVaR aggregation at the pre-specified level α=0.1, also the level selected on 30 held-out instances, improved neither single-start QAOA under best-sample scoring (r=+0.086, P=0.62, 46 non-tied pairs) nor multi-start on matched subproblems (r=+0.099, P=0.58, 43 non-tied pairs). It improved the read-out-scored gap (0.105 to 0.071), as expected of an objective that concentrates the final state on the best outcomes, but the improvement disappears once all samples are credited, a further instance of the scoring effect. Linear-ramp initialization (P=0.32) and its combination with CVaR (P=0.44) were also indistinguishable from the baseline. On MaxCut (80 graphs), a variant of warm-started QAOA (Egger et al., cited in the article) whose initial state is biased by a spectral relaxation instead of the quadratic-programming or SDP relaxation used there was indistinguishable from standard QAOA (P=0.73, 21 non-tied pairs) and worse than GW (r=+1.000, P=1.2×10^-4, 14 non-tied pairs). Transferred angles beat the standalone baseline on every non-tied instance (r=-1.000, P<10^-10, 40 non-tied pairs).

Fig. A5 (`fig03_mechanism_ablation.pdf`). Why optimized QAOA falls short (standalone n=12 subproblem, 80 instances). (a) Decomposition of the multi-start benefit: single-start (SS) and multi-start (MS) at equal read-out sizes, under best-sample scoring (filled) and read-out scoring (open). (b) Classical optimizers for angle optimization, with transferred angles, SA, and random search for reference.
