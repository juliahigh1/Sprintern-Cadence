# Sprintern-Cadence

This repository is for cadence sprinterns.

## Steiner Tree Algorithm Implementation

### **Overview**
This project implements a Steiner Tree algorithm to minimize the total wirelength connecting a set of input points using Manhattan (rectilinear) distances. The goal is to compute the optimal connections for a given set of terminal points, possibly adding Steiner points, while following the constraints of the rectilinear geometry.

The program takes an input file containing Cartesian coordinates of points and outputs a file containing the resulting Steiner tree, including Steiner points and edges.

---

### **Features**
- Implements the **Hanan grid** to identify potential Steiner points.
- Utilizes **Kruskal’s MST algorithm** to generate a Minimum Spanning Tree (MST).
- Includes a **1-Steiner heuristic** to further optimize the rectilinear MST.
- Supports modular and multithreaded code design for efficiency.
- Fully documented and follows good coding practices.

## **Input File Format**
The input file is a text file (e.g., `example.in`) with the following format:
1. The first line contains the number of terminal points (**N**).
2. Each of the next **N** lines contains the **x** and **y** coordinates of a point.

### Example:

4
0 0
10 0
0 10
10 10

---

## **Output File Format**
The output file (e.g., `example.out`) includes:
1. A list of all points (terminal and Steiner points).
2. A list of edges in the Steiner tree and their weights (Manhattan distances).

### Example:

Points:
* 0 0
* 10 0
* 0 10
* 10 10
* 5 5

Edges:
* (0, 0) -> (5, 5) : 10
* (10, 0) -> (5, 5) : 10
* (0, 10) -> (5, 5) : 10
* (10, 10) -> (5, 5) : 10

---

## **Setup Instructions**

### **Prerequisites**
- A C++ compiler (e.g., `g++` or `clang`).
- A terminal or command prompt.
- (Optional) A text editor like VS Code for editing and testing the code.

