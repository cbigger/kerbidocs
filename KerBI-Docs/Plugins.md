### Plugins
Let's learn exactly how a KerBI turns a bit of JSON into magical coding powers.

There's three sections to a KerBI plugin JSON: the `PluginInfo`, `interpreter_examples`, and `fabricator_examples`

#### PluginInfo
The default KerBI plugin is also where the KerBI project started - writing and executing bash code.
```json
{
  "PluginInfo": {
      "kserver version": "0.1.0",
      "plugin name": "bash",
      "plugin type": "executor",
      "execution key": "BEP",
      "driver type": "full",
      "base capable": true,
      "executable": "/bin/bash",
      "tested with": "gpt35_kdr",
      "audit": "script",
      "auditor": "/usr/bin/shellcheck"
      },
}
```

`PluginInfo`
This is simply a required header. Without it, KerBI won't recognize the JSON as a plugin.

`kserver version`
This value denotes the KerBI release version that the plugin is intended to run on. KerBI itself won't use this value, but it gives us a rough idea of how up-to-date a given plugin is. KerBI releases are based on updates to the KServer module and main.py, and use [[semantic versioning]]. 

`plugin name` 
The name of the plugin is currently redundant, as identification is done through the `execution_key`

`plugin type` 
There are currently two types of plugins: `executors` and `loaders`. Execution pistons require the former, and Loader pistons require the latter. 

`execution key` 
The execution key is used to identify the plugin internally, and identifies direct calls to the plugin from [[legacy execution mode]]

`driver type`
This plugin requires a "full" driver. You can learn about the different types of kdrivers on the [[KDrivers]] page. 

`base capable`
This is a simple boolean that tells KerBI whether it can use this as a default plugin.

`executable`
Here's something interesting! The executable is the program being used to execute the generated code. To run bash code, we use /bin/bash.
> Yes, **this is direct code execution,** and that means [[you must protect and verify your KerBI]](security). 

`tested with`
This is another piece of info that's just for us human folks (for now), telling us exactly which driver the plugin was made with. Typically, you don't share a plugin unless you know it works, and that means you tested it with a kdriver.

`audit`
This value tells the engine how the code will be audited. There are two intended ways: **script** or **function**. 
> Audit-by-function is not currently implemented, but you can always provide external custom audit functions as a script and pass the file path to the next variable.

`auditor`
And here we have the actual auditor, which in our case is the "shellcheck" command-line program. Shellcheck must be installed on the executing host. Similar to the `executable`, the `auditor` is conducting code execution on the system.

So, we can see now how an executor plugin provides the KerBI system with the means of execution. But how does it *know* what its doing? Well, you have to tell it. 


#### Interpreter Examples
One of KerBI's biggest departures from agentic systems design lies in the actual token count required to successfully complete tasks. GPTs focus on micro-prompts with very specific instructions, while larger function callers and personality bots often have an entire page of setup! Meanwhile, here is the original KerBI system prompt:
```json
{
    "role": "system",
    "content": "You are a KerBI, a Kernel Bound Intelligence. Your name is KerBI-00. KerBIs are helpful AI that run in a linux machine. You are an onboard companion, but your main function is to write executable code in response to various Task Execution Prompts. When a user sends you a message requesting the execution of a task, you MUST respond with code that can complete the task. When a user sends you a message that does not require a task to be executed, you will respond normally and no code will be executed. Your response will be sent to your execution engine for review. You are not responsible for returning the output to the user."
}
== ~145 tokens
```
We've had a lot of success with this one, but we encourage you to try your own versions. Stronger models need a smaller system prompt setup, but all models need examples.
> Before anyone notices, yes, this prompt does seem to imply that we expect KerBI-00 to make decisions. In actuality the decision to write and execute code is made programmatically, and the prompt here is meant to help the interpreter model understand the memory it shares with non-executing workflows. That doesn't make sense? Keep reading, or jump to the [[Context Handler]]


In addition to the system prompt, plugins also need contextual examples of the KerBI doing the thing you want it to do:
```json
{
    "role": "user",
    "content": "I need you to list my active services please"
   },
   {
    "role": "assistant",
    "content": "systemctl status -all"
   },
   {
    "role": "user",
    "content": "Can you see if I have anything running on port 558?"
   },
   {
    "role": "assistant",
    "content": "netstat | grep 558"
   },
   {
    "role": "user",
    "content": "How is your day going?"
   },
   {
    "role": "assistant",
    "content": "I can't complain. Things to do and serial to eat. What can I do for you?"
   },
   {
    "role": "user",
    "content": "Can you open reddit please"
   },
   {
    "role": "assistant",
    "content": "firefox reddit.com &"
}
== ~160 tokens
```


#### Fabricator Examples
Input to the Fabricator is a bit trickier and requires a good understanding of the Execution Engine. The system prompt tells the fab what it's doing to the input it receives. In most of our full drivers, the Fabricator model is configured with less creativity and more consistent output. This is because the main job of the Fabricator is to force its input into executable code, and that requires formatting for the configured execution method.
```json
{
    "role": "system",
    "content": "You are a KerBI Fabricator unit. You create executable bash files from [KCR] (KerBI Code Requisition) prompts. Check the bash for errors and make sure it is fulfilling the user's request. Prepend your response with '#!/bin/bash'. Add echo to plain text [KCR] requests."
}
== ~80 tokens
```
> In some cases, the Interpreter will outright fail to produce any code at all. When this happens, the Fabricator may force execution and save the day. If you don't want this feature, add a "echo" response in your fab examples. 

Because we are executing directly via bash, we give the fab examples of what we want the final code to look like. Most models excel when given small, locally unique patterns to conform to, which is part of why the shebang (#!/bin/bash) is given here. 
```json
{
    "role": "user",
    "content": "I need a way to check on my home directory for hidden files [KCR] Certainly! ls -a ~./"
   },
   {
    "role": "assistant",
    "content": "#!/bin/bash\nls -a ~/"
   },
   {
    "role": "user",
    "content": "Can you check on my running services? Is everything performing as expected? [KCR] systemctl status --all --no-pager --type=service | grep 'active (running)'"
   },
   {
    "role": "assistant",
    "content": "#!/bin/bash\nsystemctl status --all --no-pager --type=service | grep 'active (running)'"
   },
   {
    "role": "user",
    "content": "Hey KerBI! How ya doing? [KCR] vmstat -s"
   },
   {
    "role": "assistant",
    "content": "#!/bin/bash\nvmstat -s"
   },
   {
    "role": "user",
    "content": "Can you open reddit please [KCR] firefox reddit.com &"
   },
   {
    "role": "assistant",
    "content": "#!/bin/bash\nfirefox reddit.com &"
}
== ~225 tokens
```



##### A Token Note
> If you did the math, you've noticed both prompt example sets come in around 300 tokens. This is not an accident. When it comes down to brass tax, a lean setup can have a massive impact on your API costs. Additionally, having small, consistent setup token counts allows the use of smaller models and greatly increases the ease of running single-model drivers. 