# Rural Delivery by Vehicle-Ported Drone

## Problem Description
Vehicle routing with drone-assisted delivery for rural last-mile logistics. A delivery vehicle collaborates with a drone to serve customers in rural areas. The drone can launch from and be retrieved at discretized points along the vehicle's route on secondary roads, enabling parallel service while the vehicle continues its route.

> **Revision notice (September 2026).** The results in this repository accompany
> the first revision of the manuscript and **supersede all previously posted
> results.** 

## Instances

### Saltville, Virginia
- **58 customers** (selected subsets in each instance)
- **158 road network nodes**
- Network includes primary roads, secondary roads (drone-eligible), and private roads (customer access)
- Instances **1–20**, in four groups of five with increasing size (8–52 customers)

### Suffolk, Virginia
- **104 customers** (selected subsets in each instance)
- **270 road network nodes**
- Larger, more dispersed network with greater route complexity
- Instances **21–40**, in four groups of five with increasing size (24–101 customers)

## Model Parameters

### Drone Parameters
- **Flight time limit**: 30 minutes (total endurance including service)
- **Drone speed**: 5,720 ft/min (≈65 mph)
- **Service time**: 0.666 minutes total
  - Takeoff: 0.083 min
  - Customer service: 0.5 min
  - Landing: 0.083 min

### Vehicle Parameters
- **Primary road speed**: 3,080 ft/min (≈35 mph)
- **Secondary road speed**: 2,200 ft/min (≈25 mph)
- **Private road speed**: 880 ft/min (≈10 mph)
- **Service time per customer**: Depends on driveway length (see
  `figures_data/Service_Times_PerCustomer.csv` for all 162 per-customer values).
  Driveway navigation is charged to service time in both delivery systems, so
  reported vehicle mileage is public-road mileage.

### Algorithm Parameters
- **Discretization step**: 105 feet (spacing between candidate launch/retrieval points)
- **Curtailment tolerance**: ε = 0.05 minutes for Suffolk, ε = 0 (exact) for
  Saltville; the ε-dominance rule carries a proven optimality-loss bound of |C|ε
- **Time limit**: uniform 1,800 seconds per instance. Suffolk instances 21--26 and 28 were subsequently re-solved at
  3,600 seconds: no incumbent changed, and instances 21, 22, 25, and 28 closed
  to proven optimality (`results/Suffolk_Mopup_3600.csv`)

## Computational Environment
- **Solver**: Gurobi 12.0.3
- **Modeling language**: AMPL
- **Hardware**: Intel(R) Xeon(R) Silver 4216 CPU @ 2.10GHz and 64GB RAM

## Results Files

| File | Contents |
|---|---|
| `results/Saltville_Exact.csv` | 20 Saltville instances, all solved to proven optimality (ε = 0) |
| `results/Suffolk_Heuristic_1800.csv` | 20 Suffolk instances under the ε = 0.05 curtailment, uniform 1,800 s benchmark |
| `results/Suffolk_Mopup_3600.csv` | Extended-limit re-solves (instances 21--24, 26, 28); incumbents unchanged, gaps closed. Instance 25 was likewise re-solved to proven optimality at its unchanged incumbent (38.8525, 991 s); its detailed metrics row was not retained |
| `results/VehicleOnly_Saltville.csv`, `results/VehicleOnly_Suffolk.csv` | Vehicle-only (VDS) baselines, all proven optimal |
| `results/Sensitivity_Final.csv` | Step-size arm (σ ∈ {52.5, 105, 210, 420} ft) and tolerance arm (ε ∈ {0.025, 0.05, 0.1, 0.2} min), all solved to proven optimality |
| `results/ctdrsp_matched.csv` | Flexible-site truck-and-drone benchmark on Saltville instance 1 |
| `figures_data/Excel_Data_Final.csv` | Tidy per-instance dataset joining VDS and DDS results with derived time and mileage changes |
| `figures_data/Service_Times_PerCustomer.csv` | Per-customer manual service times (both AOIs) against the drone's fixed 40-second service time |

### Column Reference (drone result files)
`instance, objective (min), solve_time_sec, bnb_nodes, vehicle_duration_min,
primary_road_duration_min, secondary_road_duration_min, vehicle_idle_time_min,
total_drone_airtime_min, vehicle_distance_ft, primary_road_distance_ft,
secondary_road_distance_ft, private_road_distance_ft, num_drone_launch_arcs,
num_customers, num_total_flights_generated, num_active_flights,
flight_reduction_pct, final_gap_pct, final_best_bound`

Vehicle-only files report the analogous subset (no drone or idling columns, plus
`total_service_min` and `gap_pct`). The sensitivity file adds `arm, sigma, eps,
customers, flights_generated, flights_retained, cliques`.

### Interpreting Results
- **Drone** files show results when the vehicle collaborates with a drone (DDS)
- **VehicleOnly** files show baseline results where only the vehicle serves customers (VDS)
- Objectives are route completion times in minutes; `final_gap_pct` is the
  terminal optimality gap (0.00 = proven optimal within the 10⁻⁴ solver tolerance)

## Instance Data
`data/` provides the road networks, customer coordinates, per-customer service
times, and the customer subsets defining each instance — sufficient to recreate
every instance in the study.

> **Coordinate units.** Customer and node coordinates are geographic
> (latitude/longitude, decimal degrees). All distances and drone flight times
> in the study were computed after projecting coordinates to planar feet;
> computing Euclidean distances directly on the raw degree values will not
> reproduce the reported results.

## Citing
If you use these instances or results, please cite the manuscript
(*Rural Delivery by Vehicle-Ported Drone*, revised manuscript under review at
Omega) and this repository (see `CITATION` note in the paper's reference list).