### **Building the Code**
1. Navigate to the project directory:
   ```bash
   cd project/src

	2.	Use the provided Makefile to build the project:

make

This will compile the steiner.cpp file and generate an executable called steiner.

Running the Program
	1.	Run the program with an input file and specify an output file:

./steiner ../data/example.in ../data/example.out

	•	Input File: Contains terminal points in the format described above.
	•	Output File: The program will generate this file with the Steiner tree details.

	2.	View the generated output file:

cat ../data/example.out

Implementation Plan

Pseudocode

**SteinerTree Algorithm**

**Data Structures:**

* Point: (id, x, y, sink) - represents a point in 2D space, `sink` flag indicates if it's an original input point.
* Edge: (node1, node2, weight) - represents a connection between two points, `weight` is the Manhattan distance. 

**Class SteinerTree:**

    Constructor(path):
        - Initialize filePath with the given path
        - Set numberOfPoints and originalSinkCount to 0
        - Call readInputFile() to load input data
        - Store the initial number of points in originalSinkCount 

    readInputFile():
        - Open the file specified by filePath for reading
        - IF file open fails:
            - Throw an error
        - LOOP through each line in the file:
            - Split the line into words
            - IF the first word is "number_of_sinks":
                - Read the next word as the numberOfPoints
            - ELSE IF the first word is "sink":
                - Read the next three words as id, x, and y 
                - Create a new Point with the read values and set its `sink` flag to true
                - Add the new Point to the `points` vector
        - Close the input file

    printSinks():
        - Print "Number of sinks: " followed by the value of numberOfPoints
        - FOR each Point 'p' in the `points` vector:
            - IF `p.sink` is true:
                - Print the Point's ID, x-coordinate, and y-coordinate

    mhDistance(Point a, Point b):
        - Calculate and return the Manhattan distance between points 'a' and 'b': |a.x - b.x| + |a.y - b.y|

    computePrimMST(points, outMstLen):
        - IF `points` has less than 2 points:
            - IF `outMstLen` is provided (not null), set it to 0
            - Return an empty vector (no edges in the MST)
        - Determine the number of points 'n' 
        - Create a mapping 'idToIndex' to map point IDs to indices 0 to n-1
        - Initialize `mstEdges` to store the MST edges
        - Initialize `dist` (distances to MST), `parent` (MST connections), and `inMST` (inclusion in MST) vectors, all with size 'n' 
        - Set dist[0] to 0 (starting point)
        - FOR each point 'i' from 0 to n-1:
            - Find the unvisited node 'u' with the smallest distance in the `dist` vector 
            - Mark node 'u' as visited in the `inMST` vector
            - IF 'u' has a parent (not the starting node):
                - Create a new Edge 'e' connecting 'u' and its parent based on the `parent` vector
                - Calculate the weight of 'e' using `mhDistance`
                - Add 'e' to the `mstEdges` vector
            - FOR each neighbor 'v' of 'u':
                - IF 'v' is not already in the MST:
                    - Calculate the cost of the edge between 'u' and 'v' using `mhDistance`
                    - IF `cost` is less than the current distance to 'v' in the `dist` vector:
                        - Update the distance to 'v' in the `dist` vector
                        - Set the parent of 'v' to 'u' in the `parent` vector 
        - IF `outMstLen` is provided, calculate the total length of the MST and store it in `outMstLen`
        - Return the `mstEdges` vector

    sumEdges(edges): 
        - Initialize a variable `total` to 0
        - FOR each Edge 'e' in the `edges` vector:
            - Add `e.weight` to `total`
        - Return `total`

    getUniqueCoords(pts, isX): 
        - Create an empty set `coords`
        - FOR each Point 'p' in the `pts` vector:
            - IF `isX` is true, insert `p.x` into the `coords` set
            - ELSE, insert `p.y` into the `coords` set
        - Return a vector containing all elements from the `coords` set

    getHananGridPoints(pts):
        - Get the unique x-coordinates from `pts` using `getUniqueCoords(pts, true)` and store them in `xVals`
        - Get the unique y-coordinates from `pts` using `getUniqueCoords(pts, false)` and store them in `yVals`
        - Create an empty vector `candidates` to store potential Hanan grid points
        - Initialize an ID counter `startId` for the new points
        - FOR each x-coordinate 'x' in `xVals`:
            - FOR each y-coordinate 'y' in `yVals`:
                - Set a flag `alreadyExists` to false
                - FOR each Point 'p' in the `pts` vector:
                    - IF the current (x, y) coincides with an existing point: 
                        - Set `alreadyExists` to true and break the inner loop
                - IF `alreadyExists` is false (the point is new):
                    - Create a new Point with ID `startId`, coordinates (x, y), and set `sink` to false (Steiner point)
                    - Increment `startId`
                    - Add the new point to the `candidates` vector
        - Print debug information: the generated Hanan grid points
        - Return the `candidates` vector

    addSteinerPoints():
        - Calculate the initial MST length using `computePrimMST()` and store it in `baseLength`
        - Print the initial MST length 
        - Generate Hanan grid points based on the current `points` and store them in `hanan`
        - Initialize `improved` flag to true
        - WHILE `improved` is true:
            - Set `improved` to false 
            - Initialize `bestImprovement` to 0 and `bestSteiner` to an empty Point
            - Print debug information about the current iteration
            - FOR each candidate point 'candidate' in the `hanan` vector:
                - Create a temporary vector `tempPoints` containing all points from the `points` vector
                - Add the `candidate` point to `tempPoints`
                - Calculate the MST length of `tempPoints` using `computePrimMST()` and store it in `newLength`
                - Calculate the `improvement` achieved by adding the `candidate` point (`baseLength` - `newLength`)
                - Print debug information about the candidate point and its improvement
                - IF `improvement` is greater than the current `bestImprovement`:
                    - Update `bestImprovement` and `bestSteiner` with the current candidate's values
                    - Set `improved` to true 
            - IF `improved` is true:
                - Add the `bestSteiner` to the main `points` vector
                - Increment `numberOfPoints`
                - Update the `baseLength` with the new MST length using `computePrimMST()`
                - Print debug information about the added Steiner point
                - Recompute the Hanan grid points `hanan` with the updated set of points 
        - [Commented out section for removing Steiner points - Logic similar to adding]
        - Compute the final MST using `computePrimMST()` and store it in `edges`
        - Print the final MST length 

    findPointByID(id):
        - FOR each point 'p' in the `points` vector:
            - IF `p.id` matches the given `id`, return a pointer to 'p'
        - Return nullptr (point not found)

    writeOutputToFile(outFile, mstEdges):
        - Open the file specified by `outFile` for writing
        - IF file open fails: 
            - Throw an error
        - Write "number_of_sinks " followed by `originalSinkCount` to the file
        - Write two newline characters for formatting 
        - Initialize a flag `onceSpace` to true
        - FOR each Point 'p' in the `points` vector:
            - IF `p.sink` is true:
                - Write "sink " followed by the point's ID, x-coordinate, and y-coordinate to the file
            - ELSE:
                - IF `onceSpace` is true (only once for Steiner points):
                    - Write a newline character to separate sinks and Steiner points
                    - Set `onceSpace` to false
                - Write "point " followed by the point's ID, x-coordinate, and y-coordinate to the file 
        - Write two newline characters for formatting
        - FOR each Edge 'e' in the `mstEdges` vector:
            - Find the endpoints of the edge using `findPointByID()` and store them in `p1` and `p2`
            - IF either endpoint is not found:
                - Continue to the next edge (skip processing)
            - IF the edge is rectilinear (shares x or y coordinate): 
                - Write "edge " followed by the edge's `node1` and `node2` to the file 
            - ELSE (the edge is diagonal): 
                - Calculate a unique ID `midID` for a new Steiner point
                - Write "point " followed by `midID`, and the coordinates for the new Steiner point (to make the edge rectilinear) 
                - Write two "edge" lines, connecting the original points to the new Steiner point, making the connection rectilinear
        - Calculate the total MST length using `sumEdges(mstEdges)` and write it to the file
        - Close the output file 

    printEdges(edgeList):
        - Print "Total number of edges: " followed by the size of the `edgeList` vector
        - FOR each Edge 'e' in the `edgeList` vector:
            - Print "Edge between " followed by `e.node1`, " and ", `e.node2`, " has Manhattan distance: ", and `e.weight` 

**Function reorderOutputFile(filename):**
    - Opens the file specified by `filename` for reading.
    - Reads lines from the file and categorizes them into `sinks`, `points`, `edges`, `numberOfSinks`, and `pathLength` vectors based on keywords.
    - Closes the input file.
    - Opens the same file for writing, overwriting the existing content.
    - Writes the categorized lines back into the file in a specific order: `numberOfSinks`, `sinks`, `points`, `edges`, and `pathLength`.
    - Closes the output file.

---

## **Contributors**
- Arohan Shrestha
- Viswa Kotra
- Lambert Ike
- Julia High
- Ishita Kumari
