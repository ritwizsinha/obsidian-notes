[[Youtube Video Notes]]
[[LLMS]]



### Stages
1. Pretraining
2. Tokenization
3. Neural Network Training
4. Inference





#### Pretraining
1. Download and pre process the internet
2. Fineweb, a dataset curated by hugging face and Common Crawl

The steps for pretraining is as follows:
	1. Url Filtering
	2. Text Extraction
	3. Language Filtering, filter for English for example
	4. A bunch of deduplication and filtering steps
	5. PII Removal


#### Tokenization
We pass these texts to neural nets which expect a one dimensional sequence of symbols, within a finite set of symbols.So we need to find out the symbols first. So we convert the text representation to bits, thus giving us finite symbols of 0-1. Storing the length of the symbols is really important for neural network, thus we need to increase the number of symbols and reduce the sequence length. Thus we create a sequence of bytes, representing 256 possible combinatins. In production we need to reduce the sequence size even further. We further group 2 byte pairs which occur very frequently and get a new symbol. GPT 4 uses around 100,277 symbols. This is called tokenisation.

Tiktokenizer
Shows how words are converted to tokens.



#### Neural Network Training
We take windows of tokens from the data and the length can have 0-MAX_SIZE_TOKENS. The long window sequences is computationally expensive. Then we try to predict what comes next in the sequence.  Then we predict the next token, we have 100,277 tokens so each of them get assigned a probability
![[Pasted image 20250330112348.png]]
We already know what comes next, thus we can tune the neural network. Thus we adjust and update the neural network to give higher probability for what comes next


##### Neural Network Internals
The input tokens in the sequence window get mixed in a giant mathematical expression with the weights of the of the model
![[Pasted image 20250330112806.png]]
[Visulization for Prediction](https://bbycroft.net/llm)


GPT - 2 had 1.6 billion parameters which is. the weights for the model


### Base Model
A token simulator. This contains the python code for the sequence of steps taken by that model . Then we also get the parameters. LLama 3 is one of the latest base models with 405 billion parameter. 
We can play with those models on Hyperbolic. Base model is more of a text autocomplete and it can't answer questions like an assistant. The base models are also stochastic ie they generate different autocompletes from the probability samples. Base models mostly regurgitate the data they see on the internet

Models have in context learning abilities, ie as they read the prompt , they have power to inplace learn the pattern



### Post Training or Fine Tuning

To have something like an assistant we create a smaller dataset and start training the base model on the smaller dataset. The assistant is programmed by example, and the data comes from human labelers. The base model was trained on internet documents, instead of the documents now we train it on conversations. Post training stage is shorter because dataset is smaller. A conversation between an agent and a person has different tokenization than a normal text document
UltraChat is an LLM helper that makes this process easier

#### LLM Psychology
###### Hallucinations
The model responses imitates the style of answers to similar questions and doesn't really incorporate facts. To improve on that we need to return the answer of I don't know when the model doesn't have an idea. To find out the extent of knowledge of the LLM.

If the data for which answer is to be made is in the context, it doesn't have to go to its memory, instead they could just look at the window sequence. We use model interrogation to discover's model knowledge and programatically augment the dataset with knowledge based refusals in cases where the model doesn't know.
Sometime we can also allow the model to search the web, get the text and copy paste in the context window or working memory.
Knowledge in Parameters == "Vague recolllcetion"
Knowledge in Context Window ==  "Working memory"


##### Knowledge of Self
No knowledge of self, just token completion boxes. 

##### Models need tokens to think
For each token we have a fixed amount of compute, thus for a single token, there can't be uneven distribution of compute, thus we need to distribute compute equally. Thus for solving some mathematical problems it needs to go step by step and solve it instead of giving answers in a single shot, as a single token would not be able to take into account the compuatation required.
Models also can't count, because it has to do this in a single forward pass in which there is not much computation.
Copying strings is very simple for the model.
Not good with spelling and counting and viewing a string charcter by character 



#### Reinforcement Learning
In a book.
Exposition = Pre Training
Worked Problems = Supervised Fine Tuning(SFT Model)
Practice Problems. = Reinforcement Learning