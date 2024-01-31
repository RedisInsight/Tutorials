## Introduction

Tutorials are interactive documents that contain helpful use cases, comments and interesting experience about Redis to level up your Redis game.
You can use tutorials already embedded in RedisInsight to explore powerful Redis capabilities, or create your own custom tutorials to present your experience using RedisInsight and share them with others!

This page provides an overview of the Tutorials structure and its elements. It also contains instructions, recommendations, and best practices for creating, updating and sharing Tutorials.

If you want to share your own tutorials - add the `redis-tutorials` label to your GitHub repository so other other GitHub users can find and learn your experience.

## Navigation

1. [Structure](#Structure)
2. [Examples](#Examples)
3. [Markdown](#Markdown)
4. [Images](#Images)
5. [Manifest](#Manifest)
6. [Bulk Upload From Tutorials](#bulk-upload-from-tutorials)
7. [Advanced button parameters](#Advanced)
8. [Links configuration](#links-configuration)

## Structure

Tutorials should be combined into a .zip archive that contains the following:
1. [Markdown files](#Markdown) with the content of Tutorials
2. [manifest.json](#Manifest) that describes the structure and behavior of Tutorials

## Examples
If you are interested in creating your own tutorials and would like to understand how they work, you can start with the following steps:
- download this example
- open RedisInsight
- navigate to the Insights panel (top right corner)
- find the "My Tutorials" section on the "Explore" tab
- click the "Upload Tutorial" button and select the example

Your first custom Tutorials are uploaded, and you can open and explore them.

[Tutorials Example](https://github.com/RedisInsight/Tutorials/releases/download/2.42/data.zip)

## Markdown

In short, Tutorials support the basic [Markdown syntax](https://www.markdownguide.org/basic-syntax/) that you can use to specify your information.
If you would like to add a button to run commands, use Redis Code block that is almost the same as for the [Fenced Code Block](https://www.markdownguide.org/extended-syntax/#fenced-code-blocks), but you need to specify `redis` as language and the label next to it (see the `Create` button example below).

````
 ```redis Create
    // Let's add three documents as Hashes.
    // Each document represents a building permit.

    HSET permit:1 "description" "To reconstruct a single detached house with a front covered veranda." "construction_value" 42000 "building_type" "single detached house" "address_city" "Lisbon" "work_type" "demolition,reconstruction" "permit_timestamp" "1602156878" "address_street" "R. Da Palma" "location" "38.717746, -9.135839"

    HSET permit:2 "description" "To construct a loft" "construction_value" 53000 "building_type" "apartment" "address_city" "Porto" "work_type" "construction" "permit_timestamp" "1556546400" "address_street" "Rua da Boavista" "location" "41.155854, -8.616721"

    HSET permit:3 "description" "New house build" "construction_value" 260000 "building_type" "house" "address_city" "Lagos" "work_type" "construction;design" "permit_timestamp" "1612947600" "address_street" "R. Antonio Gedeao" "location" "37.114864, -8.668521"

 ```
````

## Images
Basic [markdown syntax](https://www.markdownguide.org/basic-syntax/#images-1) provides the ability to render images.

To render images, you need to provide either an external URL to these images or a relative path to a folder inside of your tutorials.
The folder with images can have any name, but it is recommended to start it with an underscore (_) or a dot (.) to avoid displaying it in RedisInsight in cases when the [manifest](#Manifest) is automatically generated.

Use the following logic to specify a relative path:

- `./` means the current directory;
- `../` means the parent of the current directory.

For example:

```
![RedisInsight Browser screenshot](./_images/aggregations.png)
```


## Manifest

`manifest.json` is an optional file that describes the structure and behavior of Tutorials.
If not specified, then RedisInsight will use the following logic:

1. the tutorial name is defined based on the name of the `.zip` archive
2. Page names are defined based on the file names in the `.zip` archive
3. The order of pages is defined based on the order in the `.zip` archive

If you would like to customize or prettify the archive name, page names, or the order, you can use the `manifest.json`. This JSON file is described as simple [Objects](https://javascript.info/object) which represents tree node and might have children nodes inside.

The `manifest.json` file should start with single Node and should have properties described 
below (it will be displayed as type "group"):

| Prop                    | Type     | Description                                                         |
|-------------------------|----------|---------------------------------------------------------------------|
| label (**required**)    | string   | Label that will be displayed on UI for Node                         |
| children (**required**) | Node[]   | List of Nodes (nested groups or markdown files)                     |
| type                    |          | Can be skipped - RedisInsight will always use the "group" parameter |
| author                  | string   | _Not required, not displayed_<br/> Author name                      |
| URL                     | string   | _Not required, not displayed_<br/> link to repository/web-site      |
| industry                | string[] | _Not required, not displayed_<br/> tags to show relevant industry   |
| description             | string   | _Not required, not displayed_<br/> short description of tutorials   |

Each Node requires a `label`,`type` (all available properties are described in the table below).

| Prop                 | Type                     | Description                                                                            |
|----------------------|--------------------------|----------------------------------------------------------------------------------------|
| id (**required**)    | string                   |                                                                                        |
| type (**required**)  | "group", "internal-link" |                                                                                        |
| label (**required**) | string                   | Label that will be displayed on UI for Node                                            |
| summary              | string                   | Short summary for the document (applied only for "internal-link" type)                 |
| children             | Node[]                   | Use only for the "group" type                                                          |
| args                 | Record<string, any>      | A special set of parameters required by a certain type of node (each type has its own) |

A Node can be represented by various UI components and is specified by `type` prop. Supported types are listed below.

| Type            	     | Description                                                                          	   | Args                                                                                         |
|-----------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
| **"internal-link"** 	 | A link that opens a page inside the Enablement Area <br> (e.g. link to guide page).      | - "path" (required, string) - relative file path                                             |
| **"group"**         	 | Grouping several nodes into one directory/folder. <br> Allows you to create nested lists | - "initialIsOpen" (boolean) - The group will start in the open state (default value - false) |

> _**!Note.** All **"internal-link"** nodes located in the same **"group"** will be connected to each other. And using pagination (created dynamically) you can go directly from one page to another. Therefore, it is better to combine elements into groups related to the same topic._


## Bulk Upload From Tutorials

You can upload sample data sets in bulk from your custom RedisInsight tutorials.
To do so, you will need to:
- create a text file with the list of Redis commands
- add this file to the archive with your custom tutorials
- create a special `redis-upload` button described in this section to execute all the Redis commands from custom tutorials

This button works similarly to the Bulk Upload in Browser, so all the Redis commands specified in the file will be executed.

The following syntax describes the `redis-upload` button:

````

```redis-upload:[{relative_path_to_the_text_file}] Upload data
```

````
**Notes:**

* use only a relative path to the text file inside the tutorial folder
* it is recommended to start the folder name with the text file from a dot (*.*) or underscore (*_*), so in the cases when the `manifest,json` file is automatically generated, RedisInsight will ignore this folder and not display it in your custom tutorial
* all the files included in your custom tutorials will be stored inside the RedisInsight folder as long as the tutorials exist in RedisInsight, so instead of including large text files in your custom tutorials, consider using Bulk Upload in Browser

**Example:**

For example, with the following structure of a custom tutorial:

```
 _data // a folder with the file to upload in bulk
    upload.txt
 folder_1 // a folder with custom tutorials
    my-tutorial.md
 manifest.json
```

Use one of the following examples to specify the `redis-upload` button in `my-tutorial.md`:

````
```redis-upload:[../_data/upload.txt] Upload data
```
````

as a relative path from `folder_1`

or

````
```redis-upload:[./_data/upload.txt] Upload data
```
````

as an absolute path from the tutorial folder.


## Advanced

You can customize the run parameters to configure the raw mode, pipeline parameter and group mode. If specified, they will override
the configuration set in RedisInsight. If not specified - the behaviour will be defined by parameters set in RedisInsight.
To specify the parameters, insert them after the `redis:` and add the parameters in square brackets separated with a semicolon (`;`) in
the following way:

```
redis:[<parameter1>;<parameter2>;<...>;<parameterN>]
```

Ensure that all the parameters are listed **without spaces**, ordering does not matter.

_Supported parameters:_

| Name             | Values                                  | Notes                                                                       | Description                                                                                                                                                                                                                                                                                    |
|------------------|-----------------------------------------|-----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| results          | * _single_<br/>* _group_<br/>* _silent_ | If enabled, then pipeline mode is ignored                                   | * _single_ - Disable the group mode to display the command results in a batch or as separate ones.<br/>* _group_ - Enable the group mode <br/>* _silent_:  <br/>&emsp; 1. Enable the group mode <br/>&emsp; 2. Auto collapse the result <br/>&emsp; 3. Show only error commands in the results |
| mode             | * _ascii_<br/>* _raw_                   |                                                                             | Enable/disable the raw mode to display command results.                                                                                                                                                                                                                                        |
| pipeline         | any integer number                      |                                                                             | Configure the number of commands in the pipeline. 0 or 1 pipelines every command.                                                                                                                                                                                                              |
| run_confirmation | * _true_                                | If _true_ RedisInsight will display confirmation dialog to run the commands | Enabling this parameter shows for particular query confirmation dialog                                                                                                                                                                                                                         |


_Notes:_

* Ensure that there are no spaces
* All the parameters with mistakes will be ignored

_Examples:_

```
redis:[results=single]
redis:[mode=raw]
redis:[pipeline=1]
redis:[results=silent;mode=raw]
redis:[results=single;pipeline=4]
redis:[results=group;mode=ascii]
redis:[pipeline=8;mode=raw;results=single;]
```
### Links configuration

#### Internal links
You can insert links that lead to a specific page inside RedisInsight. The syntax for these links is following:
```
[{text}](redisinsight:{path})
```

For example:
```
[Browser](redisinsight:browser)
```

The full list of pages:

| Page                                        | path                          |
|---------------------------------------------|-------------------------------|
| Home Page (List of databases)               | /                             |
| Browser                                     | browser                       |
| Workbench                                   | workbench                     |
| Analytics (will be redirected to first tab) | analytics                     |
| Cluster Details                             | analytics/cluster-details     |
| Database Analysis                           | analytics/database-analysis   |
| Slow Log                                    | analytics/slowlog             |
| Pub Sub                                     | pub-sub                       |
| Triggers and Functions                      | triggered-functions           |
| Libraries                                   | triggered-functions/libraries |
| Functions                                   | triggered-functions/functions |
| Settings                                    | settings                      |


#### Create a free Cloud database link

Also, there is a possibility to insert link which opens Create Free Database dialog inside RedisInsight

> *Note*: For Docker builds these links will work as pure external links
Syntax:
```
[{title}]({link} 'Redis Cloud')
```

For example:
```
[Create a free database](https://redis.com/try-free 'Redis Cloud')
```
