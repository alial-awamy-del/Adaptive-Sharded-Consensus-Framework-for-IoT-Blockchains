> **Latest source selection:** See [LATEST_SOURCE_GUIDE.md](LATEST_SOURCE_GUIDE.md) for the newest included code variants, verification boundaries and unresolved audits.
Auditable IoT Blockchain Simulation Experiments
Research code repository for a simulated heterogeneous IoT blockchain architecture involving reputation-driven validator selection, adaptive queueing, sharding, modeled energy, attack scenarios and synthetic smart-city workloads.
> **Evidence status:** This repository assembles the available research-code versions. It is **not** evidence that all published manuscript results have been exactly reproduced or independently validated. The 15-million-device smart-city configuration was reconstructed from manuscript-described settings rather than recovered from the original historical implementation. Never adjust model inputs solely to force agreement with a reported number.
Repository map
Location	Experiment / purpose	Scope
`experiments/integrated/`	Integrated 80K TPS node scaling, service capacity, queue and EWMA reviewer audits	500–2,000 nodes, 10 shards, 10 runs per size
`experiments/smart_city/`	Unified synthetic 2.8M and 15M city scenarios	24 h, workload profiles, peak periods, attack windows
`experiments/inter_shard/`	Inter-shard V4	Independent aggregate fluid service model, not packet-level 2PC
`experiments/queue_scheduling/`	Queue V3	Synthetic microbatch strict priority / FIFO / WFQ-inspired comparison
`experiments/validator_sensitivity/`	Sensitivity V3	Independent trust and validator-weight study
`experiments/energy/`	Consensus-energy V3	Modeled energy and assumed workloads; not protocol implementations
`experiments/markov/`	Markov Option B	Prototype and comparison runner
`experiments/consensus_comparison/`	Consensus comparison	Independent simulation
`legacy/`	Older inter-shard versions	For provenance; do not combine their metrics with V4
Requirements
Python 3.10+ (individual scripts may depend on newer Python/NumPy features). On Google Colab:
```python
!git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
%cd YOUR-REPOSITORY
!pip install -r requirements.txt
```
Replace placeholders with the actual repository details after you create it. You can also upload the ZIP rather than cloning.
Main command: unified Smart City, both scenarios
```bash
python experiments/smart_city/smart_city_both_scenarios.py \
  --scenario both --runs 10 --hours 24 \
  --out outputs/smart_city_dual --resume
```
This requests 80 independent runs: 2.8M devices with seven scenarios (no attack and six attack configurations), and 15M devices with one mixed-attack configuration, ten seeds each. A time step of ten simulated seconds implies 8,640 steps per 24-hour run. The model samples device activity in aggregate; it does not instantiate 15 million Python objects. Full execution can be time-consuming.
Accounting warning: An earlier 2.8M / SYBIL / seed=1213 run reported `queue accounting gap=13`. Treat a nonzero accounting gap as an audit issue. Preserve raw logs; diagnose and correct the cause before labeling outputs publication-ready. An exact reproduction of manuscript values is not guaranteed.
Main command: integrated reviewer audit
```bash
cd experiments/integrated
python run_reviewer_audit_v3.py \
  --nodes 500 700 1000 1500 2000 --runs 10 \
  --duration 30 --epoch 0.05 --offered-tps 80000 \
  --shards 10 --seed-base 20261108 \
  --out ../../outputs/reviewer_audit --resume --audit-shard-epochs
cd ../..
```
Then, if needed:
```bash
python experiments/integrated/reviewer_report.py --out outputs/reviewer_audit
```
Note: the 80K TPS setting refers to the baseline workload. DDoS traffic can increase realized arrivals. The source engine supports four modeled attack categories (Sybil, DDoS, Eclipse, Collusion). The other attack categories formerly listed in the manuscript cannot be inferred from this engine.
Additional experiments
```bash
python experiments/inter_shard/inter_shard_v4_CORRECTED.py --help
python experiments/queue_scheduling/queue_scheduling_v3.py --help
python experiments/validator_sensitivity/validator_weight_sensitivity_v3.py --help
python experiments/energy/consensus_energy_v3.py --help
python experiments/smart_city/smart_city_both_scenarios.py --help
```
See experiment-specific `README_AR.md` documents for their CLI arguments and interpretation. For Colab, use the supplied notebooks in each experiment folder or run commands with `!python`.
Data, results and manuscript comparison
This ZIP contains source and documentation, not the large result archives or manuscript documents. Generated files are written to user-specified `--out` directories and are ignored by git. Review any results before releasing them. Where scripts create `per_run.csv`, configuration metadata, summary tables, or provenance audits, retain these alongside the manuscript revision. Do not present reference manuscript numbers as fresh measured values.
Reproducibility and scientific limitations
Service capacities, power coefficients, and attack outcomes are simulation assumptions. They have not been demonstrated as hardware measurements.
The main integrated model distinguishes nominal per-validator service capacity (tx/s) from synthetic scheduling delay (ms). Do not equate the two unless their relationship is explicitly established and measured.
The smart-city model's energy boundary primarily covers modeled validator operation and per-transaction device energy. It does not integrate idle power for all 2.8M or 15M physical devices.
`100 μWh/tx` is the stated energy target, while approximately `8,081 μWh/tx` for the 2.8M smart-city scenario is about 80.8 times higher, under a different boundary. This difference must be disclosed, not suppressed.
The smart-city M/M/c prototype clips utilization, and certain overloaded-delay values use proxies. Consult audit files before using stable queueing claims.
EWMA-based epoch attack-window detection must not be represented as verified malicious-validator identification.
Independent component simulations must not be pooled with integrated baseline runs as if they shared the same mechanism or energy scope.
The two original experiment sources for manuscripts' Tables 10 and 14 and the 15M historical code are not established by this repository; follow-up verification is required.
No license has been selected for this repository. You should add an appropriate `LICENSE` after deciding on permissions for reuse; public visibility alone does not grant reuse rights.
Publishing on GitHub
```bash
git init
git add .
git commit -m "Add audited simulation code and documentation"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git push -u origin main
```
To support the manuscript's data/code availability statement, create a tagged release, archive the exact version (e.g., via Zenodo), record its DOI, and document its code hash, settings, seeds, and input/output schemas. Do not claim an archive DOI exists until it is issued.
Source status
The original uploaded source, adapted wrappers, and components are preserved in their respective folders. Audit files are not a substitute for empirical calibration or experimental verification. In particular, public release does not, by itself, close reviewers' concerns about unsupported experiments, unresolved workload definitions, or mismatched table values.
