Redis Stack offers native graph capabilities. You can use graphs for highly interconnected data, like relationships between people, organizations, groups, documents, or places they have access to and so on.

Suppose you want to track which bikes users bought so you can suggest them based on the fact that their friends have also bought them. 

## Create nodes

This query creates a single bike node and sets its properties.

```redis Create a bike node
GRAPH.QUERY bikes_graph 'CREATE (b:Bike { 
    Brand:"Velorim", 
    Model:"Jigger", 
    Price:"270", 
    Type: "Kids\' bikes" })
    RETURN b'
```

Now, load more bikes.

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

Let's create some users.

```redis Create users
// Let's create some user nodes
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Andrea"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Alicia"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Mathew"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Noah"})'
GRAPH.QUERY bikes_graph 'CREATE (u:User { Name:"Mario"})'
```

## Add relationships

Model graph data:

- A user makes a transaction.
- That transaction contains a bike.  

You already have `User` and `Bike` nodes. You're only missing transactions. Let's create them.
You also need to establish the relationships between all the nodes. Match the existing nodes, save them in a variable (b, u, t), and use that variable to create the relationships.

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

Let's create a `REVIEWED` relationship between some users and bikes. The relationship has a `Stars` property that shows the number of stars that the user assigned to the bike and a `ReviewID` property that points you to the document that contains the review.

```redis Model users reviewing bikes
GRAPH.QUERY bikes_graph '
    MATCH (u:User {Name: "Noah"}), 
    (b:Bike { Model: "Hillcraft"}) 
    CREATE (u)-[r:REVIEWED {ReviewID: 123, Stars: 5}]->(b)'


GRAPH.QUERY bikes_graph 'MATCH (u:User {Name: "Mathew"}), (b:Bike { Model: "XBN 2.1 Alloy"}) CREATE (u)-[r:REVIEWED {ReviewID: 234, Stars: 4}]->(b)'
GRAPH.QUERY bikes_graph 'MATCH (u:User {Name: "Mario"}), (b:Bike { Model: "Hillcraft"}) CREATE (u)-[r:REVIEWED {ReviewID: 123, Stars: 3}]->(b)'
```

Users of the bike shop are able to follow each other so they can get updates on their recent activity.

```redis Users can follow each other
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Andrea"}), (u2:User {Name: "Noah"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Andrea"}), (u2:User {Name: "Alicia"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Andrea"}), (u2:User {Name: "Mario"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Mathew"}), (u2:User {Name: "Mario"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
GRAPH.QUERY bikes_graph 'MATCH (u1:User {Name: "Mario"}), (u2:User {Name: "Andrea"}) CREATE (u1)-[r:FOLLOWS]->(u2)'
```

## Use graph to discover how data is related

When a user accesses a bike page, you can increase the probability of a sale by showing the relationships that exist between the bike and the user. For example, someone your user follows might have already bought the bike or might have reviewed it. 
This is very tricky to query with a relational database, but you can easily query it using a graph database.

```redis Check user's connection with a bike
GRAPH.QUERY bikes_graph 'MATCH p=(u:User {Name: "Andrea"})-[r*1..5]->(b:Bike {Model: "Hillcraft"}) return p'
```

```redis All users who I follow who reviewed this bike
GRAPH.QUERY bikes_graph 'MATCH p=(u1:User {Name: "Andrea"})-[f:FOLLOWS]->(u2:User)-[r:REVIEWED]->(b:Bike {Model: "Hillcraft"}) return p'
```

```redis All users who I follow who reviewed this bike with more than 3 stars
GRAPH.QUERY bikes_graph 'MATCH p=(u1:User {Name: "Andrea"})-[f:FOLLOWS]->(u2:User)-[r:REVIEWED]->(b:Bike {Model: "Hillcraft"}) WHERE r.Stars>3  return p'
```