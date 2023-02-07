`algo.SPpaths` and `algo.SSpaths` can solve a wide range of real-world problems, where minimum-weight paths need to be found. `algo.SPpaths` finds paths between a given pair of nodes, while `algo.SSpaths` finds paths from a given source node. Weight can represent time, distance, price, or any other measurement. A bound can be set on another property (e.g., finding a minimum-time bounded-price way to reach from point A to point B). Both algorithms are performant and have low memory requirements.

For both algorithms, you can set:

* A list of relationship types to traverse (relTypes).

* The relationships' property whose sum you want to minimize (weight).

* An optional relationships' property whose sum you want to bound (cost) and the optional bound (maxCost).

* An optional bound on the path length - the number of relationships along the path (maxLen).

The number of paths you want to retrieve: either all minimal-weight paths (pathCount is 0), a single minimal-weight path (pathCount is 1), or n minimal-weight paths with potentially different weights (pathCount is n).

This tutorial shows you how to use these algorithms.


## Step 1: Create graph

Analyze the query to create a graph. Note how nodes and relationships are created:

```redis Create a graph
GRAPH.QUERY city_graph "CREATE 
    (a:City{Name:'A'}), 
    (b:City{Name:'B'}), 
    (c:City{Name:'C'}), 
    (d:City{Name:'D'}), 
    (e:City{Name:'E'}), 
    (f:City{Name:'F'}), 
    (g:City{Name:'G'}), 
    (a)-[r1:ROAD_TO{Time:4, Dist:3}]->(b), 
    (a)-[r2:ROAD_TO{Time:3, Dist:8}]->(c), 
    (a)-[r3:ROAD_TO{Time:4, Dist:2}]->(d), 
    (b)-[r4:ROAD_TO{Time:5, Dist:7}]->(e), 
    (b)-[r5:ROAD_TO{Time:5, Dist:5}]->(d), 
    (d)-[r6:ROAD_TO{Time:4, Dist:5}]->(e), 
    (c)-[r7:ROAD_TO{Time:3, Dist:6}]->(f), 
    (d)-[r8:ROAD_TO{Time:1, Dist:4}]->(c), 
    (d)-[r9:ROAD_TO{Time:2, Dist:12}]->(f), 
    (e)-[r10:ROAD_TO{Time:5, Dist:5}]->(g), 
    (f)-[r11:ROAD_TO{Time:4, Dist:2}]->(g) 
    return a,b,c,d,e,f,g,r1,r2,r3,r4,r5,r6,r7,r8,r9,r10,r11"
```

## Fastest path between A and G

What is the fastest path (in minutes) from A to G?

```redis Find fastest path
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'A'}),(g:City{Name:'G'}) 
    CALL algo.SPpaths({
        sourceNode: a,
         targetNode: g, 
         relTypes: ['ROAD_TO'], 
         weightProp: 'Time'}) 
    YIELD path, pathWeight 
    RETURN pathWeight, [n in nodes(path) | n.Name] as pathNodes"
```

## Find all shortest paths between A and G

What are all the shortest paths (in kilometers) from A to G?

```redis Find all shortest paths
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'A'}),(g:City{Name:'G'}) 
    CALL algo.SPpaths({
        sourceNode: a, 
        targetNode: g,
        relTypes: ['ROAD_TO'], 
        pathCount: 0, 
        weightProp: 'Dist'}) 
    YIELD path, pathWeight 
    RETURN pathWeight, [n in nodes(path) | n.Name] as pathNodes"
```

By specifying `pathCount:0` you're asking for all minimal-weight paths instead of only one, which is the default.

## Find N shortest paths between A and G

What are the five shortest paths (in kilometres) from A to G?

```redis Find N shortest paths
GRAPH.QUERY city_graph "MATCH
    (a:City{Name:'A'}),(g:City{Name:'G'}) 
    CALL algo.SPpaths({
        sourceNode: a, 
        targetNode: g, 
        relTypes: ['ROAD_TO'], 
        pathCount: 5, 
        weightProp: 'dist'})
    YIELD path, pathWeight 
    RETURN pathWeight, [n in nodes(path) | n.Name] 
    ORDER BY pathWeight"
```

## Find N time-bounded shortest paths from A to G

Which two shortest paths (in kilometers) can you take from A to G, where you can reach G in up to 12 minutes?

For queries where you need to find the shortest paths bounded by one of the relationships' properties, you can use the `costProp` input argument:

```redis Find N time-bounded shortest paths from A to G
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'A'}),(g:City{Name:'G'}) 
    CALL algo.SPpaths({
        sourceNode: a, 
        targetNode: g, 
        relTypes: ['ROAD_TO'], 
        pathCount: 2, 
        weightProp: 'Dist', 
        costProp: 'Time', 
        maxCost: 12}) 
    YIELD path, pathWeight, pathCost 
    RETURN pathWeight, pathCost, [n in nodes(path) | n.Name] 
    ORDER BY pathWeight"
```

## Find paths that revert or ignore the relationship direction

What paths with lengths of up to 4 can you take from D to G, assuming you can traverse each road in both directions?

```redis Find paths with specific lengths
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'D'}),(g:City{Name:'G'}) 
    CALL algo.SPpaths({
        sourceNode: a, 
        targetNode: g, 
        relTypes: ['ROAD_TO'], 
        relDirection: 'both', 
        pathCount: 1000, 
        weightProp: 'Dist',
        maxLen: 4} ) 
    YIELD path, pathWeight 
    RETURN pathWeight, [n in nodes(path) | n.Name] as pathNodes ORDER BY pathWeight"
```

In the query above, you specified `maxLen: 4`, limiting the number of hops between nodes.

## Find time-bounded possible shortest paths from A

Which paths can you take from A if you limit the trip to 8 minutes?

```redis Find time-bounded shortest paths from A
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'A'}) 
    CALL algo.SSpaths({
        sourceNode: a, 
        relTypes: ['ROAD_TO'], 
        pathCount: 1000, 
        costProp: 'Time', 
        maxCost: 8}) 
    YIELD path, pathCost 
    RETURN pathCost, [n in nodes(path) | n.Name] as pathNodes ORDER BY pathCost"
```

## Find possible shortest paths from a node

What five shortest paths (in kilometers) can you take from A?

```redis Find shortest paths from a node
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'A'}) 
    CALL algo.SSpaths({
        sourceNode: a, 
        relTypes: ['ROAD_TO'], 
        pathCount: 5, 
        weightProp: 'Dist', 
        costProp: 'Cost'} ) 
    YIELD path, pathWeight, pathCost 
    RETURN pathWeight, pathCost, [n in nodes(path) | n.Name] as pathNodes 
    ORDER BY pathWeight"
```

## Find N time-bound shortest paths from A

What five shortest paths (in kilometers) can you take from A if you limit the trip to 6 minutes?

```redis Find N time-bound shortest paths
GRAPH.QUERY city_graph "MATCH 
    (a:City{Name:'A'}) 
    CALL algo.SSpaths({
        sourceNode: a, 
        relTypes: ['ROAD_TO'], 
        pathCount: 5, 
        weightProp: 'Dist', 
        costProp: 'Time', 
        maxCost: 6}) 
    YIELD path, pathWeight, pathCost 
    RETURN pathWeight, pathCost, [n in nodes(path) | n.Name] as pathNodes ORDER BY pathWeight"
```

