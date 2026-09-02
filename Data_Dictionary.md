# Data Dictionary

Documentation for the instance data files in `data/`.

## Files

| File | Contents |
|---|---|
| `Saltville_Data.dat` | Saltville, VA network and customer data (158 nodes, 58 customers) |
| `Suffolk_Data.dat` | Suffolk, VA network and customer data (270 nodes, 104 customers) |
| `Saltville_Customers.dat` | Customer subsets for instances 1–20 |
| `Suffolk_Customers.dat` | Customer subsets for instances 21–40 |

Both `*_Data.dat` files share one schema, described below. Both
`*_Customers.dat` files assign the demand set `K` per instance
(`if (instance = i) then { let K := {...}; }`); per-instance customer counts
match the `num_customers` column of the published result files exactly.

## Scalar parameters

| Name | Value | Units | Meaning |
|---|---|---|---|
| `n` | 158 / 270 | — | Number of road-network nodes (Saltville / Suffolk) |
| `customers` | 58 / 104 | — | Number of customers in the AOI |
| `flightTime` | 30 | min | Maximum drone flight time (endurance), inclusive of service |
| `droneSpeed` | 5,720 | ft/min | Drone airspeed (≈65 mph) |
| `droneServiceTime[1..3]` | 0.083 / 0.5 / 0.083 | min | Takeoff / customer service / landing components; total 0.666 min (40 s) per sortie |
| `vehicleSpeed[1..3]` | 3,080 / 2,200 / 880 | ft/min | Vehicle speed on primary / secondary / private roads (≈35 / 25 / 10 mph) |
| `StepSize` | 105 | ft | Discretization step between candidate drone launch/retrieval (rendezvous) points along eligible arcs |

## Indexed parameters and sets

| Name | Index | Units | Meaning |
|---|---|---|---|
| `Xc`, `Yc` | customer `1..customers` | decimal degrees | Customer coordinates. **`Xc` is longitude (negative in Virginia), `Yc` is latitude.** |
| `Xv`, `Yv` | node `1..n` | decimal degrees | Road-network node coordinates (same convention) |
| `cost2[i,j]` | directed node pair | feet | Road distance of arc (i, j). The set of index pairs defines the road network's arcs |
| `customerServiceNode[k]` | customer | node id | The road-network node at which customer *k*'s driveway meets the public road (the vehicle's service point under VDS) |
| `customerVehicleServiceTime[k]` | customer | min | Pre-computed manual (VDS) service time for customer *k*, a function of driveway length under USPS RRECS time standards. **Driveway navigation is charged here, to service time, in both delivery systems** — reported vehicle mileage is therefore public-road mileage |
| `AL` | set of directed arcs | — | Low-speed (secondary-road) arcs eligible for drone launch and retrieval |
| `RetainedArcs` | set of directed arcs | — | Arcs retained after network preprocessing |
| `Arcs` | set of directed arcs | — | Initialized empty; populated at run time from the road network during the dead-end network transformation |

## Units warning

Coordinates are geographic (decimal degrees). All distances and drone flight
times in the study were computed **after projecting coordinates to planar
feet**; computing Euclidean distances directly on raw degree values will not
reproduce the reported results.

## Recreating an instance

1. Load an AOI's `*_Data.dat`.
2. Select the instance's customer set `K` from the matching `*_Customers.dat`.
3. Project all coordinates to planar feet.
4. Generate candidate rendezvous points at `StepSize` intervals along the arcs
   in `AL`, and enumerate drone flight paths feasible within `flightTime`
   (flight legs at `droneSpeed`, plus the three `droneServiceTime` components).
5. Vehicle travel times follow `cost2` distances at the road-class speeds;
   customer *k*'s manual service takes `customerVehicleServiceTime[k]` under
   VDS, replaced by the drone sortie under DDS.

The manuscript's problem-statement and flight-path-generation sections
give the complete formal construction.
