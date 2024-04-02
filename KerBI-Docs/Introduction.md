# Welcome to the **KerBI Project** 🐹

We'd like to take a minute to introduce you to your new best friend.

### What is a KerBI?

KerBI stands for **Ker**nel **B**ound **I**ntelligence.

In classical computing, the *kernel* is the operating system software that controls the physical hardware of the computer. The kernel loads and interacts with device drivers to display things on the screen, take input from a keyboard or mouse, use the network card to send signals, you get the idea. 

A Kernel Bound Intelligence does a similar thing. As the AI community continues to develop tools, clear patterns of use have sprung up. Some people want to use LLMs to write code and some want to execute it; others want to make friends, therapists, and knowledgeable experts, and chat with them in real-time; and many, many people want to automate away long file-based workflows. For a lot of developers, this means creating specific-use platforms in siloed projects, or depending entirely on a single AI ecosystem - like basing every new development off features in the OpenAI API (YUCK), or trying to sell hardware that's little more than a microphone with a network interface (CONSIDERABLY MORE YUCK).

A single running KerBI can provide for all of these tasks, simultaneously, executing locally on a single host, between hosts on a LAN, and/or across the web.  

### What's wrong with Agents?

If you're interested in developing AI applications, you are almost certainly familiar with the concept of an AI agent. 

'Agent' is a term used to describe a combination of LLM and prompt templating which attempts to simulate *agency*, that is to say, the ability to make decisions. These expectations are implicitly communicated by giving the model a 'toolbox' of functions. When the agent receives a request to complete some task, the list of tools comes with it, and the AI "decides" what to do based off this data combo. There are different mechanisms for how that decision happens: pure generation, chain of thought, tree of thought, etc. All of them rely on the model to make a decision. Somehow in the design of these products, most organizations forgot one thing: **Large Language Models can't make decisions**. They generate text - or, more accurately, they predict it. And, if you are familiar with using agents, you may have noticed that they regularly fail for a number of reasons, many of which are entirely out of the developers hands. This is especially the case when dealing with models provided over an API; when the provider releases different tunes or checkpoints or whathaveyou, the "decisions" that it may make will also change. So then the goal becomes designing a platform that:

1. Doesn't rely on the underlying model for consistent execution
2. Doesn't rely on making 10 API calls to create a plan of action and another bunch to execute
3. Offers a wide development platform for testing different models in different use cases
4. Is capable of providing a large array of AI services through a single point of configuration/install

**Enter KerBI**.

### What does a KerBI do differently?

##### KerBIs don't have agency. 
They don't decide what to do. They don't generate an action plan or a list of tasks and they don't use chain of thought to rack up your API calls. **You** tell your KerBI what to do and when to do it. In the KerBI system, the Large Language Model generates text - you know, the thing it's supposed to do.

##### KerBIs don't rely on external providers.
Besides connecting to the model itself , they don't rely on external tools or platforms. Not Langchain, not GPT functions, not text-generation-ui, not Open Interpreter. Of course, you can build any of these things on top of your KerBI, if you want. 

##### KerBIs don't care what model is running them.
We use a model driver system to provide KerBIs their LLM functionality. As long as all your [[components]] are compatible with your KServer version, you can swap out every piece of your KerBI and still run your workflows.

##### KerBIs can be built and reconfigured at will.
Builds let you quickly swap out components on your KerBI, or adapt your KerBI to different environments. For instance, you might want to run a development build with test KDriver and Context Handler, and deploy it with a GPT4 driver and MySQL. Much like building on GNU/Linux, your potential KerBI configurations are endless.  


### We introduce and/or codify a set of components that define a **Kernel Bound Intelligence**:

##### The [[KMessage]] object format carries the data needed to execute every task on the server, including KerBI [[SYSCALLS]]

##### The [[KServer]] routes kmessages internally and externally between the various components

##### The [KDriver](KDrivers) system provides robust reproducibility, testing capabilities, deployment stability, shareability, and more

##### The [[Context Handler]] employs selective memory and sliding-window context

##### The [[Execution Engine]] uses a dual-model architecture to write, format, and execute code client- or KerBI-side.

