All pistons require a plugin in order to function. Workflows assign plugins to pistons in their configuration. The plugin provides the examples and sets up the execution environment, and the piston accepts and processes the input data.

There are a few different ways that we categorize pistons. The three broad types are:
- Execution Pistons
- Loader Pistons
- Chat Pistons
##### Execution Pistons
Execution pistons are also known as StandardPistons and are responsible for the main dual-model, \[KCR\] usin', code-writin' tasks for which KerBI was originally put together. 

In an EP, data flows as follows:
- USR supplies input to INT requesting the completion of a code-solvable task
- INT responds with code as per the plugin assigned
- The USR request and INT response are combined using the special token \[KCR\]
- This combination request is sent as input to the FAB
- The FAB call prepares the code for execution by fixing errors and adding whatever is needed to execute according to the plugin examples. It should also evaluate the code to see if it is appropriately fulfilling the user's request
- The FAB response, now fully formatted as working executable code, is executed by the local kerbihost, or sent back to the client.

The Execution Piston is the bread and butter of the KerBI system. 

##### Loader Pistons
Loaders are kinda like reverse Execution Pistons. The loose purpose of a loader is to add some knowledge to the running channel. To that end, currently available loaders can parse text-based PDFs, HTML, binary, code, really whatever you're looking to load in (we haven't released image-based PDF parsing yet, working on it!).

You might think its easier to use external tools to parse data structures, and simply add it to your request to the interpreter. While that does work decently well, let's consider a few arguments against this method:
1. If you dump the entire file into a message to the Interpreter, it will end up in the memory. That means it will be in **every** call the INT makes, which can quickly rack up your tokens and use up your context space.
2. LLMs are mathematical machines that predict the most likely next token. If you load a large page of documents into memory such that the contents of the document in some way "overtake" the model's understanding of the conversation, you risk setting your computer on FIRE.
3. Limiting the raw data to the fabricator's memory allows the interpreter to maintain a supervisory distance from the "nervous system". This has implications for training holistic overseer models for both halves of the KerBI brain. Additionally, the FAB context is less important to the success of the KerBI-user interaction as a whole, and filling a loader pistons' memory with data is perfectly acceptable.

Now that you're convinced to use proper Loader Pistons wherever you can, let's take a look at the data flow for a RAG piston:
- In a RAG Piston, as with all loaders, the input starts at the fabricator.
- The FAB will accept a combination of the user's request and the text data to be consumed
- This data is combined at the special token \[KDI\], which stands for **K**erBI **D**ata **I**nspection. 
- The Fabricator will conduct the Data Inspection, ie., generate a response to the user's request by referencing the data.

At this point in our piston, we have to decide if we require the full context of the chat to fulfil the task at hand. We currently have the response from the fabricator, but the fabricator doesn't "see" the whole history of the channel and conversation. You might want to pipe the fabricator's Data Inspection response into a call to the interpreter to get some holistic insight, or, if its heading to another piston, return it directly. The latter is called a *Simple* Loader, and the former a *Compound* Loader.
###### Wait, aren't all Loaders RAG loaders?
RAG stands for **R**etrieval **A**ugmented **G**eneration. Most loaders are RAG loaders, but you may want to use a loader for something other than loading in data from an accompanying file or database. Really, due to the overall configurability of plugins and workflows, you can use a loader anytime you want the workflow to incorporate data from more than once source. 
##### Chat Pistons
A chat piston (sometimes called a half piston) uses only a single model in its call. Typically, this is used for, well, chatting with the model. You can use half pistons to build more complicated workflows and to interface with models directly. The FabChat piston is unique in that it allows the fabricator to receive memory from outside of the calling plugin's specific context, which can be cool for lazy debugging and general tomfoolery.  