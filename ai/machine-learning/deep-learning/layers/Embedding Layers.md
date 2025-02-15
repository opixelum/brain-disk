- Maps from integer indices (tokenized strings or output of a [[Text Vectorization Layer]]) to dense vectors (words embeddings).
- Lower dimension than one-hot encoding.
- Used in NLP models (sentiment classification, …).
- Embedding is a sub-type of vectorization (TextVectorization layer vs Embedding layer)[¹](#references).
- Parameters:
	- input_dim: vocabulary size (number of unique words)
    - output_dim: size of the vector for each word. Test different sizes to find the optimal one. The bigger the vocabulary size is, the bigger the output dimension should be, but if the vocabulary contains a lot of « useless » words, the output dim should be a bit smaller, to get rid of noisy words. If the output dimension is too big, the model will overfit, making the layer useless[²](#references).
## References

1. [What are the exact differences between Word Embedding and Word Vectorization?](https://datascience.stackexchange.com/a/114794)
2. [How to choose dimension of Keras embedding layer?](https://datascience.stackexchange.com/a/83987)