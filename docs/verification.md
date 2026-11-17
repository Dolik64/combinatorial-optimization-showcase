# Verification and provenance

[Project](../README.md) · [Model](model.md) · [Walkthrough](demo.md)

## Source and attribution

The presentation is based on Jiří Doležal's public [KO_cvut repository](https://github.com/Dolik64/KO_cvut), pinned to commit `bf26f99a31e2519d7921f9ec5bc57c29cb463e5a`. The inspected solver is [`python/HW1/main.py`](https://github.com/Dolik64/KO_cvut/blob/bf26f99a31e2519d7921f9ec5bc57c29cb463e5a/python/HW1/main.py). The [course assignment](https://github.com/Dolik64/KO_cvut/blob/bf26f99a31e2519d7921f9ec5bc57c29cb463e5a/python/HW1/2026-hw1.pdf) defines the problem and provides the main example, whose stated weekly objective is 232.

The course supplies the problem formulation, example data and teaching materials. This showcase presents the solution stored in the repository, not a claim to have invented the scheduling problem or ILP method. The plots and validation data were prepared for this presentation on **17 November 2026**.

## Fresh execution

The original `main.py` was downloaded from the pinned commit and executed without model changes, in a local Python environment with **Gurobi 14.0.0**. Each input was run in a separate subprocess. A separate verifier reconstructed coverage from the output decisions rather than relying on saved historical result files.

| Input | D | Objective | Final bound | Recomputed output checks |
| --- | ---: | ---: | ---: | --- |
| Stored input 1: zero demand | 0 | 0 | 0 | Passed |
| Stored input 2: flat weekday/weekend demand | 0 | 0 | 0 | Passed |
| Stored input 3: demand around midnight | 0 | 0 | 0 | Passed |
| Stored input 4: alternating hourly demand | 0 | 60 | 60 | Passed |
| Main weekly example | 0 | 430 | 430 | Passed |
| Main weekly example | 1 | 312 | 312 | Passed |
| Main weekly example | 2 | **232** | **232** | Passed |
| Main weekly example | 4 | 170 | 170 | Passed |

All final reported gaps were 0.0000%. Validation checks that the output has 168 nonnegative integer shift starts; cyclic coverage is recomputed for all 168 hours; shortage never exceeds D; and the sum of absolute hourly errors equals the output's objective. Equality of the final solver objective and bound supports the optimality claim for these inputs. Output validation by itself only establishes feasibility and the objective value.

The charted example is a fresh run. Its shift vector need not match the illustrative vector in the assignment because multiple schedules can have the same optimal objective. The eight runs do not establish correctness on every possible input or any official coursework grade. Runtime figures are deliberately not presented as a benchmark.

## Reproduce the main result

1. Obtain the original `python/HW1/main.py` from the pinned source link above.
2. Use a Python environment with `gurobipy` installed and a Gurobi licence suitable for the model.
3. Place that script alongside the showcase's `demo` folder and run:

```sh
python main.py demo/input.txt reproduced-solution.txt
```

The first line should be **232**, followed by 168 integer shift-start values on the second line. The model has 336 integer variables and 672 explicit linear constraints. The output vector can differ while remaining optimal.

## Published evidence

- [Input](../demo/input.txt) and [fresh solution](../demo/solution.txt).
- [Full hourly schedule](../demo/hourly-schedule.csv) and [sensitivity results](../demo/sensitivity.csv).
- [Machine-readable verification record](../demo/verification.json), including the source commit, source checksum, solver version and per-run objective/bound.

The three images were generated with Matplotlib from these computed schedules. Full course PDFs, notebooks, raw solver logs and local environment details are not copied into this showcase. The original source and teaching materials remain in the linked repository.
