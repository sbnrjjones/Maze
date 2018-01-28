# maze
In this learning activity, we will examine code that explores a 2D maze.  This experience may help you in developing the code for the recursion lab to explore a 3D maze.

To get started, lets build out enough of the recursive lab infrastructure to call the code we will develop.  Start by downloading the recursion lab interface files and creating a Pathfinder class in "Pathfinder.h" that inherits from PathfinderInterface.  Then create "Pathfinder.cpp" that includes "Pathfinder.h"

Now create a sample maze for us to explore in 2d.txt
```
1 1 0 1 0
0 1 0 1 0
1 1 1 1 0
1 0 0 1 0
1 1 0 1 1
```
Locations where there is a 1 are open pathways, locations with a zero are walls in the maze.

Now create code to read in the maze.  First, we will need an array that we can use to hold the maze.  Put it in "Pathfinder.h".  We will also add a vector to hold the solution when we find it.  Constants for the walls and pathways are also needed by the code from the book.
```c++
protected:
  const int ROW_SIZE = 5;
  const int COL_SIZE = 5;
  const int BACKGROUND = 1;
  const int WALL = 0;
  const int TEMPORARY = 2; // Used to show this path has been explored
  const int PATH = 3;
  int maze_grid[ROW_SIZE][COL_SIZE]; // To hold values
  vector<string> solution;
```
We can read from the 2d.txt file into the maze_grid by implementing importMaze in Pathfinder.cpp.  Notice that this only imports a 2d maze.  You will have to rewrite the code to make the lab work, but this should give us a start in making the code from the book work.  We use a stringstream to parse the zeros and ones in the line.
```c++
	bool Pathfinder::importMaze(string file_name)
	{
		cout << "importMaze from "<<file_name<<endl;
		ifstream file (file_name.c_str());
		if (file.is_open()) {
			string line;
			int row_count = 0;
			for(int row = 0; row < ROW_SIZE; row++) {
				getline(file, line);
				stringstream ss(line);
				for(int col = 0; col < COL_SIZE; col++) {
					int value;
					ss >> value;
					cout << "["<<row<<"]["<<col<<"]="<<value<<endl;
					maze_grid[row][col] = value;
				}
			}
		}
	    return(true);
	}
```
Now we need to create a way of printing out the maze so we can see what is happening.  The interface suggests that you should create a toString() function.
```c++
	string Pathfinder::toString() const
	{
	    stringstream ss;
	    for(int row = 0; row < ROW_SIZE; row++) {
	        for(int col = 0; col < COL_SIZE; col++) {
	            ss << maze_grid[row][col];
	        }
	        ss << endl;
	    }
	    return ss.str();
	}
```
Now call this code from main.cpp and make sure things are working.  You should have a stub to solveMaze() in your "Pathfinder.cpp".
```c++
	Pathfinder* pathptr = NULL;//the Pathfinder
	// Test 2D array
	pathptr = new Pathfinder();
	pathptr->importMaze("2D.txt");
	cout << pathptr->toString();
	pathptr->solveMaze();
	exit(0);
	// End of Test 2D array
```
Compile your code and make sure everything is working.  You should be able to see the grid in the terminal window.

