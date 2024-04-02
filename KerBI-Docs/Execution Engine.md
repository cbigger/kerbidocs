
Humans probably don't have a left brain and a right brain, but KerBIs definitely do. KerBIs use a dual-model architecture to interpret requests and generate code/prepare data. We call the first model the *Interpreter* and the second model the *Fabricator*, and they both have distinct roles in making KerBIs so darn cute. Sorry, effective. *Affective?* That too!

KerBI workflows are built out of preconfigured generation paths between these two models that we call *Execution Pistons*. Understanding how the different pistons work is crucial to creating effective workflows and getting the most out of your KerBI. Read on for more in-depth descriptions of these three parts, or check out their python implementation over in the [coding docs]

> [!Info]
> We refer to the Interpreter and Fabricator as separate models, but depending on the driver they're often the same model with different quants/configuration knobs/LoRAs/etc.

### Interpreter Model
The Interpreter model is the model that most requests see first. It's the default model that engages with the user in chat, and it's context includes full memory from the associated channel, unlike the Fabricator. In an execution piston, the Interpreter will create code to fulfill the client's request. If engaged in a LoaderPiston, it acts as a data interpreter, returning analysis to the user. 

We design our kdrivers with the goal of configuring **highly creative, non-repetitive, general-experience** Interpreters. The success of your request depends in part on the ability of the Interpreter to design a solution to the problem you present it with. We recommend top-of-the-line, medium context LLMs for this one like GPT4. Turn the temperature up a bit as well - it gives a bit of "yes, and:" if you catch our drift.

### Fabricator Model
The Fabricator model is the one in the back doing the heavy lifting. The fab receives combined & refined inputs from the client and the Interpreter (or in the case of loader pistons, the data that's loaded in). In execution pistons, this combined input is called a KerBI Code Requisition, or **KCR**. If you've poked around your KerBI, you may recognize this from your plugin's `fabricator_examples`. The special KerBI token `[KCR]` is placed in between the first and second inputs, and this combined input is sent to the kdriver for generation. Fabrication calls include plugin-specific memory and context **only**. Unlike the Interpreter, the Fabricator doesn't see the entire history of its calls and generations. Instead, it sees examples and the relevant history for the task at hand.

We design our Fabricators to be **rigid, consistent, and efficient**. The execution of code is a precise exercise, and even slight variations can make the building and running of workflows more difficult. Fabricators employed in standard execution pistons are essentially superpowered editors. They trim conversational text from the Interpreter, format the code blocks/script files per the execution method, and occasionally solve the entire request themselves! 

You might not talk to it directly, but your KerBI's fab is just as important as the Interpreter. However, the lack of a need for creativity and the larger size of the examples fed in means we recommend a smaller and more tightly configured model. GPT3.5 works great, and we are experimenting with micro-sized models as well.

### Pistons
Pistons are functions that make use of AI generation and classical programming to achieve a specific goal. They're the main building block of [[workflows]], and understanding the different pistons and how they interact will unlock the full potential of your KerBI. There's a lot to cover, so they have [their own page](Pistons)

