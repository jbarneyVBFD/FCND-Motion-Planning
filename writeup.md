## Project: 3D Motion Planning

---


# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations!  Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
These scripts contain a basic planning implementation that includes...

The path is planned by the programmer in the function calculate_box in the BackyardFlyer class. In the MotionPlanning class the path is planned in the plan_path function. Also in the plan_path function, obstacles are read into the map through the colliders.csv file. The file planning_utils.py offers functions that help to create the grid and plan the path. The function a_star checks the current node for adjacent grid cells and uses the function valid_actions to check the adjacent cells for obstacles and edges of the map. The function a_star then assigns a cost to each of the grid cells leftover from valid_actions. The PriorityQueue is then used to select the adjacent grid cell with the lowest cost to be the new current_node. When the PriorityQueue returns the goal, "Found a path" is printed, the boolean found is set to true, and break is called to exit the while loop.


Here's | A | Snappy | Table
--- | --- | --- | ---
1 | `highlight` | **bold** | 7.41
2 | a | b | c
3 | *italic* | text | 403
4 | 2 | 3 | abcd

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
Here students should read the first line of the csv file, extract lat0 and lon0 as floating point values and use the self.set_home_position() method to set global home. Explain briefly how you accomplished this in your code.

I read the file colliders.csv with the open function from the built in functions of the standard library. Then I appended each row into a list by splitting by each newline. After looking at a print of the new list to 
locate where the lat and lon were indexed, I split the file further by spaces and used the indices to place them in the float objects named lat0 and lon0.

Once I had both lat0 and lon0, I used the Drone class function, set_home_position to set the home position to lat0, lon0, 0.


#### 2. Set your current local position
Here as long as you successfully determine your local position relative to global home you'll be all set. Explain briefly how you accomplished this in your code.

I first placed self.global_position into current_global_position. I then used the function global_to_local from the udacidrone.frame_utils file 
to convert the variable current_local_position.


#### 3. Set grid start position from local position
This is another step in adding flexibility to the start location. As long as it works you're good to go!

I created variables grid_start_north and grid_start_east then int casted with numpy.ceil to round up and took the difference of the first and second indices against the north and
east offsets for each. I then updated the grid_start variable to contain grid_start_north, and grid_start_east. 

#### 4. Set grid goal position from geodetic coords
This step is to add flexibility to the desired goal location. Should be able to choose any (lat, lon) within the map and have it rendered to a goal location on the grid.

After entering the latlong into the variable latlonG, I used the global_to_local function, from udacidrone.frame_utils, to convert to a north, east down coordinate. I then set grid_goal to the difference of the north and east coordinates against the north and east offsets while casting them as integers.  

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
Minimal requirement here is to modify the code in planning_utils() to update the A* implementation to include diagonal motions on the grid that have a cost of sqrt(2), but more creative solutions are welcome. Explain the code you used to accomplish this step.

I added the four diagonal directions to the class enumeration Action, each with the cost of the sqrt(2).
I modified the function valid_actions by adding the following four if statements, NE, NW, SE, and SW; to check to see if the diagonal motion is on the map and not an obstacle.

#### 6. Cull waypoints 
For this step you can use a collinearity test or ray tracing method like Bresenham. The idea is simply to prune your path of unnecessary waypoints. Explain the code you used to accomplish this step.

I defined a point function that converts the point into a numpy array of the waypoint coordinate reshaped to add an extra dimension to make concatenation easier in the function collinearity. 

I then defined the function collinearity to take in 3 waypoints and an epsilon, then convert the waypoints to numpy arrays and add the extra dimension through the point function; then concatenate them into a matrix. I then found the determinate of the three points by using the numpy linalg function det. Next I compared the determinate to the epsilon returning a boolean.

In the prunePath I first checked to see if the path was not empty. Next I started a while loop to loop through the length of the path, three waypoints at a time, calling the collinearity function through an if statement checking to see if the three waypoints passed in returned true in the collinearity function. If they did I removed the middle waypoint. If they returned false, I increased the index value to move further along in the while loop. Once the while loop reached the end of the path, I returned the path.

### Execute the flight
#### 1. Does it work?
It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
  
# Extra Challenges: Real World Planning

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to invoke if you get off course or encounter unexpected obstacles.


