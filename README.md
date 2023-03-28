## Introduction
Tutorials are documents that contain helpful use cases, comments and interesting experience about Redis and Redis Stack capabilities.
It is a perfect tool to present your use cases and experience using RedisInsight Workbench and share them with others!

This page provides an overview of the Tutorials structure, its elements and contains instructions, recommendations, and best practices for updating and sharing Tutorials.

## Navigation

1. [Structure](#Structure)
2. [Examples](#Examples)
3. [Pages](#Pages)
4. [Advanced button parameters](#Advanced)

## Structure

Tutorials should be combined into a .zip archive that contains the following:
1. [Markdown files](#Markdown) with content of Tutorials
2. [manifest.json](#manifest.json) that describes the structure and befavior of Tutorials

## Examples
If you would like to start with Tutorials, download this example with Redis Stack Tutorials and upload the archive using the "Upload Tutorial" feature in RedisInsight Workbench so you can view and understand the overall logic.

[Redis Stack Tutorials.zip](https://github.com/RedisInsight/Tutorials/files/10911840/Redis.Stack.Tutorials.zip)

## Markdown files

In short, Tutorials support the basic [Markdown syntax](https://www.markdownguide.org/basic-syntax/) that you can use to specify your information.
If you would like to add a button that will insert Redis commands to the Workbench Editor, use Redis Code block that is almost the same as for the [Fenced Code Block](https://www.markdownguide.org/extended-syntax/#fenced-code-blocks), but you need to specify `redis` as language and the label next to it (see the `Create` button example below).

````
 ```redis Create
    // Let's add three documents as Hashes.
    // Each document represents a building permit.

    HSET permit:1 "description" "To reconstruct a single detached house with a front covered veranda." "construction_value" 42000 "building_type" "single detached house" "address_city" "Lisbon" "work_type" "demolition,reconstruction" "permit_timestamp" "1602156878" "address_street" "R. Da Palma" "location" "38.717746, -9.135839"

    HSET permit:2 "description" "To construct a loft" "construction_value" 53000 "building_type" "apartment" "address_city" "Porto" "work_type" "construction" "permit_timestamp" "1556546400" "address_street" "Rua da Boavista" "location" "41.155854, -8.616721"

    HSET permit:3 "description" "New house build" "construction_value" 260000 "building_type" "house" "address_city" "Lagos" "work_type" "construction;design" "permit_timestamp" "1612947600" "address_street" "R. Antonio Gedeao" "location" "37.114864, -8.668521"

 ```
````

## manifest.json

Manifest.json is not required and can be skipped, in the case of missing manifest.json, RedisInsight will:
1. Take the Tutorial name from the .zip archive
2. All the pages will have names the same as file names in the .zip archive
3. The order of pages will be the same as in the .zip archive

If you would like to customize or prettify the archive name, page names, or the order, you can use the manifest.json. This JSON file is described as simple [Objects](https://javascript.info/object) which represents tree node and might have children nodes inside.

Manifest file should start with single Node and should have properties described 
below (it will be displayed as type "group"):

| Prop                    | Type                     | Description                                                                                                                   |
|-------------------------|--------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| label (**required**)    | string                   | Label that will be displayed on UI for Node                                                                                   |
| children (**required**) | Node[]                   | List of Nodes (nested groups or markdown files)                                                                               |
| type                    |  | Can be skipped - RedisInsight will always use the "group" parameter                                                                            |
| author                  | string                   | Not required, Author name                                                                                                                   |
| URL                     | string                   | Not required, link to repository/web-site                                                                                                   |
| industry                | string[]                 | Not required, tags to show relevant industry                                                                                                |
| description             | string                   | Not required, short description of tutorials                                                                                                |

Each Node requires a `label`,`type` (all available properties are described in the table below).

| Prop                 | Type                     | Description                                                                            |
|----------------------|--------------------------|----------------------------------------------------------------------------------------|
| id                   | string                   |                                                                                        |
| type (**required**)  | "group", "internal-link" |                                                                                        |
| label (**required**) | string                   | Label that will be displayed on UI for Node                                            |
| children             | Node[]                   | Use only for the "group" type                                                              |
| args                 | Record<string, any>      | A special set of parameters required by a certain type of node (each type has its own) |

A Node can be represented by various UI components and is specified by `type` prop. Supported types are listed below.

| Type            	     | Description                                                                          	   | Args                                                                                         |
|-----------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| **"internal-link"** 	 | A link that opens a page inside the Enablement Area <br> (e.g. link to guide page).      | - "path" (required, string) - relative file path                                             |
| **"group"**         	 | Grouping several nodes into one directory/folder. <br> Allows you to create nested lists | - "initialIsOpen" (boolean) - The group will start in the open state (default value - false) |

> _**!Note.** All **"internal-link"** nodes located in the same **"group"** will be connected to each other. And using pagination (created dynamically) you can go directly from one page to another. Therefore, it is better to combine elements into groups related to the same topic._


## Advanced button parameters

You can customize the run parameters to configure the raw mode, pipeline parameter and group mode. If specified, they will override
the configuration set in RedisInsight. If not specified - the behaviour will be defined by parameters set in RedisInsight.
To specify the parameters, insert them after the `redis-auto:` and add the parameters in square brackets separated with a semicolon (`;`) in
the following way:

```
redis:[<parameter1>;<parameter2>;<...>;<parameterN>]
```

Ensure that all the parameters are listed **without spaces**, ordering does not matter.

_Supported parameters:_

| Name     | Values                   | Notes                                     | Description                                                                                                                                              |
|----------|--------------------------|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| results  | * _single_<br/>* _group_<br/>* _silent_ | If enabled, then pipeline mode is ignored | * _single_ - Disable the group mode to display the command results in a batch or as separate ones.<br/>* _group_ - Enable the group mode <br/>* _silent_:  <br/>&emsp; 1. Enable the group mode <br/>&emsp; 2. Auto collapse the result <br/>&emsp; 3. Show only error commands in the results|
| mode     | * _ascii_<br/>* _raw_    |                                           | Enable/disable the raw mode to display command results.                                                                                                                        |
| pipeline | any integer number       |                                           | Configure the number of commands in the pipeline. 0 or 1 pipelines every command.                                                     |
| auto | * _true_<br/>* _false_       |                                           | Configure the auto execute commands after click on the button |


_Notes:_

* Ensure that there are no spaces
* All the parameters with mistakes will be ignored

_Examples:_

```
redis:[results=single]
redis:[auto=true;mode=raw]
redis:[pipeline=1]
redis:[results=silent;mode=raw]
redis:[results=single;pipeline=4]
redis:[results=group;mode=ascii]
redis:[auto=true;pipeline=8;mode=raw;results=single;]
```


