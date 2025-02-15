- Used in [[Recurrent Neural Networks (RNN)]];
- Learns dependencies between time steps in time-series and sequence data[¹ ](#references);
- Longer memory than simple [[Simple Recurrent Units]];

## Architecture

- Reset Gate: Determines how much of the previous hidden state to forget;
- Update Gate: Determines how much of the candidate activation vector to incorporate into the new hidden state.

## References

1. [gruLayer](https://www.mathworks.com/help/deeplearning/ref/nnet.cnn.layer.grulayer.html)
2. [Medium - Understanding Gated Recurrent Unit (GRU) in Deep Learning](https://medium.com/@anishnama20/understanding-gated-recurrent-unit-gru-in-deep-learning-2e54923f3e2)