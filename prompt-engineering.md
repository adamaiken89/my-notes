# Prompt Engineering

- LLM produces non-deterministic result, skilled LLM way can ensure a good result from prompt consistently
- LLM has a cut-off date

## Randomness

### Temperature

0 - deterministic 2 - chaotic

### Top-P

Cumulative Alternate Cutoff

#### Top-K

## Tokens

1 token = 0.75 word

cumulative tokens = input + output history

## Context Window

- Max tokens can remember

## Generative AI

It is a refined method to solve complex problems

- Machine Learning (build a model based on training data)
- Deep Learning (the model structure is in network of neural)
- Probability distribution

Predictive ML Model

-> training code & labelled data
-> structure and processed for ML Model

GenAI learns from nature language, video, images

y = f(x)

f = model x = input y = output

foundation model

<https://sgoldfarb2.github.io/practical-prompt-engineering> FrontendMaster Course - Practical Prompt Engineering

## Prompts

### Zero Shot Prompt

- rely on model knowledge
- very bad on long prompts
- common tasks focus

Good

- Translation
- General Knowledge
- simple script to do a single thing
- Fix a line of bug in a program
- Polish existing message

Bad

- Analysis
- series of tasks
- Programming on a product

### One Shot Prompt

Give an example and let the model learns it - you have full confident on this example Generative Work

Good

- Writing a guided, structural message

Bad

- More context, more text to input

### Few Shot Prompt

- provides a few examples, while you are not sure the actual styles and logic of it
- variety of level of examples, e.g. for programming
  - languages specification
  - programming style (OOP / functional)
  - with-(out) test cases
  - tools selected

Bad

- More context does not necessarily generate a better result and it can be inconsistent

## Context Placement

Importance

- Beginner > End > Middle

## Structural Output

Consistent Form

## Chain of Thought

Magic Sentence of first line

`Let's think step by step`

## Favouritism

- Toss a coin? (Randomness)

<https://www.ibm.com/think/prompt-engineering> Prompt Engineering Guide
