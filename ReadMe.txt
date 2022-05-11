INFO6017 Artificial Intelligence Project 3
Evan Sinasac - 1081418

Built and run in Visual Studios Community 2019 in Debug in x64.  The BMPLoader does not read the bitmaps correctly when run in Release (It reads them in black and white with no colour).  I also had issues with resourceMap3.bmp and the BMPLoader.  After testing a few different things, I realised that it is a size issue, where the size of the bit map needs to be multiples of 8, otherwise it gets some funny numbers while reading it.  I made a copy of resourceMap3.bmp and made it 56x56 instead 54x54 and now it works in the code :)


CONTROLS
WASD			- Move Camera
QE			- Raise/Lower Camera
Mouse			- Aim Camera
1-0			- Graphics effects (leftovers from code I built off of)
P			- Reset FBO resolution (Press whenever re-sizing the window)
B			- Show Debug Normals
M			- Make Meshes Wireframe
Space			- Static Cam Position (leftovers from code I built off of, not useful, really should've removed it)
KEYPAD 1/2/3		- Gatherer movement speed multiplier

A.I.
Gatherer Entities use a Finite State Machine (modified from the one given in class) to determine what kind of behaviour to perform.  When the FSM ends the entities remain at rest wherever they realize there are no resources to gather.  Slight bug when they can't find an actual path Dijkstra's wasn't returning NULL so the program would crash when determining the actual path to follow.  (so line 99 in SearchState.cpp, targetNode wouldn't be NULL and the program would continue into the do while, where it would crash saying targetNode is null.  I was checking for nullptr before and just changed it to NULL, so hopefully it doesn't crash anymore.  {I actually tested it again after changing it to NULL, it seems to work now, I think it was because I was comparing it to nullptr instead}).

FINITE STATE MACHINE
Modified the given state machine form the one given to us in class.  The FSMSystem behaves the same as in class, but I pass the Entity's position into the Update, then the System passes this along into each state for different behaviours.  
The IdleState behaves as the enter state, and passes into the SearchState almost immediately.  
SearchState OnEnter determines the path from the current node to the nearest node with the hasGoal true using Dijkstra.  I'm using hasGoal as an indication that a resource is at that node.  Then in Update, it moves from position to position until the path is empty (indicating that the entity has arrived at the resource).  When it arrives at the resource it double checks that the resource is still there (checking that another entity didn't get it first), if it's still there then it transitions to the GatherState.
GatherState OnEnter tells the node the entity is at that it is no longer a goal and resets the timer.  The entity waits 4 seconds and then gathers the resource (indicated by changing the mesh colour of the resource) and then transitions to ReturnState.
The ReturnState uses AStar to find the path from the current node to the homeBase (I added a new bool to the Nodes to indicate that the node is the homeBase and changed AStar to check this boolean instead of the hasGoal).  Then it uses the same methods as the SearchState to move along the path and return to the base.  When it reaches the base it waits 2 seconds and then transitions back to the SearchState.
When there are no more resources available to be gathered, the SearchState transitions to an empty state and no longer performs any behaviours.

GRAPHS
The only real changes I made to the graphs code from class is that I added a char type and a bool isHomeBase to the Node which are set while reading the bmp and making the graph.  And as mentioned before, I changed AStar to check for isHomeBase instead of hasGoal.  
I decided the easiest way to make the graph would be to just make a Node at every point in the bmp (I'm using meshes that are 1x1 centered axes), so I can position them at the same spot as the bmp coordinates.  Then I go through every Node in the graph in a pair of nested for loops, checking the physical positions of each Node compared to the current Node.  If the second Node fits the requirements for an edge, then we make a bi-directional edge (weight determined during making the edge if either node is difficult terrain).  Base weight is 10 if the nodes are horizontal or vertical of each other, 14 if diagonal.  Doubled towards a yellow node, not doubled out of a yellow node.
While making the graph, the blue square is indicated as the home base, red squares are marked as hasGoal (they have a resource), green squares are marked as spawn points, and all squares are marked with their colour.  White is normal terrain, black is non-traversable, yellow is difficult terrain.  After the graph and edges are made, I make an entity at each spawn point.

RESOURCE MAPS
I have placed my resource maps with my other textures located at SOLUTION_DIR\common\assets\textures\<resourceMap.bmp>
To change which map is being used, lines 94-101 in the main.cpp are commented out string streams of the various maps I have.  To use a new map, you can either place it in the PROJECT_DIR and use the commented out line 103 as the format. Or, place the map in the textures folder, and then copy/change one of the string stream lines to have your file's name instead of the other bit maps.  Make sure only one of the streams is not commented at a time (if line 94 & 95 were uncommented the string would become 
"SOLUTION_DIRcommon\\assets\\textures\\resourceMap.bmpSOLUTION_DIRcommon\\assets\\textures\\resourceMap2.bmp")

I think that's about it.  Demo video should be included with the project.  Thank you, enjoy!

Video Demo: https://youtu.be/WsycHO0dL84
Addendum: https://youtu.be/eNJBPiqeeyc
 