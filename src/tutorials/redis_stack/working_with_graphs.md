Redis Stack offers native graph capabilities. You can use graphs for highly interconnected data, like relationships between people, organisations, groups, documents or places they have access to and so on.

For our shop, we would like to track which users have bought what so that we can suggest bikes based on the fact that their friends have also bought them. 

## Creating nodes

This query will create a single bike node and set its properties
```redis Create a bike node
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { 
    Brand:"Velorim", 
    Model:"Jigger", 
    Price:"270", 
    Type: "Kids\' bikes" })
    RETURN b'
```

```redis Load more bikes
// Let's load some more bikes
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"Bicyk", Model:"Hillcraft", Price:"1200", Type: "Kids Mountain Bikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"Nord", Model:"Chook air 5", Price:"815", Type: "Kids Mountain Bikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"Eva", Model:"Eva 291", Price:"3400", Type: "Mountain Bikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"Breakout", Model:"XBN 2.1 Alloy", Price:"810", Type: "Road Bikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"ScramBikes", Model:"WattBike", Price:"2300", Type: "eBikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"Peaknetic", Model:"Soothe Electric bike", Price:"1950", Type: "eBikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"Peaknetic", Model:"Secto", Price:"430", Type: "Commuter bikes" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"nHill", Model:"Summit", Price:"1200", Type: "Mountain Bike" })'
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { Brand:"BikeShind", Model:"ThrillCycle", Price:"815", Type: "Commuter Bikes" })'
```

```redis Create users
// Let's create some user nodes
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Andrea"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Alicia"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Mathew"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Noah"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Mario"})'
```

## Adding relationships
We model graph data very similarly to how we would describe it in a human language:
- A user makes a transaction
- That transaction contains a bike  
We already have User and Bike nodes, we're only missing the Transactions, so let's create them.
We also need to establish the relationships between all the nodes; we do that by matching the existing nodes, saving them in a variable (b, u, t) and using that variable to create the relationships

```redis Model bike sales
GRAPH.QUERY bikes_graph '
    MATCH (b:Bike { Model: "Chook air 5"}), (u:User {Name: "Noah"}) 
    CREATE (t:Transaction {Value: 815 }) 
    CREATE (u)-[r1:MADE]->(t) 
    CREATE (t)-[r2:CONTAINS]->(b)'
```

Let's load some more relationships:
```redis Load more bike sales
GRAPH.QUERY bikes_graph 'MATCH (b:Bike { Model: "Hillcraft"}), (u:User {Name: "Alicia"}) CREATE (t:Transaction {Value: 1200 }) CREATE (u)-[r1:MADE]->(t) CREATE (t)-[r2:CONTAINS]->(b)'
GRAPH.QUERY bikes_graph 'MATCH (b:Bike { Model: "ThrillCycle"}), (u:User {Name: "Andrea"}) CREATE (t:Transaction {Value: 815 }) CREATE (u)-[r1:MADE]->(t) CREATE (t)-[r2:CONTAINS]->(b)'
GRAPH.QUERY bikes_graph 'MATCH (b:Bike { Model: "XBN 2.1 Alloy"}), (u:User {Name: "Mathew"}) CREATE (t:Transaction {Value: 810 }) CREATE (u)-[r1:MADE]->(t) CREATE (t)-[r2:CONTAINS]->(b)'
```

Let's create a REVIEWED relationship between some users and bikes. The relationship will have a "Stars" property that will show the number of stars that the user assigned to the bike and a "ReviewID" property which will point us to the document that contains the review 
```redis Model users reviewing bikes
GRAPH.QUERY bikes_graph '
    MATCH (u:User {Name: "Noah"}), 
    (b:Bike { Model: "Hillcraft"}) 
    CREATE (u)-[r:REVIEWED {ReviewID: 123, Stars: 5}]->(b)'


GRAPH.QUERY bikes_graph 'MATCH (u:User {Name: "Mathew"}), (b:Bike { Model: "XBN 2.1 Alloy"}) CREATE (u)-[r:REVIEWED {ReviewID: 234, Stars: 4}]->(b)'
GRAPH.QUERY bikes_graph 'MATCH (u:User {Name: "Mario"}), (b:Bike { Model: "Hillcraft"}) CREATE (u)-[r:REVIEWED {ReviewID: 123, Stars: 3}]->(b)'
```

Users of our bike shop will be able to follow each other so they can get updates on their recent updates
```redis Users can follow each other
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Andrea"}), (u2:User {Name: "Noah"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Andrea"}), (u2:User {Name: "Alicia"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Andrea"}), (u2:User {Name: "Mario"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Mathew"}), (u2:User {Name: "Mario"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Mario"}), (u2:User {Name: "Andrea"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
```

## Utilising the graph for discovering how data is related
When a user is viewing a page of a bike, we can increase the probability of a sale by showing the relationships that exist between the bike and the user, for example, someone our user follows might have bought the bike already, or might have reviewed it. 
This is very easy to query with a graph database but very tricky with a relational database.

```redis Check user's connection with a bike
GRAPH.QUERY bikes_graph 'MATCH p=(u:User {Name: "Andrea"})-[r*1..5]->(b:Bike {Model: "Hillcraft"}) return p'
```

```redis All users who I follow who reviewed this bike
GRAPH.QUERY bikes_graph 'MATCH p=(u1:User {Name: "Andrea"})-[f:FOLLOWS]->(u2:User)-[r:REVIEWED]->(b:Bike {Model: "Hillcraft"}) return p'
```

```redis All users who I follow who reviewed this bike with more than 3 stars
GRAPH.QUERY bikes_graph 'MATCH p=(u1:User {Name: "Andrea"})-[f:FOLLOWS]->(u2:User)-[r:REVIEWED]->(b:Bike {Model: "Hillcraft"}) WHERE r.Stars>3  return p'
```