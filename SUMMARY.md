

---

# Humanitarian Aid Warehouse Network Optimization for Disaster Response in Turkey

## A Mixed Integer Programming Approach to Emergency Supply Chain Design

**By Aysan Pakmanesh (528241005)**
M.Sc. Big Data & Business Analytics - Istanbul Technical University

---

## Executive Summary

Turkey faces significant natural disaster risks, particularly in the Aegean and Marmara regions where seismic activity, industrial density, and coastal vulnerabilities create compound risks.
This project develops a warehouse placement system for humanitarian aid distribution across Turkey's disaster-prone regions.

---

## 1. Problem Statement

### Geographic Scope

Focus on 14 key cities:

* Istanbul (Europe & Asia) – High-risk mega-city
* Bursa – Industrial hub with seismic risk
* Balıkesir – Coastal area with flood/earthquake risk
* Çanakkale – Earthquake-prone
* Kocaeli – Industrial zone with high seismic risk
* Sakarya – Earthquake risk
* Tekirdağ – Moderate risk
* İzmir – Major city with high earthquake risk
* Aydın – Earthquake and flood risk
* Denizli – Moderate seismic risk
* Manisa – Earthquake risk
* Muğla – Coastal flood risk
* Uşak – Lower risk

### Warehouse Location & Sizing

* Potential warehouse locations where warehouses can be built and serve the cities.
* Three warehouse sizes with varying costs and capacities:

| Size   | Capacity (units) | Cost (million TL) |
| ------ | ---------------- | ----------------- |
| Large  | 2,200,000        | 25                |
| Medium | 1,500,000        | 20                |
| Small  | 1,250,000        | 17                |

### Project Objective

Create a mathematically optimal warehouse network that:

* Minimizes total system costs (construction and operation)
* Prioritizes rapid response to high-risk areas
* Ensures adequate coverage for vulnerable populations
* Maintains operational efficiency and resource utilization

---

## 2. Data Foundation and Methodology

### 2.1 City/District Structure

* Model operates at district level.
* Cities subdivided into districts for better population and logistics modeling.
* 21 potential warehouse locations across 14 cities.

### 2.2 Data Collection and Processing

#### Population & Risk

* Population data and risk scores assigned at city level, then proportionally distributed to districts.
* Risk scores based on seismic fault proximity, historical earthquakes, industrial density, flood risk, etc.

#### Distance Calculations

* Distances between district centroids calculated using Euclidean distance multiplied by 1.4 to approximate driving distance.

### 2.3 Demand Estimation

* Demand based on affected population ratios, coverage targets, and household-based unit requirements.
* Assumes 25% of population affected, 30% coverage by foundation.
* Units per person calculated from blankets, sleeping bags, and tents (with tents requiring 3 units per family of \~3.2 people).
* Total units per person = 2.9375 units.

Demand formula:
`Demand = Population × 0.25 (affected) × 0.30 (coverage) × 2.9375 (units/person)`

### 2.4 Technical Approach

#### Risk and Priority Modeling

* Incorporates seismic risk scores (1-10) directly into the objective function.
* Risk-weighted transportation costs incentivize warehouses closer to high-risk districts.
* Distance and capacity constraints maintain operational efficiency and prevent clustering outside Istanbul.

#### Assignment

* Each district assigned to exactly one warehouse for initial disaster response.

---

## 3. Mathematical Model Formulation

### 3.1 Sets and Indices

* I: Potential warehouse locations (districts)
* J: Demand districts (same as I)
* S: Warehouse sizes {Small, Medium, Large}
* C: Cities
* R: Regions {Marmara, Aegean, Northwest}

### 3.2 Parameters

* demand\_j: Demand in district j
* population\_j: Population of district j
* risk\_j: Disaster risk score for district j
* dist\_ij: Distance between i and j
* capacity\_s: Warehouse capacity by size s
* cost\_s: Warehouse cost by size s
* DISTANCE\_WEIGHT: 0.01 (cost penalty per unit risk-weighted distance)
* MIN\_UTILIZATION: 0.50 (min warehouse utilization)
* MIN\_TOTALWAREHOUSE: 6
* MIN\_DISTANCE: 20 km (min separation distance)
* Regional warehouse minimums and city max warehouses defined

### 3.3 Decision Variables

* y\_is ∈ {0,1}: Warehouse built of size s at district i
* x\_ij ∈ {0,1}: District j assigned to warehouse i

### 3.4 Objective Function

Minimize total cost = sum of fixed warehouse costs + risk-weighted transportation costs:

`Min Z = Σ_i Σ_s cost_s × y_is + DISTANCE_WEIGHT × Σ_i Σ_j dist_ij × risk_j × x_ij`

### 3.5 Constraints

