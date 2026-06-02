# DRL_DP_BITS_Sem2_Assignment
DRL Dynamic programming

Derived from Group ID 239 (ends in 9, which is in range 5–9):

Grid: 6×6
Environment: 3 rescue targets, 2 charging stations, 4 danger zones, 3 blocked cells
Max battery: 15 units (9 is odd)
Wind probability: 30% (5–9 range)
Max steps: 75 (6×6 grid)
Start position: (0,0) — top-left corner
State space (for DP enumeration):

Position: 6×6 = 36 cells
Battery: 0–15 = 16 levels
Rescue target statuses: 2³ = 8 combinations
Total: 36 × 16 × 8 = 4,608 states
Actions: Up, Down, Left, Right, Hover (5 actions)

Reward structure:

Rescue target: +20
Danger zone entry: −10
Battery exhausted: −20
Charging station: +5
Regular movement: −1

Register number: 2024ac05884
Strip non-numeric characters → 202405884
Use as numpy random seed: np.random.seed(202405884)
Shuffle the 35 non-start cells and assign the first 14 positions in order: 3R → 2C → 4D → 3X → 2W, rest F

Register 2024ac05884 → seed 202405884
deterministic grid:

Row\Col  0    1    2    3    4    5
  0      S    F    R    F    C    F
  1      F    W    F    W    X    X
  2      C    X    F    F    D    R
  3      F    F    F    D    F    F
  4      F    D    F    D    F    R
  5      F    F    F    F    F    F

Behaviour of battery consumption:

* Entering C: battery → 15 (full refill), reward +5
* Hovering on C: battery = min(battery + 2, 15), reward −1 (normal hover cost applies)
* Hovering elsewhere: battery − 1, reward −1


Summary:

Group ID 239 | Register# 2024ac05884

Parameter	                Value
_________                __________
Grid                       6×6
Max battery                15 units
Wind probability           30%
Max steps                  75
DP method              Value Iteration (θ = 1e-3, γ = 0.99)
Rescue targets          3 at (0,2), (2,5), (4,5)
Charging stations        2 at (0,4), (2,0)
Danger zones              4 at (2,4), (3,3), (4,1), (4,3)
Blocked cells            3 at (1,4), (1,5), (2,1)
Wind zones                2 at (1,1), (1,3)

Key design decisions implemented:
* Entering C: battery → 15 (full), reward +5
* Hovering on C: battery += 2 (capped), reward −1
* Wind (30%): 0.775 probability for intended direction, 0.075 each for other 3
* Danger: −10 only on entry (not hover); episode continues
* Battery = 0: +R_BATTERY_DEAD (−20) additive penalty, terminal
* Rescue mask: bitmask prevents double-counting; bit $i$ set on first arrival at target $i$
* Transition cache: pre-computed for all ~21,000 (state, action) pairs before VI runs
* γ = 0.99, θ = 1e−3 (10^-3)
