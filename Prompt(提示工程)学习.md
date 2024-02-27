### 1.资料 Prompt-Engineering-Guide https://github.com/dair-ai/Prompt-Engineering-Guide    https://www.promptingguide.ai/introduction  中文版见 https://www.promptingguide.ai/zh
> LLM settings
>> * Temperature:the lower the temperature, the more deterministic the results in the sense that the highest probable next token is always picked. Increasing temperature could lead to more randomness, which encourages more diverse or creative outputs
>>
>> * Top P:A sampling technique with temperature, called nucleus sampling, where you can control how deterministic the model is.low top_p value selects the most confident responses.a high top_p value will enable the model to look at more possible words, including less likely ones, leading to more diverse outputs. The general recommendation is to alter temperature or Top P but not both
> Elements of a Prompt
>> * Instruction - a specific task or instruction you want the model to perform
>> * Context - external information or additional context that can steer the model to better responses
>> * Input Data - the input or question that we are interested to find a response for
>> * Output Indicator - the type or format of the output.
```
Classify the text into neutral, negative, or positive
Text: I think the food was okay.
Sentiment:
```
> General Tips for Designing Prompts
>> * Start Simple:start with simple prompts and keep adding more elements and context as you aim for better results. Iterating your prompt along the way is vital for this reason
>> * The Instruction:1) using commands to instruct the model what you want to achieve, such as "Write", "Classify", "Summarize", "Translate", "Order", etc  2) Others recommend that you place instructions at the beginning of the prompt. Another recommendation is to use some clear separator like "###" to separate the instruction and context
```
### Instruction ###
Translate the text below to Spanish:
Text: "hello!"
```
>> * Specificity：Be very specific about the instruction and task you want the model to perform，The details should be relevant and contribute to the task at hand.This is something you will need to experiment with a lot. We encourage a lot of experimentation and iteration to optimize prompts for your applications.
```
Extract the name of places in the following text. 
Desired format:
Place: <comma_separated_list_of_company_names>
Input: "Although these developments are encouraging to researchers, much is still a mystery. “We often have a black box between the brain and the effect we see in the periphery,” says Henrique Veiga-Fernandes, a neuroimmunologist at the Champalimaud Centre for the Unknown in Lisbon. “If we want to use it in the therapeutic context, we actually need to understand the mechanism.“"
```
>> * Avoid Impreciseness:It's often better to be specific and direct. The analogy here is very similar to effective communication -- the more direct, the more effective the message gets across.prompt would be one that is very specific, concise, and to the point.
```
Explain the concept prompt engineering. Keep the explanation short, only a few sentences, and don't be too descriptive.
vs  
Use 2-3 sentences to explain the concept of prompt engineering to a high school student.
```
>> * To do or not to do?:to avoid saying what not to do but say what to do instead,encourages more specificity and focuses on the details that lead to good responses from the model.

> Examples of Prompts: Text Summarization(长话短说  Explain the above in one sentence: \  Explain xxx),Information Extraction(根据内容回答问题 Mention xxx mentioned in the paragraph above:),Question Answering(给内容和问题，求解答 Question: What was OKT3 originally sourced from?）,Text Classification（文本分类）,Conversation,Code Generation,Reasoning （比如 What is 9,000 * 9,000?)

> Zero-Shot Prompting
>> didn't provide the model with any examples of text,When zero-shot doesn't work, it's recommended to provide demonstrations or examples in the prompt which leads to few-shot prompting
```
Classify the text into neutral, negative or positive. 
Text: I think the vacation is okay.
Sentiment:
```
>> https://www.promptingguide.ai/techniques/fewshot







### 2.资料 ChatGPT中的提示工程(Prompt)怎么做？DAIR.AI最新《提示工程指南》，全面讲述提示技术，附书册课件视频 https://www.zhihu.com/tardis/zm/art/616794213?source_id=1003
### 3. 思维链  https://zhuanlan.zhihu.com/p/620776904
### 4. 提升ChatGPT性能的实用指南：Prompt Engineering的艺术 https://aijishu.com/a/1060000000421219
