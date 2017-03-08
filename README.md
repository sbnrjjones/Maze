# maze
In this learning activity, we will examine the code from the book that explores a 2D maze.  This experience may help you in developing the code for lab 5 to explore a 3D maze.

To get started, lets build out enough of the lab 5 infrastructure to call the book code.  Start by downloading the lab 5 interface files and creating a Pathfinder class in "Pathfinder.h" that inherits from PathfinderInterface.  Then create "Pathfinder.cpp" that includes "Pathfinder.h"

Now create a sample maze for us to explore in 2d.txt
```
1 1 0 1 0
0 1 0 1 0
1 1 1 1 0
1 0 0 1 0
1 1 0 1 1
```
Locations where there is a 1 are open pathways, locations with a zero are walls in the maze.

Now create code to read in the maze.  First, we will need an array that we can use to hold the maze.  Put it in "Pathfinder.h".
```c++
protected:
  const int ROW_SIZE = 5;
  const int COL_SIZE = 5;
	int maze_grid[ROW_SIZE][COL_SIZE]; // To hold values
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
Now call this code from main.cpp and make sure things are working.
