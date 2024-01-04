## Create JSON documents

Here's a query that creates a JSON document describing a single bike.

```redis Create a JSON document
JSON.SET bicycle:1 $ '{
    "model": "Jigger",
    "brand": "Velorim",
    "price": 270,
    "type": "Kids bikes",
    "specs": {
        "material": "aluminium",
        "weight": "10"
      },
    "description": "Small and powerful, the Jigger is the best ride for the smallest of tikes! This is the tiniest kids\u2019 pedal bike on the market available without a coaster brake, the Jigger is the vehicle of choice for the rare tenacious little rider raring to go. We say rare because this smokin\u2019 little bike is not ideal for a nervous first-time rider, but it\u2019s a true giddy up for a true speedster. The Jigger is a 12 inch lightweight kids bicycle and it will meet your little one\u2019s need for speed. It\u2019s a single speed bike that makes learning to pump pedals simple and intuitive. It even has  a handle in the bottom of the saddle so you can easily help your child during training!  The Jigger is among the most lightweight children\u2019s bikes on the planet. It is designed so that 2-3 year-olds fit comfortably in a molded ride position that allows for efficient riding, balanced handling and agility. The Jigger\u2019s frame design and gears work together so your buddingbiker can stand up out of the seat, stop rapidly, rip over trails and pump tracks. The Jigger\u2019s is amazing on dirt or pavement. Your tike will speed down the bike path in no time. The Jigger will ship with a coaster brake. A freewheel kit is provided at no cost.",
    "addons": [
        "reflectors",
        "grip tassles"
    ],
    "helmet_included": false
    }'
```

Now retrieve the newly created JSON document.

```redis Retrieve bicycle:1
JSON.GET bicycle:1
```

In the above example, the path, which is root (`$`), is implied. You could also write this command as:

```
JSON.GET bicycle:1 $
```

You can also retrieve parts of documents using JSONPath expressions. JSONPath will be discussed in more detail later in this tutorial, but here are a few examples:

```redis Get the price of bicycle:1
JSON.GET bicycle:1 $.price
```

```redis Get the weight of bicycle:1
JSON.GET bicycle:1 $.specs.weight
```

```redis Get the first addon of bicycle:1
JSON.GET bicycle:1 $.addons[0]
```

You can create multiple documents in a single command using `JSON.MSET`:

```redis Add two more documents using JSON.MGET
JSON.MSET "bicycle:2" $ "{\"model\": \"Hillcraft\", \"brand\": \"Bicyk\", \"price\": 1200, \"type\": \"Kids Mountain Bikes\", \"specs\": {\"material\": \"carbon\", \"weight\": \"11\"}, \"description\": \"Kids want to ride with as little weight as possible. Especially on an incline! They may be at the age when a 27.5\\\" wheel bike is just too clumsy coming off a 24\\\" bike. The Hillcraft 26 is just the solution they need! Imagine 120mm travel. Boost front/rear.  You have NOTHING to tweak because it is easy to assemble right out of the box. The Hillcraft 26 is an efficient trail trekking machine. Up or down does not matter - dominate the trails going both down and up with this amazing bike. The name Monarch comes from Monarch trail in Colorado where we love to ride.  It\u2019s a highly technical, steep and rocky trail but the rip on the waydown is so fulfilling.  Don\u2019t ride the trail on a hardtail! It is so much more fun on the full suspension Hillcraft!  Hit your local trail with the Hillcraft Monarch 26 to get to where you want to go.\", \"addons\": [\"reflectors\", \"safety lights\"],\"helmet_included\": false}" "bicycle:3" $ "{\"model\": \"Chook air 5\", \"brand\": \"Nord\", \"price\": 815, \"type\": \"Kids Mountain Bikes\", \"specs\": {\"material\": \"alloy\", \"weight\": \"9.1\"}, \"description\": \"The Chook Air 5  gives kids aged six years and older a durable and uberlight mountain bike for their first experience on tracks and easy cruising through forests and fields. The lower  top tube makes it easy to mount and dismount in any situation, giving your kids greater safety on the trails. The Chook Air 5 is the perfect intro to mountain biking.\", \"addons\": [\"reflectors\", \"safety lights\"],\"helmet_included\": false}"
```

You can retrieve multiple documents or parts of documents in a single command using `JSON.MGET`:

```redis Get bicycle:1 and bicycle:2
JSON.MGET bicycle:1 bicycle:2 $
```

```redis Get the price of all three bicycle documents
JSON.MGET bicycle:1 bicycle:2 bicycle:3 $.price
```

There are two other commands you can use to get information from documents:

```redis Get the length of bicycle:1's description
JSON.STRLEN bicycle:1 $.description
```

```redis Get the type of bicycle:1's helmet_included attribute
JSON.TYPE bicycle:1 $.helmet_included
```

The `JSON.MERGE` can also be used to create new documents. `JSON.MERGE` will be covered in more detail later in this tutorial.