## Introduction
![Tutorials UI](docs/tutorials.png)

The Tutorials are documents that contain helpful use cases, comments and interesting experience about Redis features and capabilities.

The content of the Tutorials can be updated autonomously without a need to update the entire application.

This document provides an overview of the Tutorials structure, its elements and contains instructions, recommendations, and best practices for updating the content of Tutorials.



## Navigation

1. [Structure](#Structure)
2. [Pages](#Pages)
3. [Autoupdate Flow](#Autoupdate)
4. [Development Flow](#Development)

## Structure
Tutorials allows you to render recursive objects, such as a file directory.

On the root level of [sources](https://github.com/RedisInsight/Tutorials/tree/main/src) folder, we have `tutorials.json` and all necessary static files (markdowns, images, etc.)

The content of this area is generated based on Nodes specified inside `tutorials.json`.
This JSON file is described as a simple [Object](https://javascript.info/object) (Hash map), where key is a **string** and value is a **Node** (`Record<string, Node>`). Each Node requires a `label`,`type` and  a unique `id` (all available properties are described in the table below).

| Prop                 | Type                                    | Description |
| -------------------- | --------------------------------------- | ----------- |
| id (**required**)    | string                                  |             |
| type (**required**)  | "group", "internal-link", "code-button" |             |
| label (**required**) | string                                  | Label that will be displayed on UI for Node |
| children             | Record<string, Node>                    | Use only for "group" type |
| args                 | Record<string, any>                     | A special set of parameters required by a certain type of node (each type has its own)             |

A Node can be represented by various UI components and is specified by `type` prop. Supported types are listed below.

| Type            	| Description                                                                         	| Args                                                                                                                                                                         	|
|-----------------	|-------------------------------------------------------------------------------------	|--------------	|
| **"internal-link"** 	| A link that opens a page inside the Tutorials <br> (e.g. link to Redis Stack tutorial).   | - "path" (required, string) - relative file path                                                                                                              |
| **"code-button"**   	| A button that inserts content into the Code Editor. | - "path" (required, string) - relative file path                                                                                                                             	|
| **"group"**         	| Grouping several nodes into one directory/folder. <br> Allows you to create nested lists | - "initialIsOpen" (boolean) - The group will start in the open state (default value - false) <br> - "withBorder" (boolean) - Display border at the bottom (default value - false) 	|

> _**!Note.** All **"internal-link"** nodes located in the same **"group"** will be connected to each other. And using pagination (created dynamically) you can go directly from one page to another. Therefore, it is best to combine elements into groups related to the same topic._

### Example with "Tutorials" group and two sections inside it
```json
{
  "tutorials": {
    "type": "group",
    "id": "tutorials",
    "label": "TUTORIALS",
    "args": {
      "withBorder": true,
      "initialIsOpen": true
    },
    "children": {
      "redis_stack": {
        "type": "group",
        "id": "redis_stack",
        "label": "Redis Stack",
        "args": {
          "initialIsOpen": false
        },
        "children": {
          "working_with_json": {
            "type": "internal-link",
            "id": "working_with_json",
            "label": "Working with JSON",
            "args": {
              "path": "/redis_stack/working_with_json.md"
            }
          },
          "vss": {
            "type": "internal-link",
            "id": "vector_similarity_search",
            "label": "Vector Similarity Search",
            "args": {
              "path": "/redis_stack/vector_similarity_search.md"
            }
          }
        }
      }
    }
  }
}
```
## Pages
By using **“internal-link”** node we can open other pages inside of Tutorials. To display more pages, add relevant files to the [src](https://github.com/RedisInsight/Tutorials/tree/main/src) folder.

These tutorials may contain the following elements:
* Plain text
* [Basic Markdown Syntax](https://www.markdownguide.org/basic-syntax/)
* [Extended Markdown Syntax](https://www.markdownguide.org/extended-syntax/)
* [HTML tags](https://www.markdownguide.org/basic-syntax/#html)
* Images
* Custom Syntax

### Images
Basic Markdown syntax provides the ability to render images. ([Image Markdown Syntax](https://www.markdownguide.org/basic-syntax/#images-1))

You can use the absolute path
```
![RedisInsight Browser screenshot](https://github.com/RedisInsight/RedisInsight/blob/main/.github/redisinsight_browser.png)
```
or relative path to image inside the [statics](https://github.com/RedisInsight/Tutorials)
```
![RedisInsight Browser screenshot](/_images/browser.png)
```

#### Redis Code block
![Redis Code block](docs/tutorials-redis-code-block.png)

###### Manual-execute button
A button that inserts Redis commands in the Editor. The syntax is almost the same as for the [Fenced Code Block](https://www.markdownguide.org/extended-syntax/#fenced-code-blocks),
the only difference is that you must specify `redis` as language and the label next to it (`Create` in the example below).
````
 ```redis Create
    // Let's add three documents as Hashes.
    // Each document represents a building permit.

    HSET permit:1 "description" "To reconstruct a single detached house with a front covered veranda." "construction_value" 42000 "building_type" "single detached house" "address_city" "Lisbon" "work_type" "demolition,reconstruction" "permit_timestamp" "1602156878" "address_street" "R. Da Palma" "location" "38.717746, -9.135839"

    HSET permit:2 "description" "To construct a loft" "construction_value" 53000 "building_type" "apartment" "address_city" "Porto" "work_type" "construction" "permit_timestamp" "1556546400" "address_street" "Rua da Boavista" "location" "41.155854, -8.616721"

    HSET permit:3 "description" "New house build" "construction_value" 260000 "building_type" "house" "address_city" "Lagos" "work_type" "construction;design" "permit_timestamp" "1612947600" "address_street" "R. Antonio Gedeao" "location" "37.114864, -8.668521"

 ```
````

###### Auto-execute button
![img.png](docs/tutorials-redis-auto-code-block.png)

A button that automatically executes the Redis commands (without inserting it into the Editor). Such buttons will be displayed with the "Play" icon inside.
The code syntax is the same as for the manually executed buttons, just indicate the `redis-auto` (instead of `redis`) before the button.

````
 ```redis-auto Create
    // Let's add three documents as Hashes.
    // Each document represents a building permit.

    HSET permit:1 "description" "To reconstruct a single detached house with a front covered veranda." "construction_value" 42000 "building_type" "single detached house" "address_city" "Lisbon" "work_type" "demolition,reconstruction" "permit_timestamp" "1602156878" "address_street" "R. Da Palma" "location" "38.717746, -9.135839"

    HSET permit:2 "description" "To construct a loft" "construction_value" 53000 "building_type" "apartment" "address_city" "Porto" "work_type" "construction" "permit_timestamp" "1556546400" "address_street" "Rua da Boavista" "location" "41.155854, -8.616721"

    HSET permit:3 "description" "New house build" "construction_value" 260000 "building_type" "house" "address_city" "Lagos" "work_type" "construction;design" "permit_timestamp" "1612947600" "address_street" "R. Antonio Gedeao" "location" "37.114864, -8.668521"

 ```
````

## Autoupdate
Our application supports the ability to quickly update the static files of the Enablement area so that we can provide users with up-to-date information.

EA tutorials auto-update flow:
1. Push commit with updates to the main branch
2. Wait until changes are merged into the latest branch
3. Reopen the Redisinsight with a working internet connection. In the background, new files will be downloaded to the user's local home directory:
    * **Mac**: In the `/Users/<your-username>/.redisinsight-v2/tutorials` directory.
    * **Windows**: In the `C:\Users\<your-username>\.redisinsight-v2/tutorials` directory.
    * **Linux**: In the `/home/<your-username>/.redisinsight-v2/tutorials` directory.
4. Open Enablement area on UI

## Development
Let's imagine that we need to add a new group (Streams) with 2 pages (Basics, Getting data).

Step by step implementation would be as follows:
1. First of all, it's best to create new pages locally and make sure that everything looks like it was planned.
2. Clone Tutorials repository (if you haven't done it before) and go into `<folder to tutorials repo>/src`
3. Create a new branch from the `main`
4. Add new `streams` folder with `basics.md` and `getting-data.md`. As a result, the folder structure will look something like this.
```
Tutorials
 > Redis Stack
   > Working with JSON
   > streams
     basics.md
     getting-data.md
 tutorials.json
```
5. Add new nodes inside `tutorials.json`
```json
{
  "tutorials": {
    "type": "group",
    "id": "tutorials",
    "label": "TUTORIALS",
    "children": {
      "redis_stack": {"id": "redis_stack"...},
      "streams": {
        "type": "group",
        "id": "streams",
        "label": "Streams",
        "children": {
          "basics": {
            "type": "internal-link",
            "id": "basics",
            "label": "Basics",
            "args": {
              "path": "/streams/basics.md"
            }
          },
          "get-data": {
            "type": "internal-link",
            "id": "getting-data",
            "label": "Get Data",
            "args": {
              "path": "/streams/getting-data.md"
            }
          }
        }
      }
    }
  }
}

```
> _**!Note.** Markdown file name should have the same value as  Node `id` to properly create pagination. _
6. Fill markdowns with content.

`basics.md`
````
For the goal of understanding what Redis Streams are and how to use them, we will ignore all the advanced features, and instead focus on the data structure itself, in terms of commands used to manipulate and access it.
This is, basically, the part that is common to most of the other Redis data types, like Lists, Sets, Sorted Sets and so forth.
However, note that lists also have an optional more complex blocking API, exported by commands like BLPOP and similar.
So streams are not much different than lists in this regard, it's just that the additional API is more complex and more powerful.

Because Streams are an append-only data structure, the fundamental write command, called **XADD**, appends a new entry into the specified stream.
A stream entry is not just a string but is instead composed of one or multiple field-value pairs.
This way, each entry of a stream is already structured, like an append-only file written in CSV format where multiple separated fields are present in each line.

```redis Create
XADD mystream * sensor-id 1234 temperature 19.8
```

````

`getting-data.md`
````
Querying by range: **XRANGE** and **XREVRANGE*
To query the stream by the range we are only required to specify two IDs, _start_ and _end_.
The range returned will include the elements having start or end as ID, so the range is inclusive. The two special IDs - and + respectively mean the smallest and the greatest ID possible.

```redis XRANGE
XRANGE mystream - +
```

```redis XREVRANGE
XREVRANGE mystream + - COUNT 1
```


````
7. Open **RedisInsight** application with `TUTORIALS` environment variable `TUTORIALS_DEV_PATH='<path to Tutorials repo>/src' ./<path to RedisInsight binary>`

    Example on Linux: `TUTORIALS='/home/user/projects/Tutorials/src' ./home/user/programms/RedisInsight.AppImage`
8. Make sure that everything looks fine.


9. After that, just commit and push, and then create Pull Request to the main branch. (Release flow described in the [Autoupdate Flow](#Autoupdate) section).


