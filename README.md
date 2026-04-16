# Rural Delivery by Vehicle-Ported Drone

## Problem Description
Vehicle routing with drone-assisted delivery for rural last-mile logistics. A delivery vehicle collaborates with a drone to serve customers in rural areas. The drone can launch from and be retrieved at discretized points along the vehicle's route on secondary roads, enabling parallel service while the vehicle continues its route.

## Instances

### Saltville, Virginia
- **58 customers** (selected subsets in each instance)
- **158 road network nodes**
- Network includes primary roads, secondary roads (drone-eligible), and private roads (customer access)

### Suffolk, Virginia  
- **104 customers** (selected subsets in each instance)
- **270 road network nodes**
- Larger, more dispersed network with greater route complexity

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
- **Service time per customer**: Depends on driveway length

### Algorithm Parameters
- **Discretization step**: 84 feet (spacing between potential launch/retrieval points)

## Computational Environment
- **Solver**: Gurobi 12.0.3
- **Modeling language**: AMPL
- **Hardware**: Intel(R) Xeon(R) Silver 4216 CPU @ 2.10GHz and 64GB RAM

## Instance Design

Each geographic area (Saltville and Suffolk) is solved for **20 instances** with varying customer sets:
- **Instances 1-10**: 8-27 customers (small to medium)
- **Instances 11-15**: 30-35 customers (medium to large)
- **Instances 16-20**: 45-57 customers (large)

Customer selection is designed to test:
- Geographic dispersion (clustered vs. scattered)
- Network coverage (spanning different road segments)
- Computational scalability (increasing problem size)

See `Saltville_Customers.dat` and `Suffolk_Customers.dat` for the exact customer sets used in each instance.

## Results Files

All CSV files contain the following columns:
- **instance**: Instance number (1-20)
- **objective**: Total route duration in minutes
- **solve_time_sec**: Wall-clock solution time in seconds
- **bnb_nodes**: Branch-and-bound nodes explored by Gurobi

### Interpreting Results
- **Drone** files show results when the vehicle collaborates with a drone
- **Vehicle** files show baseline results where only the vehicle serves customers
