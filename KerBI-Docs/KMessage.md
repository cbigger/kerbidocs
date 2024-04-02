> [!info]
> KerBI security is handled by the authentication server. The API server assumes you have a secure environment, however, **it is up to you to make it so**. 

The KerBI system relies on a specific format to transmit information around the system. We call this a `kmessage`:

```bash
{ 
  "channel": "HOME",
  "workflow": "basicchat",
  "to": "KSV",
  "from": "USR",
  "text": "Hello KerBI!",
  "attachments": [""]
}
```

The first four parameters from this example are the default KerBI values to run a basic chat mode with conversational memory. Let's discuss the six fields that make up a kmessage.

## Message Fields
#### Channel
Channels are how KerBIs organize data flows. All KerBIs come with two reserved channels: `HOME` and `SYSTM`, but you can add as many as you'd like. Just make sure to give them unique names.

The `SYSTM` channel routes system control calls. You can learn more about system calls in their own section: [[SYSCALLS]]

The `HOME` channel is the default channel and comes pre-registered with the `basicchat` workflow.

#### Workflow

Workflows are JSON files that describe a set of actions for a KerBI to take. You'll learn more about workflows in the dedicated [[Workflows]] page, including how to write your own. For this section, just know that you need one.

#### Addressing

The to/from must be one from a list of approved addressees:
- `USR` is a generic ID that tells the KServer that this message comes from outside the receiving host
- `KSV` this message is heading to/from the KServer.
- `INT` this message is heading to/from the Interpreter model
- `FAB` this message is heading to/from the Fabricator model
- `ENG` denotes a SYSCALL to/from the Execution Engine
- `MEM` denotes a SYSCALL to/from the Context Handler
- `KDR` denotes a SYSCALL to/from the KDriver Loader

*Support for whitelisting of Addressees is planned in a future release.*

#### Text

Fairly simple bit here. The `text` field contains the actual message being sent from the connecting client. It should be a text string.

#### Attachments

The `attachment` field is a flexible carrier for all kinds of accompanying data, including photos, files, URLs, etc. It is a list of strings. Having the correct stack of attachments to operate the given workflow is essential when constructing your kmessage.

## Composition

It's pretty hard to make a bad message. For example, the following is perfectly valid:
 
```bash
{ 
  "channel": "",
  "workflow": "",
  "to": "",
  "from": "",
  "text": "Hello KerBI!",
  "attachments": [""]
}
```

A KServer will generate a response based off the default workflow, which is basicchat. Without a channel, no conversation will be loaded, but the workflow will still load its examples and required system messages. This is great for batching a-contextual generations (and piping them around your system).