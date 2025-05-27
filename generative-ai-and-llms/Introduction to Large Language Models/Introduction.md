## Large Language Models

1) *Language Model* is a machine learning model that aims to predict and generate plausible language. Autocomplete is a language model, for example. These models work by estimating the probability of a token or sequence of tokens occurring within a longer sequence of tokens.

2) *Token* In a language model, the atomic unit that the model is training on and making predictions on. A token is typically one of the following:

	- a word—for example, the phrase "dogs like cats" consists of three word tokens: "dogs", "like", and "cats".
	- a character—for example, the phrase "bike fish" consists of nine character tokens. (Note that the blank space counts as one of the tokens.)
	- subwords—in which a single word can be a single token or multiple tokens. A subword consists of a root word, a prefix, or a suffix. For example, a language model that uses subwords as tokens might view the word "dogs" as two tokens (the root word "dog" and the plural suffix "s"). That same language model might view the single word "taller" as two subwords (the root word "tall" and the suffix "er").

3) *Large Language Models* They are large, general-purpose language models are "pre-trained" on broad datasets and then "fine-tuned" with smaller, specific datasets

4) *Prompt design* involves creating clear and informative prompts for a task

5) *Prompt engineering* focuses on designing prompts to enhance model performance, possibly using domain knowledge or specific keywords

6) _Context window_ in a large language model (LLM) is the amount of text the model can process or "remember" at one time. It essentially defines the limit of how much input information an LLM can consider when generating a response. A larger context window allows the model to handle longer inputs and incorporate more information into its output.

7) _Temperature_ is a parameter provided to the function call to generate content. The temperature controls the degree of randomness used by the model in its token selection.

	- Optimal temperature depends on the model, specific task, and desired response style.
	- Higher temperature is suggested for instruction following and creative tasks.

8) _Top-K_ : Top-K limits the AI's word choices to only the K most probable words at each step.
	
	Imagine the AI is picking the next word: "The dog ___"
	1. If Top-K is 1, it only considers the single most likely word (e.g., "barked").
	2. If Top-K is 5, it considers the 5 most likely words (e.g., "barked," "ran," "slept," "ate," "chased") and then uses Temperature to pick from only those 5. This makes responses less random by forcing choices from a smaller, safer pool.

9) _Top-P_
	Simple explanation: Top-P sets a cumulative probability threshold for selecting the next word. The AI considers words from most to least probable, adding their probabilities, until their sum reaches the Top-P value. All words beyond that sum are ignored.
	
	Using "The dog ___" again:
	Suppose probabilities are: barked (0.7), ran (0.15), slept (0.08), ate (0.05), chased (0.02).
	
	If Top-P is 0.9:
	- Add barked (0.7). Sum = 0.7. (Still < 0.9)
	- Add ran (0.15). Sum = 0.85. (Still < 0.9)
	- Add slept (0.08). Sum = 0.93. (Now > 0.9). 
	
	So, the AI will only choose from "barked," "ran," and "slept" (using Temperature). This is a more flexible way than Top-K to ensure the chosen words are collectively highly probable, cutting off long tails of unlikely words.

10) _Model Tuning_ 
	Model tuning is a crucial process in adapting Gemini to perform specific tasks with greater precision and accuracy. Model tuning works by providing a model with a training dataset that contains a set of examples of specific downstream tasks.

11) _Grounding_
	Grounding is the ability to connect model output to verifiable sources of information. If you provide models with access to specific data sources, then grounding tethers their output to these data and reduces the chances of inventing content.

12) _Vector Embeddings_
	Vector embeddings are useful representations of unstructured data because they map content in such a way that semantic similarity is represented by distance in n-dimensional vector space.