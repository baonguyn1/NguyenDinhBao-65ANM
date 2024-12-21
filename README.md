# NguyenDinhBao-65ANM
#include <iostream>
#include <vector>
#include <queue>
#include <cmath>
#include <limits.h>
#include <functional>

using namespace std;

// Thuuat toan nhanh can (Branch and Bound)
int branchAndBound(vector<int>& weights, vector<int>& values, int capacity) {
    int n = weights.size();
    int maxValue = 0;
    vector<int> currentSolution(n, 0);
    vector<int> bestSolution(n, 0);
    function <void(int, int, int)> solve = & {
        if (i == n) {
            if (currentValue > maxValue) {
                maxValue = currentValue;
                bestSolution = currentSolution;
            }
            return;
        }

        if (currentWeight + weights[i] <= capacity) {
            currentSolution[i] = 1;
            solve(i + 1, currentWeight + weights[i], currentValue + values[i]);
            currentSolution[i] = 0;
        }
        solve(i + 1, currentWeight, currentValue);
    };

    solve(0, 0, 0);
    return maxValue;
}

// Algo A* (A-star)
struct Node {
    int x, y;
    int g, h;
    Node* parent;

    Node(int x, int y, int g, int h, Node* parent = nullptr) : x(x), y(y), g(g), h(h), parent(parent) {}

    int f() const {
        return g + h;
    }

    bool operator>(const Node& other) const {
        return f() > other.f();
    }
};

int heuristic(int x1, int y1, int x2, int y2) {
    return abs(x1 - x2) + abs(y1 - y2);
}

vector<Node*> getNeighbors(Node* node, int goalX, int goalY) {
    vector<Node*> neighbors;
    int dx[] = {0, 1, 0, -1};
    int dy[] = {1, 0, -1, 0};

    for (int i = 0; i < 4; ++i) {
        int newX = node->x + dx[i];
        int newY = node->y + dy[i];
        neighbors.push_back(new Node(newX, newY, node->g + 1, heuristic(newX, newY, goalX, goalY), node));
    }

    return neighbors;
}

void printPath(Node* node) {
    if (node == nullptr) return;
    printPath(node->parent);
    cout << "(" << node->x << ", " << node->y << ") ";
}

void aStar(int startX, int startY, int goalX, int goalY) {
    priority_queue<Node*, vector<Node*>, greater<Node*>> openList;
    openList.push(new Node(startX, startY, 0, heuristic(startX, startY, goalX, goalY)));

    while (!openList.empty()) {
        Node* current = openList.top();
        openList.pop();

        if (current->x == goalX && current->y == goalY) {
            cout << "Path found: ";
            printPath(current);
            cout << endl;
            return;
        }

        vector<Node*> neighbors = getNeighbors(current, goalX, goalY);
        for (Node* neighbor : neighbors) {
            openList.push(neighbor);
        }
    }

    cout << "No path found" << endl;
}

int main() {
    // Test Thuat toan nhanh can
    vector<int> weights = {2, 3, 4, 5};
    vector<int> values = {3, 4, 5, 6};
    int capacity = 5;
    cout << "Maximum value (Branch and Bound): " << branchAndBound(weights, values, capacity) << endl;

    // Test thuat toan A*
    int startX = 0, startY = 0;
    int goalX = 3, goalY = 3;
    aStar(startX, startY, goalX, goalY);

    return 0;
}
