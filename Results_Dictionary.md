# Results Dictionary

Documentation for the result files in `results/` and `figures_data/`.

## Experiments at a glance

| File | Experiment | Settings |
|---|---|---|
| `Saltville_Exact.csv` | Production run, Saltville instances 1–20 | σ = 105 ft, ε = 0 (exact flight-path set), 1,800 s limit. **All 20 proven optimal.** |
| `Suffolk_Heuristic_1800.csv` | Production run, Suffolk instances 21–40 | σ = 105 ft, ε = 0.05 min curtailment, uniform 1,800 s limit. By Lemma 1, solved objectives lie within \|C\|·ε of the exact optimum. |
| `Suffolk_Mopup_3600.csv` | Extended-limit re-solves of instances 21–24, 26, 28 | Identical model and settings, 3,600 s limit. **No incumbent changed**; instances 21, 22, and 28 closed to proven optimality (23, 24 re-verified). Instance 25 was likewise re-solved to proven optimality at its unchanged incumbent (38.8525 min, 991 s); its detailed metrics row was not retained. |
| `VehicleOnly_Saltville.csv`, `VehicleOnly_Suffolk.csv` | Vehicle-only (VDS) baselines, all 40 instances | No drone; manual service per customer. All proven optimal. |
| `Sensitivity_Final.csv` | Calibration ladders on instances {1, 6, 11, 16} | σ-arm: σ ∈ {52.5, 105, 210, 420} ft at ε = 0. ε-arm: ε ∈ {0.025, 0.05, 0.1, 0.2} min at σ = 105 ft. All solved to proven optimality. |
| `ctdrsp_matched.csv` | Literature benchmark on Saltville instance 1 | Flexible-site truck-and-drone model configured to matched speeds, endurance, and service accounting; 3,600 s limit. |

Solver: Gurobi 12.0.3 via AMPL; Intel Xeon Silver 4216, 64 GB RAM.

## Drone result files (`Saltville_Exact`, `Suffolk_Heuristic_1800`, `Suffolk_Mopup_3600`)

| Column | Units | Meaning |
|---|---|---|
| `instance` | — | Instance id (Saltville 1–20; Suffolk 21–40) |
| `objective` | min | Route completion time of the best solution found (vehicle and drone returned to the depot, all customers served) |
| `solve_time_sec` | s | Wall-clock solver time as recorded. Values slightly above the limit (e.g., 1,804) reflect solver overhead; the manuscript caps reported CPU at the limit |
| `bnb_nodes` | — | Branch-and-bound nodes explored |
| `vehicle_duration_min` | min | Vehicle's total driving time (primary + secondary roads) |
| `primary_road_duration_min`, `secondary_road_duration_min` | min | Driving-time decomposition by road class |
| `vehicle_idle_time_min` | min | Time the vehicle waits at rendezvous points for drone flights |
| `total_drone_airtime_min` | min | Sum of drone flight time (incl. service components) across sorties |
| `vehicle_distance_ft`, `primary_road_distance_ft`, `secondary_road_distance_ft` | ft | Public-road mileage and its decomposition |
| `private_road_distance_ft` | ft | Always 0 by design: driveway navigation is charged to service time (see Data Dictionary), so mileage columns count public roads only |
| `num_drone_launch_arcs` | — | Distinct arcs from which at least one selected flight launches |
| `num_customers` | — | \|K\| for the instance (matches the `*_Customers.dat` sets) |
| `num_total_flights_generated` | — | Feasible flight paths enumerated before curtailment |
| `num_active_flights` | — | Flight paths retained after ε-dominance curtailment (equals generated when ε = 0) |
| `flight_reduction_pct` | % | 100 × (generated − retained) / generated |
| `final_gap_pct` | % | Terminal optimality gap. 0.00 (below the 10⁻⁴ solver tolerance) = proven optimal |
| `final_best_bound` | min | Terminal best bound (lower bound on the instance's optimum under the model solved) |

**Solved criterion used in the manuscript:** an instance counts as solved when
`final_gap_pct` < 0.01 (the 10⁻⁴ relative tolerance).

## Vehicle-only files

Same identification and solve columns (`instance`, `num_customers`,
`objective`, `solve_time_sec`, `bnb_nodes`, `gap_pct`, `solve_result`), the
driving-time and mileage decompositions as above, plus:

| Column | Units | Meaning |
|---|---|---|
| `total_service_min` | min | Sum of `customerVehicleServiceTime[k]` over served customers (includes all driveway navigation) |

There are no idling, drone, or flight columns; `objective` =
`vehicle_duration_min` + `total_service_min`.

## `Sensitivity_Final.csv`

| Column | Meaning |
|---|---|
| `arm` | `sigma` (step-size ladder) or `eps` (tolerance ladder) |
| `sigma`, `eps` | The configuration solved (the non-varied parameter is at its production value) |
| `instance`, `customers` | Representative instance (first of each Saltville group) and its \|K\| |
| `objective`, `best_bound`, `gap_pct`, `solve_time_sec`, `solve_result` | As above; every row solved to proven optimality |
| `flights_generated`, `flights_retained` | Flight-path counts before/after curtailment at this configuration |
| `cliques` | Incompatibility cliques in the model at this configuration |

Percent changes reported in the manuscript's calibration table are relative to
the smallest-objective configurations: σ = 52.5 ft for the σ-arm and ε = 0 for
the ε-arm.

## Reading the headline results

- All comparisons use identical customer sets, speeds, and service accounting
  between VDS and DDS; objectives are identical across the benchmark files,
  the extended re-solves, and the manuscript's tables and figures.
