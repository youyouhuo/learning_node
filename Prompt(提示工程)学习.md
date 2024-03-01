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
> Few-Shot Prompting

>> Few-shot prompting can be used as a technique to enable in-context learning where we provide demonstrations in the prompt to steer the model to better performance
```
A "whatpu" is a small, furry animal native to Tanzania. An example of a sentence that uses the word whatpu is:
We were traveling in Africa and we saw these very cute whatpus.
To do a "farduddle" means to jump up and down really fast. An example of a sentence that uses the word farduddle is:
```

>>> tips

>>> * the label space and the distribution of the input text specified by the demonstrations are both important (regardless of whether the labels are correct for individual inputs)(label的空间和输入的文本空间同等重要【无论输入的label是否是正确的))
>>> * the format you use also plays a key role in performance, even if you just use random labels, this is much better than no labels at all.(在prompt的时候，使用的格式很重要，即使你使用随机的label，也好过没有label)
>>> * selecting random labels from a true distribution of labels (instead of a uniform distribution) also helps.（从标签的真实分布中选择随机标签（而不是均匀分布）也有所帮助。)

>>> Limitations of Few-shot Prompting

>>> * few-shot prompting works well for many tasks but is still not a perfect technique, especially when dealing with more complex reasoning tasks.(普通任务可能完成的不错，但是在resoning task中，表现就没那么ok了----最新的gpt4还ok)

> Chain-of-Thought Prompting
>> * intermediate reasoning steps： chain-of-thought (CoT) prompting enables complex reasoning capabilities through intermediate reasoning steps. You can combine it with few-shot prompting to get better results on more complex tasks that require reasoning before responding (cot可以通过中间推理步骤实现复杂的推理能力。可以通过与few-shot prompting进行结合在复杂的需要推理的任务上获取更好的结果。）
```
The odd numbers in this group add up to an even number: 4, 8, 9, 15, 12, 2, 1.
A: Adding all the odd numbers (9, 15, 1) gives 25. The answer is False.
The odd numbers in this group add up to an even number: 17,  10, 19, 4, 8, 12, 24.
A: Adding all the odd numbers (17, 19) gives 36. The answer is True.
The odd numbers in this group add up to an even number: 16,  11, 14, 4, 8, 13, 24.
A: Adding all the odd numbers (11, 13) gives 24. The answer is True.
The odd numbers in this group add up to an even number: 17,  9, 10, 12, 13, 4, 2.
A: Adding all the odd numbers (17, 9, 13) gives 39. The answer is False.
The odd numbers in this group add up to an even number: 15, 32, 5, 13, 82, 7, 1. 
A:
```
> Zero-shot COT Prompting
>> * adding "Let's think step by step" to the original prompt : One recent idea that came out more recently is the idea of zero-shot CoT,just add "Let's think step by step" to the original prompt
```
I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?
Let's think step by step.
```
> Automatic Chain-of-Thought (Auto-CoT)
>> * chain-of-thought prompting and  "Let's think step by step" prompt  can still end up with mistakes in generated chains.(前面两种方法还是可能得到错误的结果，因此Auto-CoT 尝试自动的进行处理以获取正确结果）
>> * Auto-CoT, which samples questions with diversity and generates reasoning chains to construct the demonstrations（通过多样化的采样，然后使用推理链来构建最终的结果）Auto-CoT consists of two main stages:
>>> * Stage 1): question clustering: partition questions of a given dataset into a few clusters  （将给定的问题划分成几个clusters)
>>> * Stage 2): demonstration sampling: select a representative question from each cluster and generate its reasoning chain using Zero-Shot-CoT with simple heuristics (从每个群集中选择一个代表性的问题，并使用简单的启发式方法使用Zero-Shot-CoT生成其推理链) The simple heuristics could be length of questions (e.g., 60 tokens) and number of steps in rationale (e.g., 5 reasoning steps).

> Self-Consistency:

>> self-consistency aims "to replace the naive greedy decoding used in chain-of-thought prompting". The idea is to sample multiple, diverse reasoning paths through few-shot CoT, and use the generations to select the most consistent answer. This helps to boost the performance of CoT prompting on tasks involving arithmetic and commonsense reasoning.(自洽性的目标是“替代在思维链提示中使用的天真贪婪解码”。这个想法是通过少量示例的CoT，采样多个、多样的推理路径，并使用生成的结果来选择最一致的答案。这有助于提高CoT提示在涉及算术和常识推理任务上的性能。)

> Generated Knowledge Prompting

>> generate knowledge to be used as part of the prompt

> Prompt Chaining

>> break tasks into its subtasks. Once those subtasks have been identified, the LLM is prompted with a subtask and then its response is used as input to another prompt. This is what's referred to as prompt chaining where a task is split into subtasks with the idea to create a chain of prompt operations.(分而治之）

>> Prompt chaining is useful to accomplish complex which an LLM might struggle to address if prompted with a very detailed prompt. In prompt chaining, chain prompts perform transformations or additional processes on the generated responses before reaching a final desired state.（完成复杂的问题）

>> achieving better performance, prompt chaining helps to boost transparency of your LLM application, increases controllability, and reliability. This means that you can debug problems with model responses much easier and analyze and improve performance in the different stages that need improvement.（让回答更加可控，可靠，同时也更容易debug 问题)

>https://www.promptingguide.ai/techniques/tot






### 2.资料 ChatGPT中的提示工程(Prompt)怎么做？DAIR.AI最新《提示工程指南》，全面讲述提示技术，附书册课件视频 https://www.zhihu.com/tardis/zm/art/616794213?source_id=1003
### 3. 思维链  https://zhuanlan.zhihu.com/p/620776904
### 4. 提升ChatGPT性能的实用指南：Prompt Engineering的艺术 https://aijishu.com/a/1060000000421219
