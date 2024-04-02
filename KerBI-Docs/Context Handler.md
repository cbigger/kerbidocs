The Context Handler manages the proper loading of a KerBI's memory into workflow context.

## How KerBI's Remember
In classical computing, we divide the way in which we handle data into two PC components: **memory** and **storage**. Both of these have their own data structures as well, like stack memory and various kinds of filesystem formats. Programs load into RAM (memory) from their SSD (storage).

However, when interfacing with KerBI-like systems, memory and storage take on different meanings. Below, we provide our specific definitions and declare a few others within KerBI-like systems: **history**, **stack**, **context**, and **conversation**.


### Storage
When we ask what your KerBI is using for storage, we are asking about the specific database implementation, not the format. MySQL is a viable response; SQL is not. Same with MongoDB vs. JSON.

In the end, it shouldn't really matter what you choose to use for storage. We use Mongo because of its relationship to JSON, but like every part of the KerBI system, as long as the Context Handler has the right classes and methods everything will be fine.


### History
History refers to a list of kmessages organized at the channel level. The history is considered *real data*, in that it doesn't store plugin examples or system prompts or any of that made-up nonsense. **It contains all kmessages from the channel**, including internal execution engine messages. 

Here's an example of channel history:

```json

history goes here dumfuk

```

### Memory
Memory is further differentiated from history based on which model is receiving the input. Memory is supplied by the context handler to the kdriver, which formats it according to the needs of its API or locally running LLM.

Of all these data definitions, memory is probably the strangest one to see in action. It's also part of what makes a KerBI so powerful. Each model has to maintain memory separation from its other half in order to execute its role, but at the same time needs to be aware of the successes and failures of the system as a whole.

Let's take a look at the memory passed during a call to our `executebash` workflow:
###### Interpreter Memory
```json
memory for interpreter
```
The first thing you'll notice is that the Interpreter has a significantly wider contextual feed and uses more tokens than the Fabricator does. This is because the Interpreter model receives the memory from every Interpreter call before it (in the related channel, of course), instead of only receiving the calls related to the plugin assigned to the calling piston, as is the case with the Fabricator's memory. 
###### Fabricator Memory
```json
memory for flabribater
```
The Fabricator loads its memory with only those fab calls that ran under the current plugin. We don't want to show our rigid machine possibly confusing generations from unrelated executions, and the fab already has a larger token count for its examples due to its input being a combination of the user request and response, so having a more focused memory selection helps keep the costs/gen time down. 

### Stack
If you're familiar with computer science, you might know about stacks. A stack is a simple structure for storing data. You can think of it like a stack of pancakes: you put one pancake on top of another pancake, and then you eat the second pancake before the first pancake. 

What do you mean, that's a stupid analogy? Fine! It's called **Last-In First-Out** (LIFO). Ask KerBI to explain it to you.

Anyways, a KerBI's stack is kind of like very-short-term memory, and it solves two major problems for the Context Handler.

The first problem is related to execution failure and retry. Every interaction stored in a memory segment becomes an example for the model that receives it. Naturally, we don't want it to "remember" generations that ultimately failed. We need to put them somewhere so we can either retry them, dump them for debugging, or some other as-of-yet unknown thing that could be neat. So, we stack them.

The second problem is related to speed. Because some workflows are doing multiple calls and generations behind the scenes, we want to save as much time as possible (and offer another point of configuration). This means limiting reads and writes. Using a stack can reduce a lot of database and memory management overhead by collecting messages to write them out all at once at a time convenient for the system.

### Context
The *context* is the combination of plugin examples and memory that is actually sent to the 
LLM. If you were to pop open the context, you would see a list of LLM formatted messages between a user and assistant (or whatever terms/format the called model uses).

> *Context Length* is a term that refers to the amount of tokens a model can accept as input in a single call. It lines up with our context as well: if your Context, when tokenized, is longer than the context length of the model being called, the call will either fail (default) or be cut from the middle out (with configuration).

The context contains the plugin examples from the plugin associated with the current running piston. You might rightly call these *synthetic memories* - in essence, they're implants. Using plugin examples that were actually created by a KerBI model is a good practice, and this is part of why plugins and workflows will tell you the kdriver they were made with. 

> [!Info] We're looking forward to the philosophical implications of falsifying memories in infant AGI. Chat about it with us on the [[discord]](discord)

### Conversation
The conversation is what the end user sees, and is based on how the connecting client chooses to display the interactions with the KerBI api-server. Typically, we are talking about the back-and-forth between the end user and the interpreter model, but there are endless options for what that actually looks like. For most of us working with the api-server, the conversation doesn't come up very often.
