## TLDR

- Proposed in the 2017 paper "[Attention Is All You Need](https://arxiv.org/abs/1706.03762)";
- [[Neural networks]] based on the [[Multi-head Attention Mechanism]];
- There are no recurrent units (simple RNN, GRU, LSTM) (so it requires less training time than [[Recurrent Neural Networks (RNN)]];
- The input preprocessing for [[Natural Language Processing (NLP)]] is: text -> [[Tokens]] -> [[Word Embeddings]];
- There are two types of [[Transformer Layers]]:
	- [[Encoder Layers]]: Reads and understands the input (sufficient for classification/regression tasks)
	- [[Decoder Layers]]: Generates the output (used for generative tasks, after a encoder block).

![Model architecture](images/ai/ml/dl/models/transformers/model-architecture.png)
## References

4. [arXiv - Attention Is All You Need](https://arxiv.org/abs/1706.03762)
5. [Wikipedia - Transformer (deep learning architecture)](https://en.wikipedia.org/wiki/Transformer_(deep_learning_architecture))