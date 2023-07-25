Since this was a group project on a private repository, I can only share a description of what we have done to respect the privacy of my colleagues, sorry!

# 42_lem-in


This project was done as part of my education at Codam, part of the 42 network of coding schools.

This program was written in collaboration with Kim de Winter.

<h3>Assignment</h3>

The object of this assignment is to write a program that can guide any number of ants through an ant farm in as few rounds as possible where:
1. With the exception of the starting room and the end room, a room can contain only one ant at a time
2. An ant can only move up one room every round
3. A round is defined as the collection of moves made by every ant that moves that turn

The full assignment can be found here: [lem_in subject](https://github.com/LER1990/42_subjects/blob/master/lem-in.en.pdf)

<h3>Process</h3>

Since this problem seemed to be a maximum-flow type of challenge, we started by implementing a variation on the [Edmonds-Karp algorithm](https://en.wikipedia.org/wiki/Edmonds%E2%80%93Karp_algorithm).
Using this method, we collected all possible paths from the start to the end. After that, we looked for combinations of parallel paths, calculated how many rounds these
configurations would take to get all the ants to the end and chose the best option.

The problem with this approach was that the number of possible combinations grows exponentially with the number of rooms in the network. In the end, this approach turned 
out to be too time complex. We concluded that we needed an approach that would eliminated the need to combine paths after the fact. In other words: we wanted to find a way
to build combinations of paths rather than all possible paths, so that we would only have to compare any combination we found to the best combination we had found so far. 
This brought us to our final solution.

<h3>Solution</h3>

In the end we used a variation on [Dinic's algorithm](https://en.wikipedia.org/wiki/Dinic%27s_algorithm). The algorithm, in a nutshell, looks like this:
1. We create a level graph from the end room to the start room.  
Afterwards, we know for every room connected to start how close it is (if we follow the shortest path) to the end.
2. Starting with the most promising neighbour of start (the one with the shortest distance to end, if there is more then one, some other criteria are used),
we perform a depth first search, selecting the most promising continuation of a path at every level, until we reach the end room or we run out of options.
Rooms that are already in a path that is part of the current combination of paths, are not available to be used in another path. This way we ensure paths in a 
combination are always parallel.
  If we find a path, we add it to the current combination of paths. Then we create a new level graph like in step 1, excluding the rooms that are already in a path.
  Otherwise, we continue on to the next option until we find a path or run out of options.
3. Once adding a found path doesn't improve the number of rounds this combination of paths will take anymore, we stop looking for paths.
4. Once our combination of paths is complete, we compare it to the previous best combination we found and select one.
5. The next step is to create a level graph with the distances from every room to the start room. After that, we look for the most promising neighbour of the end room 
(the one with the shortest distance to the start room) that is not yet in a path. We follow this path depth-first until we find a room that is already in a path (known 
as a blockage) or we find that such a room doesn't exist. If we cannot find any blockage, we are done. If we do find a blockage, we remove the connection from the
blockage to the next room in the path it is in. Then we start the entire algorithm again from step 1.

<h3>The Program</h3>

The use of this program is fairly simple as it's imput validating is very strong. To compile the program use the Make command

```
Make

./lem-in

./lem-in < a_valid_map
```

Here is an example of a valid map file:

```
4 <(number of Ants as an Integer)
#this is a comment <(single Hashtags are used for comments)
1 -1 2 v(rooms in format: Name X Y)
2 1 2
3 1 2
4 1 2
5 1 2
6 1 2 ^
##start <(double Hastag signals importance only used for start and end)
S 1 2	<(start room coordinates)
##end
E 1 2 <(end room coordinates)
S-1 v(Links in format: Roomname1-Roomname2)
S-2
2-3
2-5
1-4
3-6
4-5
#comments can be anywhere
5-6
6-E
5-E ^
```

Here is a Gif of the program in action:

![Gif](https://media.giphy.com/media/LBGVIkyEdvkykcwRQL/giphy.gif)

All Ants have made it from ##start to ##end