Now we need to develop code to solve the maze.  Notice that it recursively calls itself to find a path from the upper left corner (0,0) to the bottom right (4,4).  When the bottom right position is found, we will add the coordinates of the path to the solution vector.
```c++
	bool Pathfinder::find_maze_path(int grid[ROW_SIZE][COL_SIZE], int r, int c) {
	  cout << "find_maze_path ["<<r<<"]["<<c<<"]"<<endl;
	  cout << this->toString();
	  if (r < 0 || c < 0 || r >= ROW_SIZE || c >= COL_SIZE)
	    return false;      // Cell is out of bounds.
	  else if (grid[r][c] != BACKGROUND)
	    return false;      // Cell is on barrier or dead end.
	  else if (r == ROW_SIZE - 1 && c == COL_SIZE - 1) {
	    grid[r][c] = PATH;         // Cell is on path
	    solution.push_back("("+to_string(r)+","+to_string(c)+")");
	    return true;               // and is maze exit.
	  }
	  else { 
	    // Recursive case.
	    // Attempt to find a path from each neighbor.
	    // Tentatively mark cell as on path.
	    grid[r][c] = PATH;
	    if (find_maze_path(grid, r - 1, c) // Up
	        || find_maze_path(grid, r + 1, c) // Down
	        || find_maze_path(grid, r, c - 1) // Left
	        || find_maze_path(grid, r, c + 1 ) ) { // Right
	      solution.push_back("("+to_string(r)+","+to_string(c)+")");
	      return true;
	    }
	    else {
	      grid[r][c] = TEMPORARY;  // Dead end.
	      return false;
	    }
	  }
	}
```
Now we just need to call this code from solveMaze() to start the recursion in the upper left corner of the maze.
```c++
	vector<string> Pathfinder::solveMaze()
	{
	    find_maze_path(maze_grid, 0,0);
	    for(auto s:solution) {
		cout <<s<<endl;
	    }
	    return solution;
	}
```
You should be able to observe the algorithm as it recurses through the maze.
```
importMaze from 2D.txt
11010
01010
11110
10010
11011
find_maze_path [0][0]
11010
01010
11110
10010
11011
find_maze_path [-1][0]
31010
01010
11110
10010
11011
find_maze_path [1][0]
31010
01010
11110
10010
11011
find_maze_path [0][-1]
31010
01010
11110
10010
11011
find_maze_path [0][1]
31010
01010
11110
10010
11011
find_maze_path [-1][1]
33010
01010
11110
10010
11011
find_maze_path [1][1]
33010
01010
11110
10010
11011
find_maze_path [0][1]
33010
03010
11110
10010
11011
find_maze_path [2][1]
33010
03010
11110
10010
11011
find_maze_path [1][1]
33010
03010
13110
10010
11011
find_maze_path [3][1]
33010
03010
13110
10010
11011
find_maze_path [2][0]
33010
03010
13110
10010
11011
find_maze_path [1][0]
33010
03010
33110
10010
11011
find_maze_path [3][0]
33010
03010
33110
10010
11011
find_maze_path [2][0]
33010
03010
33110
30010
11011
find_maze_path [4][0]
33010
03010
33110
30010
11011
find_maze_path [3][0]
33010
03010
33110
30010
31011
find_maze_path [5][0]
33010
03010
33110
30010
31011
find_maze_path [4][-1]
33010
03010
33110
30010
31011
find_maze_path [4][1]
33010
03010
33110
30010
31011
find_maze_path [3][1]
33010
03010
33110
30010
33011
find_maze_path [5][1]
33010
03010
33110
30010
33011
find_maze_path [4][0]
33010
03010
33110
30010
33011
find_maze_path [4][2]
33010
03010
33110
30010
33011
find_maze_path [3][-1]
33010
03010
33110
30010
22011
find_maze_path [3][1]
33010
03010
33110
30010
22011
find_maze_path [2][-1]
33010
03010
33110
20010
22011
find_maze_path [2][1]
33010
03010
33110
20010
22011
find_maze_path [2][2]
33010
03010
23110
20010
22011
find_maze_path [1][2]
33010
03010
23310
20010
22011
find_maze_path [3][2]
33010
03010
23310
20010
22011
find_maze_path [2][1]
33010
03010
23310
20010
22011
find_maze_path [2][3]
33010
03010
23310
20010
22011
find_maze_path [1][3]
33010
03010
23330
20010
22011
find_maze_path [0][3]
33010
03030
23330
20010
22011
find_maze_path [-1][3]
33030
03030
23330
20010
22011
find_maze_path [1][3]
33030
03030
23330
20010
22011
find_maze_path [0][2]
33030
03030
23330
20010
22011
find_maze_path [0][4]
33030
03030
23330
20010
22011
find_maze_path [2][3]
33020
03030
23330
20010
22011
find_maze_path [1][2]
33020
03030
23330
20010
22011
find_maze_path [1][4]
33020
03030
23330
20010
22011
find_maze_path [3][3]
33020
03020
23330
20010
22011
find_maze_path [2][3]
33020
03020
23330
20030
22011
find_maze_path [4][3]
33020
03020
23330
20030
22011
find_maze_path [3][3]
33020
03020
23330
20030
22031
find_maze_path [5][3]
33020
03020
23330
20030
22031
find_maze_path [4][2]
33020
03020
23330
20030
22031
find_maze_path [4][4]
33020
03020
23330
20030
22031
(4,4)
(4,3)
(3,3)
(2,3)
(2,2)
(2,1)
(1,1)
(0,1)
(0,0)
```

From Koffman, Elliot B. (2011-12-01). Objects, Abstraction, Data Structures and Design: Using C++. Wiley Higher Ed. Kindle Edition.
