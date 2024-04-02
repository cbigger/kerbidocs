KerBIs are powered by Large Language Models. LLMs come in many different shapes and sizes, as do the various knobs that tweak their generation, like temperature, top-p, repetition, etc. In fact, when someone shares a generation you like or would like to replicate, the questions don't stop: What's the model? Context size? Is it an instruct format? What are the special tokens? Is it API? Local? Llama? GPT? 

For those of us trying to build AI applications, reproducibility is a nightmare. There are many issues at play, even beyond the many configurations:
###### API providers regularly update their models, endpoints, and functions:
- When models get updated, a lot of functionality can break. In KerBI systems, we see this most often when alignment is clumsily added; the introduction of refusals to a model's training or LoRA will result in more denial-of-execution responses from a certain model
- Endpoint updates eventually break existing code, requiring fixes to get things up and running again. Functions have a similar issue, requiring a certain level of code maintenance.
Using a driver system allows KerBIs to swap models in and out for testing (function and budget) and facilitates backup connections if a service goes down (in the case of a loss of provider, switch to a backup kdriver seamlessly).

**Models are *massive* files, which sucks because:**
- If you're trying to run local, you're dealing with models anywhere from 2GB all the way to 200GB
- Programs that cater to every model situation all at once are also *massive*, both from a developer perspective and strictly speaking in terms of actual file size
