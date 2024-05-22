**Redis** is a powerful in-memory data structure store that can be used for various use cases due to its speed, simplicity, and versatility. In this tutorial, we'll explore several common use cases: matchmaking, location-based search, job queue, leaderboard, and session store.

If you haven't done so already, you can upload the sample data related to this tutorial by clicking the button below. You will also need support for [JSON](https://redis.io/docs/latest/develop/data-types/json/) and [Search & query](https://redis.io/docs/latest/develop/interact/search-and-query/) in your database to take advantage of this tutorial fully.

```redis-upload:[/uc/sample_data.txt] Upload Sample Data
```

### Matchmaking

Redis serves as a powerful document store, well-suited for matchmaking use cases. For instance, consider a bike shop where you need to pair shoppers with bikes based on their preferences and budget.

You can store your bike inventory using the JSON data structure, where bikes have attributes such as type (e.g., mountain, road, electric), condition (new or used), price, etc. 

```redis:[run_confirmation=true]
// Add a bike as JSON
JSON.SET sample_bicycle:2048 $ '{
  "model": "Ranger",
  "brand": "TrailBlazer",
  "price": 450,
  "type": "Mountain",
  "specs": {
    "material": "carbon",
    "weight": 12
  },
  "description": "The Ranger is designed for rugged trails and adventurous rides!",
  "addons": [
    "water bottle holder",
    "rear rack"
  ],
  "helmet_included": false
  }'
```

In Redis, you can easily index these attributes and perform complex queries efficiently.

```redis:[run_confirmation=true] 
// Create a secondary index of your bike data
FT.CREATE idx:smpl_bicycle 
    ON JSON 
      PREFIX 1 sample_bicycle: 
    SCHEMA 
      $.brand AS brand TEXT
      $.model AS model TEXT  
      $.description AS description TEXT
      $.price AS price NUMERIC 
      $.condition AS condition TAG SEPARATOR , 
      $.type AS type TAG 
      $.helmet_included AS helmet_included TAG 
      $.specs.material AS material TAG 
      $.specs.weight AS weight NUMERIC
```

You can then easily match a user to their preferred type of bike within a requested price bracket.

```redis:[run_confirmation=true]
// Match leisure bikes within a price of 200 and 300
FT.SEARCH idx:smpl_bicycle "@type:{mountain} @price:[400 450]" RETURN 4 brand model type price
```

### Location-based search

Location-based search involves finding and retrieving data that is relevant to a specific geographic location. Redis can be used to power location-based search applications by storing and indexing geospatial data, such as latitude and longitude coordinates, and providing fast and efficient search capabilities

```redis:[run_confirmation=true] 
// Add a restaurant as JSON
JSON.SET sample_restaurant:341 $ '{
  "name": "Zen Galushca",
  "cuisine": "Japanese",
  "location": "-98.1221,30.8232"
  }'
```
```redis:[run_confirmation=true] 
//Create an index of your restaurant data
FT.CREATE "idx:smpl_restaurant"
  ON JSON
    PREFIX 1 "sample_restaurant:"
  SCHEMA
    "$.cuisine" AS "cuisine" TAG
    "$.name" AS "restaunt_name" TEXT
    "$.location" AS "location" GEO
```

```redis:[run_confirmation=true] 
// Find a Japanese restaurant within a 50 mile radius
FT.SEARCH idx:smpl_restaurant  "@cuisine:{japanese} @location:[-98.1179,30.671 50 mi]" RETURN 2 restaunt_name location
```

### Session store

Redis shines as a session store, offering speed and scalability for web applications. Using commands like HSET to store session data as Hash and HGET to retrieve it, Redis ensures rapid access to user sessions.

```redis:[run_confirmation=true]
// Store new session
HSET sample_session:074529275 user_id 7254 username gabe_jones email gabe@example.com last_activity "2024-06-01 10:24:00"

// Set an expiration time for the new session entry
EXPIRE sample_session:074529275 7344000
```

```redis:[run_confirmation=true] 
// Retrieve an existing session
HGETALL sample_session:074529275
```

### Job queue

In many applications, tasks need to be processed asynchronously, such as maintaining a waiting list for a helpdesk where incoming support tickets are queued for processing. A job queue helps manage these tasks efficiently. In Redis, a [job queue](https://redis.io/glossary/redis-queue/) can be implemented using the List data structure. When a new task needs to be queued, it is added to the left end of the list (using LPUSH command). When a worker is ready to process a task, it dequeues it from the right end of the list (using RPOP command). 


```redis:[run_confirmation=true]
// Create a new job as a Hash
HSET sample_jobQueue:ticket:199 id 199 user_id 723 description "Unable to access console" priority "High" created_at "2024-04-20T12:00:00Z"
```
```redis:[run_confirmation=true]
// Enqueue the new job to the waiting list
LPUSH sample_jobQueue:waitingList sample_jobQueue:ticket:199
```
```redis:[run_confirmation=true]
// Show the full list of jobs
LRANGE sample_jobQueue:waitingList 0 -1
```
```redis:[run_confirmation=true]
// Dequeue a job from the list
RPOP sample_jobQueue:waitingList
```

### Leaderboard

[Leaderboards](https://redis.io/solutions/leaderboards/) are crucial for gaming applications to display rankings based on scores. Sorted Sets in Redis are perfect for this purpose due to their ability to store elements with associated scores, which can be easily queried and sorted.

```redis:[run_confirmation=true]
// Add a new score to leaderboard
ZADD sample_leaderboard:tetris 670000 "user100"
```

```redis:[run_confirmation=true]
// Get the top 5 users on the leaderboard, with scores
ZRANGE sample_leaderboard:tetris 0 4 REV WITHSCORES
```
