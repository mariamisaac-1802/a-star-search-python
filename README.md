# A* Search Algorithm in Python

A simple, beginner-friendly implementation of the **A\* (A-star) search algorithm** in Python. It is written in the same style as a basic Uniform Cost Search (UCS) implementation, with one key change: the priority queue is ordered by

```
f(n) = g(n) + h(n)
```

instead of only `g(n)`.

- `g(n)`: the actual cost of the path from the start node to `n`
- `h(n)`: the heuristic estimate of the cost from `n` to the nearest goal
- `f(n)`: the estimated total cost of the path through `n`

## How It Works

1. Put the start node in a priority queue with priority `f = h(start)`.
2. Repeatedly remove the node with the lowest `f` value.
3. If it is a goal node, print the path and its real cost, then stop.
4. Otherwise, expand it: for every child compute `g(child) = g(current) + edge_cost` and push it with priority `g(child) + h(child)`.

Since the queue stores `f`, the real cost is recovered with `g = f - h(current)`. This keeps the same `(value, path)` tuple used in UCS.

## The Graph

The graph is a directed, weighted graph with three goal nodes (`G1`, `G2`, `G3`).

| Node | Neighbors (cost) | h(n) |
|------|------------------|------|
| S  | A (3), B (1), C (5) | 8 |
| A  | G1 (10), E (7)      | 9 |
| B  | C (2), F (2)        | 1 |
| C  | G3 (11)             | 3 |
| D  | S (6), B (4), G2 (5)| 4 |
| E  | G1 (2)              | 1 |
| F  | D (1)               | 5 |
| G1 | none                | 0 |
| G2 | none                | 0 |
| G3 | F (0)               | 0 |

## Requirements

- Python 3.x (standard library only, no external packages)

## Usage

```bash
python a_star.py
```

Or run it in a Jupyter Notebook cell.

## Code

```python
import queue as q

# Edge costs g(n): graph[node][child] = cost
graph = {
    'S':  {'A': 3, 'B': 1, 'C': 5},
    'A':  {'G1': 10, 'E': 7},
    'B':  {'C': 2, 'F': 2},
    'C':  {'G3': 11},
    'D':  {'S': 6, 'B': 4, 'G2': 5},
    'E':  {'G1': 2},
    'F':  {'D': 1},
    'G1': {},
    'G2': {},
    'G3': {'F': 0}
}

# Heuristic values h(n)
h = {'S': 8, 'A': 9, 'B': 1, 'C': 3, 'D': 4, 'E': 1, 'F': 5,
     'G1': 0, 'G2': 0, 'G3': 0}

def a_star(graph, start, end):
    queue = q.PriorityQueue()
    queue.put((h[start], [start]))            # f = g + h = 0 + h(start)
    while not queue.empty():
        node = queue.get()
        current = node[1][-1]
        if current in end:                    # end is a list of goal nodes
            print('path found ' + str(node[1]) + ' cost ' + str(node[0] - h[current]))
            break
        cost = node[0] - h[current]           # recover g(n) from f(n)
        for child in graph[current]:
            path = node[1][:]
            path.append(child)
            g = cost + graph[current][child]  # g(child)
            queue.put((g + h[child], path))   # f(child) = g(child) + h(child)

a_star(graph, 'S', ['G1', 'G2', 'G3'])
```

## Example Output

```
path found ['S', 'B', 'F', 'D', 'G2'] cost 9
```

## Notes

- **Multiple goals:** `end` is a list of goal nodes and the check is `current in end`. For a single goal, use `current == end` and pass a single node name such as `'G2'`.
- **No visited set:** like the simple UCS version, this code does not track visited nodes. It works for this graph because a goal is reached before the search loops around the cycle `S → B → F → D → S`. For graphs with cycles that the search might keep exploring, add a visited/explored set.
- **Heuristic:** goal nodes are assigned `h = 0`. The heuristic values used here are admissible (they never overestimate the true remaining cost), so A\* returns an optimal path.

## License

This project is open for educational use.
