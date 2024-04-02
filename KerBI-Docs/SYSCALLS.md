A **Sys**tem **Call** is a term used to describe a user-space request made to the kernel. Technically speaking, you could call any KMessage a system call, but in the KerBI ecosystem we specifically use it to refer to *system **control** calls*. These messages can perform operations on the server itself, like reloading various components or querying for configuration data.

KerBI SYSCALLs are composed as KMessages and sent to the `SYSTM` channel. 
```json
{ 
  "channel": "SYSTM",
  "workflow": "RELOAD",
  "to": "KSV",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```

> [!Info]
> You must provide a value for the `CONTROLS_KEY` variable in your configuration file in order to use SYSCALLs. 

## KServer
##### Server Reload
Sending a `RELOAD` signal to the KServer will restart your KerBI without starting a new process.
```json
{ 
  "channel": "SYSTM",
  "workflow": "RELOAD",
  "to": "KSV",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```
##### Query Configuration
`QUERY`-ing the KServer will return the running configuration. [[Learn more about configuring your KerBI]](Configuration)
```json
{ 
  "channel": "SYSTM",
  "workflow": "QUERY",
  "to": "KDR",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```
## Context Handler
##### Service Restart
A `RESTART` signal will re-instantiate the Context Handler. It will not load a new context_handler.py file. 
```json
{ 
  "channel": "SYSTM",
  "workflow": "RESTART",
  "to": "MEM",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```
##### Query Configuration
`QUERY`-ing the Context Handler will return the database configuration.
```json
{ 
  "channel": "SYSTM",
  "workflow": "QUERY",
  "to": "KDR",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```

## KDriver Loader
##### Service Restart
A `RESTART` signal will re-instantiate the KDriver. It will not load a new driver_kdr.py file.
```json
{ 
  "channel": "SYSTM",
  "workflow": "RESTART",
  "to": "KDR",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```
##### Load Driver
The `RELOAD` command will re-import and restart your kdriver. If the driver_kdr.py file has changed, it will load the new kdriver.
```json
{ 
  "channel": "SYSTM",
  "workflow": "RELOAD",
  "to": "KDR",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```
##### Query Configuration
`QUERY`-ing the kdriver will return the configuration for all models.
```json
{ 
  "channel": "SYSTM",
  "workflow": "QUERY",
  "to": "KDR",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```

## Execution Engine
##### Service Restart
A `RESTART` signal will re-instantiate the Execution Engine. It will not load a new execution_engine.py file.
```json
{ 
  "channel": "SYSTM",
  "workflow": "RESTART",
  "to": "EXN",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": [""]
}
```

##### SwapIf Execution Mode
A `SWAPIF` signal takes a single argument, `DESIRED_MODE`. You can set it to "KERBI" or "CLIENT". If the KerBI is already running in the given mode, no changes will be made. Otherwise, the execution engine will swap execution locales.
```json
{ 
  "channel": "SYSTM",
  "workflow": "SWAPIF",
  "to": "EXN",
  "from": "USR",
  "text": "<CONTROLS_KEY>",
  "attachments": ["<DESIRED_MODE>"]
}
```
> [!Info] 
> CLIENT execution mode can break workflows and is not fully implemented. Check the [[Execution Engine]] page for more information.

