### Workflows
Let's take an in depth look at our most basic and natural AI workflow - Chatting.
```json
{
  "basicchat": {
    "kserver version": "0.1.0",
    "cycle_count": "1",
    "cycle_retries": null,
    "initial_input": "USR",
    "piston_count": "1",
    "1": {
      "piston": "ChatPiston",
      "plugin": "BEP",
      "attachment": null,
      "retries": null
    }
  }
}
```

`basicchat` 
This first value is the name of the workflow. Because of how python dynamic imports work in KerBI, it isn't actually going to be the name of the resultant object instance. You can learn more about that in the coding docs; for now, just know that your workflow needs a unique name, and this is where it goes.

`kserver version`
This value denotes the KerBI release version at the time of use. KerBI itself won't use this value, but it gives us a rough idea of how up-to-date a given workflow or plugin is. KerBI releases are based on updates to the KServer module and main.py, and use [[semantic versioning]]. 

`cycle_count`
This parameter tells the engine how many times you want to run the entire execution chain before returning the result. For a basic chat we only want to cycle one time.

`cycle_retries`
Some workflows can fail. When a workflow fails it is because of a programmatic issue, not any failure on the model's text generation. You might add cycle retries to a workflow attempting to connect to a remote resource, or leave one running waiting for a file to update.  

`initial_input`
This parameter is for planned future use, allowing the triggering of workflows through internal message routing. For now, just copy the 'USR' value like we have here.

`piston_count`
Workflows are made up of chains of execution pistons. This tells KerBI how many pistons it has to parse and configure.

`1: {}`
Like workflows, pistons are dynamically imported python classes. Unlike workflows, they have explicit types and intended functionality. Each piston in the chain gets its own configuration object: 
- `piston` must match a real piston subclass found in the /api-server/modules/piston.py file
- `plugin` holds the `execution key` value for the plugin that the workflow is assigning to the piston
- `attachment` allows the passing of data in various formats to the piston. Chat has no additional data to pass, so we leave it as `null`
- `retries` pistons can fail too. When a piston fails, it's due to a generation failure. The piston system has a built in way of auditing code and handling execution failures; if you want, you can configure your workflow to try and "fix itself" by making use of the piston retry feature.

And that's it! This is as simple as KerBI workflows can get. Take a peek in the /api-server/workflows directory for some more interesting examples.
