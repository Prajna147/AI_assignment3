# AI_assignment3
UGV Navigation in a Dynamic, Unknown Environment

1. Introduction

This project implements navigation for an Unmanned Ground Vehicle (UGV) moving from a user-defined start position to a goal position on a 70 × 70 km grid.

The UGV does not know all the obstacles in advance. It uses a small sensor to discover nearby obstacles and uses Dijkstra's algorithm to plan the shortest path.

If an obstacle blocks the planned route, the UGV updates its knowledge and calculates a new path.

Every 20 movements, new obstacles are added to the real environment to simulate a changing battlefield.

2. Objective

The main objectives are:

Navigate the UGV from START to GOAL.

Avoid known and newly discovered obstacles.

Find a shortest path using Dijkstra's algorithm.

Re-plan when the current path becomes blocked.

Measure the performance of the navigation system.

Visualize the final UGV route.

3. Environment

The battlefield is represented as a 70 × 70 grid.

Each cell represents approximately 1 km × 1 km.

Symbols

0 → Free cell

1 → Obstacle

START → Initial UGV position

GOAL → Destination

The default settings are:

Parameter

Value

Description

Grid size

70 × 70

Battlefield size

START

(2, 2)

UGV starting position

GOAL

(67, 67)

Destination

DENSITY

0.20

Initial obstacle probability

SENSOR

1

3 × 3 sensing area

UPDATE

20

New obstacle event every 20 moves

NEW_OBS

2

Obstacles added per event

Random seed

42

Makes the initial map reproducible

These settings are taken from the project documentation.

4. Algorithm Used

Dijkstra's Algorithm

Dijkstra's algorithm is used to find the shortest path from the current UGV position to the goal.

The cost of moving from one cell to an adjacent cell is:

Cost = 1

The UGV can move in four directions:

        Up
        ↑
Left ← UGV → Right
        ↓
       Down

The algorithm uses a priority queue implemented using Python's heapq.

Why Dijkstra?

Dijkstra is suitable because:

The grid is represented as a graph.

Each movement has the same cost.

Obstacles can be treated as blocked nodes.

It finds the shortest path.

The path can be recalculated whenever new obstacles are discovered.

With equal-cost four-direction movement, Dijkstra behaves similarly to Breadth-First Search.

5. Dynamic Navigation

The UGV does not have complete information about the environment.

Two maps are maintained:

True Map

true represents the actual battlefield.

0 = free
1 = obstacle

Known Map

known represents what the UGV currently knows.

-1 = unknown
 0 = known free
 1 = known obstacle

The UGV starts with only its starting cell known.

The sensor reveals cells around the current UGV position.

6. Sensor

The sensor uses:

SENSOR = 1

This means the UGV observes a 3 × 3 area around itself.

For example:

. . .
. U .
. . .

The sensor copies the actual values from the true map into the known map.

The sensing area is checked so that the UGV does not access cells outside the grid.

7. Path Planning

The UGV initially runs Dijkstra from:

START = (2, 2)

to:

GOAL = (67, 67)

Unknown cells are treated as free during planning.

The planned path is stored in:

path

The cells actually visited by the UGV are stored in:

actual_path

8. Obstacle Detection and Re-planning

Before moving to the next cell, the UGV checks whether that cell is actually blocked.

If the cell is an obstacle:

The obstacle is marked in the known map.

The current path is discarded.

Dijkstra is run again.

A new path is generated.

The UGV can therefore adapt when an obstacle is discovered.

After every movement, the sensor checks the surrounding area again.

If a known obstacle is found somewhere on the remaining planned path, the UGV re-plans.

9. Dynamic Obstacles

Every 20 movements, new obstacles are added to the real environment.

The default configuration is:

UPDATE = 20
NEW_OBS = 2

Therefore:

Every 20 moves
       ↓
Add new obstacles
       ↓
Sense the environment
       ↓
Update knowledge
       ↓
Re-plan if required

The UGV is not directly informed when the new obstacles are added. It discovers them through sensing or when its planned next cell is blocked.

10. Measures of Effectiveness

The program records several Measures of Effectiveness (MOE).

1. Goal Reached

Shows whether the UGV successfully reached the goal.

Goal reached: True

True means successful navigation.

False means the UGV could not reach the goal.

2. Path Length

The number of movements made by the UGV.

Path length = len(actual_path) - 1

Since one cell represents 1 km, this is reported in kilometres.

3. Movement Steps

The total number of actual UGV movements.

4. Dynamic Obstacle Events

The number of times the environment added new obstacles.

5. Replans

The number of times Dijkstra was executed.

This includes the first path calculation.

A high number of replans indicates that the UGV had to frequently adjust its route.

6. Nodes Explored

The total number of nodes removed from the Dijkstra priority queue across all searches.

This gives an indication of the search effort.

7. Execution Time

The total time taken by the navigation process.

It is measured using:

time.perf_counter()

8. Final Position

The final (x, y) position of the UGV.

11. Sample Output

For the documented run using random seed 42, the sample result was approximately:

Goal reached: True
Path length: 234 km
Movement steps: 234
Dynamic obstacle events: 11
Replans: 43
Nodes explored: 116267
Execution time: about 0.27 seconds

The exact execution time can vary depending on the computer running the program.

12. Visualization

The program displays a plot showing:

The final real environment

Obstacles

UGV path

Start position

Goal position

The UGV path is drawn from the starting point to the final position.

The plot uses:

X-axis → X coordinate (km)
Y-axis → Y coordinate (km)

The origin is at the lower-left corner.

13. Installation

Make sure Python 3.8 or later is installed.

Install the required libraries:

pip install numpy matplotlib

14. How to Run

Open the project folder in VS Code.

Run:

python UGV_Dynamic_Navigation.py

The program will calculate the path and display the performance measurements and visualization.

15. Project Flow

Create 70 × 70 Battlefield
          ↓
Generate Initial Obstacles
          ↓
Initialize START and GOAL
          ↓
Sense Nearby Cells
          ↓
Run Dijkstra
          ↓
Generate Shortest Path
          ↓
Move UGV
          ↓
Sense Environment
          ↓
Obstacle Found?
      /          \
    Yes           No
     ↓             ↓
Update Map       Continue
     ↓
Re-plan using Dijkstra
          ↓
Every 20 Moves
          ↓
Add Dynamic Obstacles
          ↓
Continue Navigation
          ↓
Reach GOAL
          ↓
Calculate MOEs
          ↓
Display Route

16. Assumptions

The implementation makes the following assumptions:

The UGV moves in four directions.

Each movement has equal cost.

Each grid cell represents 1 km.

The UGV is treated as a point-sized object.

The sensor provides perfect information within its sensing range.

Obstacles are represented as blocked grid cells.

The UGV can re-plan whenever new obstacles are discovered.

17. Limitations

The current implementation has some limitations:

Dijkstra starts a new search from scratch every time re-planning is required.

Unknown cells are initially treated as free.

New obstacles are added deterministically rather than randomly.

The maximum number of movements is fixed at 10,000.

The UGV does not have physical size or turning constraints.

For better efficiency in a larger dynamic environment, algorithms such as A* or D Lite* could be considered.

18. Conclusion

This project demonstrates how a UGV can navigate a dynamic battlefield represented as a grid.

Dijkstra's algorithm is used to calculate the shortest available path while avoiding known obstacles. The sensor allows the UGV to discover previously unknown obstacles, and the system re-plans whenever the current route becomes blocked.

The performance is evaluated using path length, movement steps, number of replans, nodes explored, dynamic obstacle events, execution time, and whether the goal was successfully reached.
