# Lab Summary
The A* algorithm is developed on the base of [this](https://www.youtube.com/watch?v=-L-WgKMFuhE&list=PLFt_AvWsXl0cq5Umv3pMC9SPnKjfp9eGW&index=1)
tutorial (Note that the heuristic method isn’t used in the code of this script.
<br>To better understand the heuristic and A* see [here](http://theory.stanford.edu/~amitp/GameProgramming/).
## Steps for using the project
1.	Add object and target in the manager script, check manager script is attached to the game object manager, and A* script is attached to game object A*.
<div align=center><img width="400" height="250" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/1.png"/></div>

<br><div align=center><img width="400" height="45" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/2.png"/></div>

2.	Add object in unity, in this scene, there are 2 types of game object
	* 3D object for obstacle, walls, and floor (Create->3D object->Cube)
	* Empty game object for A*, Manager, Target (Create->Create Empty)
<br><div align=center><img width="400" height="150" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/3.png"/></div>
3.	Attach the Unit script to the object, drag the target to the holder of “Target” in unit script
<br><div align=center><img width="400" height="120" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/4.png"/></div>
4.	Set all the collideable object’s layer to a special layer in the inspector window, including floor, objects, target (in my project the layer is called Unwalkable Layer
<br><div align=center><img width="400" height="120" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/5.png"/></div>
5.	Drag different object to the holder of “object” and “target” in game object manager’s manager script, select the layer for the unwalkable layer
<br><div align=center><img width="400" height="350" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/6.png"/></div>
6.	Note that the target should be carefully selected to avoid collision in the end
## Basic flow of the algorithm (Right side is for the current code)
<br><div align=center><img width="600" height="300" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/7.png"/></div>
## Currently there is some limit for the current algorithm in the following aspects:

* Time consuming for more walls.
* Smaller grid radius promises a secure performance avoiding collision, but will sacrifices time to do so.
* Target need to be pre-selected in case of collision between moved walls.
* Interface is complex and not that easy to use.
	
## Things needed to be considered for future study are listed below:

* Add rotation part to the algorithm.
* Use data structure or better method in Unity to refine the algorithm.
* Improve the interface especially in the part of adding walls to the scene, right now its too cumbersome
* Apply ML agent to the disassemble problem.
## Following are an explanation of how the algorithm goes
## Implement A* path finding algorithm

There are four steps for implementing the A* algorithm in the assembly situation (see Figure 1).
<br>**First**, a grid was setup to create all the available nodes in our world environment. 
<br>**Next**, the heuristic (h) value of the formula was modified and marked whether each node was walkable. For this step, three aspects need to be taken into consideration. 

* The Physics function in Unity detects the nodes occupied by an object on the plane. These nodes are assigned 
	<br>a high h value to mark the node as unwalkable
* The unoccupied nodes are then checked to see which ones will cause a collision between walls if the to-move 
	<br>wall is on the node. The number of nodes a wall occupies on either side of its center is stored, and the 
	<br>Physics function is used to determine if a collision occurs as the wall moves to a specific node. 
	<br>If a collision occurs at a node, the node is assigned a high h value.
* All the other nodes are safe to move on. They are assigned a low h value to mark them as walkable.

<br>**Third**, start A* pathfinding. Beginning with the start node, the algorithm will select the neighbor (collection of nodes) 
with the lowest f value before each step. Note, since we already modified the h value of each node and we know the formula
of calculating f we can easily predict that the algorithm will automatically avoid selecting the neighbor node with a high h value. 
<br>**Fourth**, if the neighbor node being moved to is the target node, the algorithm has successfully found the path. End the A* algorithm 
and store the nodes generated by A* into the list path for future use.
<br><div align=center><img width="400" height="250" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/8.png"/></div>

## Disassemble process

The above algorithm can successfully generate a path for each wall regardless of whether there is any unwalkable nodes in the environment. We can apply the algorithm in the situation like assembly when we assemble the walls one by one and already know the correct order. However, this algorithm will fail in the disassemble situation because there may be some unwalkable nodes in the path calculated as a result. For example, wall A is blocked by wall B and wall C, we have to move wall B and C first before we move wall A. 
<br>The method we used to solve this problem was to create a queue at the very beginning to store all the walls we want to move in a sequence, note that the sequence may not be identical for moving without collision, but the algorithm will go through the walls stored in the queue in sequence while running. Every time we visited the first wall stored in the queue and get the path generated by A*, if there is any node marked unwalkable in the path list, which means we cannot move this wall right now, we deleted this wall member from the queue and add it to the last member of the queue.
<br>One step needs to be added in the part of setting heuristic value is that the original algorithm will mark the nodes the current wall occupies as unwalkable and will cause the result that no wall could move at all.
For every process, we moved the wall to the target position and deleted this wall member from the queue making the number of the walls in the queue to decrease. The algorithm will continue running until the number of walls in the queue is zero, meaning all the walls we wanted to move are in their target position. 
<br>The whole process is shown in the picture below (see Figure 2). With this process, we are able to predict the right sequence for moving all the walls. For example, in the figure below (see Figure 3), the wall marked with stripes was stored as the 3rd wall the original sequence of the queue, but it will cause collision with the dotted wall if we don’t move the dotted wall from its original position. Following the process of the algorithm, we will skip the striped wall first by deleting it from the current position and add to the last position of the queue to see if we can generate an identical path for it later.
<br><div align=center><img width="400" height="250" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/9.png"/></div>
<br><div align=center><img width="500" height="150" src="https://github.com/LOYOJO990618/path-planning-astar/blob/master/SmallExample/Picture/10.png"/></div>


