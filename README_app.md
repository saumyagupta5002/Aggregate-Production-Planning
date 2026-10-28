# Aggregate Production Planning with Linear Programming

A linear programming model that plans monthly production over a 6-month horizon. It decides how much regular and overtime labor to use, how much to produce and how much inventory to carry, so that demand is met at minimum total cost.

## Problem Statement

A manufacturer faces known monthly demand from January to June. Each month it can produce using regular labor (cheaper, limited hours) or overtime labor (more expensive, limited hours). Units can be produced early and held in inventory, at a holding cost, to cover later demand. The goal is to find the plan with the lowest total cost of production, holding, regular labor and overtime labor.

## Input Data

| | Jan | Feb | Mar | Apr | May | Jun |
|---|---|---|---|---|---|---|
| Demand | 100 | 100 | 150 | 200 | 150 | 100 |
| Unit production cost (excl. labor) | 7 | 8 | 8 | 8 | 7 | 8 |
| Unit holding cost | 3 | 4 | 4 | 4 | 3 | 2 |
| Unit regular labor cost | 15 | 15 | 18 | 18 | 15 | 15 |
| Unit overtime labor cost | 22.5 | 22.5 | 27 | 27 | 22.5 | 22.5 |
| Available regular man-hours | 120 | 130 | 120 | 150 | 100 | 100 |
| Available overtime man-hours | 30 | 40 | 40 | 30 | 30 | 30 |

Initial inventory (end of December): 3 units.

## Mathematical Formulation

**Indices:** t = 1, ..., 6 (Jan to Jun)

**Decision variables**
- R_t: regular labor hours used in month t
- O_t: overtime labor hours used in month t
- P_t: units produced in month t
- I_t: inventory at the end of month t

**Objective**

Minimize the sum over t of ( c_t P_t + h_t I_t + r_t R_t + o_t O_t )

where c_t is unit production cost, h_t is unit holding cost, r_t is regular labor cost and o_t is overtime labor cost.

**Constraints**
1. Production: P_t = R_t + O_t (1 man-hour per unit)
2. Inventory balance: I_t = I_(t-1) + P_t - D_t, with I_0 = 3
3. Regular capacity: R_t <= available regular hours
4. Overtime capacity: O_t <= available overtime hours
5. Non-negativity: R_t, O_t, P_t, I_t >= 0

## Solution

| | Dec | Jan | Feb | Mar | Apr | May | Jun |
|---|---|---|---|---|---|---|---|
| Regular man-hours | | 120 | 130 | 120 | 150 | 100 | 100 |
| Overtime man-hours | | 0 | 17 | 0 | 30 | 30 | 0 |
| Production | | 120 | 147 | 120 | 180 | 130 | 100 |
| Inventory | 3 | 23 | 70 | 40 | 20 | 0 | 0 |

**Total cost: 20,192.5**

| Component | Cost |
|---|---|
| Production | 6,126.0 |
| Holding | 589.0 |
| Regular labor | 11,610.0 |
| Overtime labor | 1,867.5 |

## Key Insights

- Regular labor is used to full capacity every month, because it is always cheaper than overtime.
- Overtime is used in February, April and May, when demand is high relative to regular capacity.
- The 17 overtime hours in February are used to build inventory for March. Producing in February costs 22.5 + 8 + 4 = 34.5 per unit including a month of holding, versus 27 + 8 = 35 for overtime in March, so February is slightly cheaper.
- Inventory falls to zero by May, so no stock is held beyond what is needed.

## How to Run

Requirements: Python 3.8+ and PuLP.

```bash
pip install pulp
python production_planning.py
```

## Repository Structure

```
.
├── README.md
├── production_planning.py   # model and solver code
└── data/                    # input data (optional)
```

## Tools Used

- Python
- PuLP (CBC solver)

## Possible Extensions

- Allow backorders with a shortage penalty
- Add hiring and firing costs for regular labor
- Add a safety stock requirement
- Run sensitivity analysis on demand and overtime cost
