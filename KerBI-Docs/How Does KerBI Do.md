Here we cover how plugins and workflows combine generic and special functionality to accomplish their programmatic goals. This section deals with the JSON files in the '/api-server/plugins' and '/api-server/workflows' directories, not the [[Execution Engine]] itself.
#### Local Contents
- [[Workflows]]
- [[Plugins]]
## KerBI's Favourite Serial

Both plugins and workflows are made of JSON, and both of them are processed at start time. JSON provides flexibility and familiarity, allowing us to separate and combine all the data involved in a single KerBI at will. It also offers the best likelihood of integration with other services and an easy slide into future processing and training of and on KerBI data. 

> [!Info]
> JSON stands for **J**ava**S**cript **O**bject **N**otation, and it's one of the most ubiquitous serialization formats in use today. JSON uses key:value pairs and supports nested objects (like { key: { key: { key: value } } }. You can even use arrays (lists) as values!

##### So then what's the difference?

**Workflows are blueprints for the Execution Engine.** In the python implementation, they're blueprints for python classes that get instantiated at run time and placed in an array belonging to the KServer instance. [Learn more about workflows](Workflows)

**Plugins give KerBI new abilities.** Basically, plugins describe a general capability, like using, writing and executing bash. [Learn more about plugins](Plugins)

