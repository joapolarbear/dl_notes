# BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding

* led by Google AI

https://arxiv.org/pdf/1810.04805.pdf

## Motivation

* Language model pre-training has been shown to be effective for improving many *natural language processing (NLP)* tasks
* two existing strategies for applying pre-trained language representations to downstream tasks
  1. *feature-based*: uses task-specific architectures that include the pre-trained representations as additional features. (ELMo)
  2. *fine-tuning*: introduces minimal task-specific parameters, and is trained on the downstream tasks by simply fine-tuning all pretrained parameters (the Generative Pre-trained Transformer, OpenAI
     GPT)

#### Gap

* The major limitation of previous work is that standard language models are unidirectional
* limits the choice of architectures that can be used during pre-training



## What 

* **B**idirectional **E**ncoder **R**epresentations from **T**ransformers

* The fine-tuning based approaches

* **bidirectional** pre-training for language representation

* alleviates the previously mentioned unidirectionality constraint by using a “*masked language model” (MLM)* pre-training objective



#### 1. Pre-training

* The model is trained on <u>unlabeled data</u> over <u>different pre-training tasks</u>.
* **Masked LM （MLM) **：
  * mask some percentage of the input tokens at random, and then predict those masked tokens
  * mask 15% of all WordPiece tokens in each sequence at random (with [MASK] token 80% of the time; random token 10%; unchanged 10%)
* **Next Sentence Prediction (NSP)**:
  * such as Question Answering (QA) and Natural Language Inference (NLI)
  * understanding the relationship between two sentences (between A and B)
  * 50% of the time B is the actual next sentence that follows A (labeled as IsNext), and 50% of the time it is a random sentence from the corpus (labeled as NotNext).

#### 2. Fine-tuning

* The BERT model is first initialized with the pre-trained parameters, and all of the parameters are fine-tuned using labeled data from the downstream tasks. 
* Each downstream task has separate fine-tuned models, even though they are initialized with the same pre-trained parameters
* Compared to pre-training, fine-tuning is relatively inexpensive. 
  * at most 1 hour on a single Cloud TPU, 
  * or a few hours on a GPU

![image-20190718123005559](assets/image-20190718123005559.png)

#### Model Architecture

* Unified architecture across different tasks, <u>minimal difference</u> between the pre-trained architecture and the final downstream architecture (output layers)
* Two model sizes: 
  * $BERT_{BASE}$ (L=12, H=768, A=12, Total Parameters=110M), the same model size as OpenAI GPT
  * $BERT_{LARGE}$ (L=24, H=1024, A=16, Total Parameters=340M).
  * $A$: the number of self-attention heads  **???**