* Each district assigned to exactly one warehouse.
* District assignments only to established warehouses.
* Demand assigned ≤ warehouse capacity.
* Warehouse utilization ≥ MIN\_UTILIZATION × capacity.
* Only one warehouse size per district.
* City-level max warehouses respected.
* Istanbul minimum warehouse count enforced.
* Warehouses outside Istanbul must be separated by MIN\_DISTANCE.
* District service distances respect max allowed by risk.
* Total warehouses ≥ MIN\_TOTALWAREHOUSE.
* Regional minimum warehouse coverage enforced.

---

## 4. Implementation and Solution

### 4.1 Technical Implementation

* Python 3.x, Pyomo for modeling, GLPK solver.
* Data preprocessing via NumPy and Pandas.

### 4.2 Baseline Solution Results

* Total demand: 7,169,630 units
* Warehouses built: 6
* Total system capacity: 8,700,000 units (utilization 82.4%)
* Total cost: 181.75 million TL (62% fixed, 38% transport)
* Maximum service distance: 192 km

Warehouse distribution:

* Istanbul: 2 warehouses (medium + large), high utilization >94%, serving all 5 districts within 22 km
* Izmir: 1 small warehouse, utilization 80.9%, serves 4 districts
* Kocaeli: 1 small warehouse, 93.4% utilization, serves 5 districts
* Canakkale & Denizli: 1 small warehouse each, utilization 52-64%, serves wider regions

---

## 5. Scenario Analysis

### 5.1 Scenario 1: Demand Surge

* Increased affected ratio to 30%, coverage to 33%
* Total demand: 9,463,911 units (+32%)
* Total cost: 201.34 million TL (+19.6 M)
* Warehouses: 7 (one more than baseline)
* Utilization: 86.8%
* Max service distance: 192 km

**Insights:**

* Added warehouse in Istanbul to maintain capacity and short response times.
* Izmir warehouse upgraded to medium size.
* Effective demand absorption with moderate added cost.

### 5.2 Scenario 2: Economic Efficiency Focus

* Minimum utilization increased to 70%
* Total cost: 207.75 million TL
* Warehouses: 8
* Utilization: 79.5%
* Max service distance: 246 km

**Insights:**

* More warehouses (Bursa, Balikesir) to increase utilization and reduce transport cost.
* Increased fixed costs but more cost-effective long-term.
* Service distances extended in lower-risk areas, balancing efficiency and coverage.

### 5.3 Scenario 3: Rapid Response Priority

* Distance weight increased to 0.03 (higher transport penalty)
* Minimum utilization lowered to 30%
* Minimum warehouses increased to 8
* Total cost: 277.99 million TL
* Warehouses: 9
* Utilization: 70.6%
* Max service distance: 182 km

**Insights:**

* Denser warehouse network for faster emergency response, especially in high-risk zones.
* Higher fixed costs due to more facilities, but improved responsiveness.
* Lower utilization acceptable for surge capacity and rapid deployment.

---

## 6. Comparative Strategic Analysis

| Scenario                            | Demand Surge | Economic Efficiency | Rapid Response |
| ----------------------------------- | ------------ | ------------------- | -------------- |
| Warehouses Built                    | 7            | 8                   | 9              |
| System Capacity (M units)           | 10.9         | 11.9                | 13.4           |
| System Utilization (%)              | 86.8         | 79.5                | 70.6           |
| Total Cost (million TL)             | 201.34       | 207.75              | 277.99         |
| Fixed / Transport Cost Split (M TL) | 138 / 63     | 152 / 56            | 172 / 106      |
| Max Service Distance (km)           | 192          | 246                 | 182            |
| Risk-10 Max Distance (km)           | 53           | 58                  | 53             |

**Strategic Recommendations:**

* **Baseline:** Balanced cost and coverage, ideal for normal operations.
* **Demand Surge:** Prepares for disaster spikes with moderate cost increase.
* **Economic Efficiency:** Budget-conscious planning prioritizing utilization and long-term cost.
* **Rapid Response:** Maximizes emergency readiness with more facilities and faster coverage at higher cost.

---

## 7. References and Data Sources

* [https://www.citypopulation.de/](https://www.citypopulation.de/)
* [https://www.macrotrends.net/](https://www.macrotrends.net/)
* [https://en.wikipedia.org/](https://en.wikipedia.org/)
* [https://en.afad.gov.tr/](https://en.afad.gov.tr/)
* [https://worldpopulationreview.com/](https://worldpopulationreview.com/)
* [https://www.distancecalculator.net/](https://www.distancecalculator.net/)
* [https://www.distancefromto.net/](https://www.distancefromto.net/)

---

## 8. Project Links and Contact

* **GitHub Repository:** [github.com/AysanPak/aid-warehouse-mip](https://github.com/AysanPak/aid-warehouse-mip)
* **LinkedIn:** [linkedin.com/in/aysan-pak](https://linkedin.com/in/aysan-pak)
* **Email:** [aysanpakmanesh@gmail.com](mailto:aysanpakmanesh@gmail.com)

---


