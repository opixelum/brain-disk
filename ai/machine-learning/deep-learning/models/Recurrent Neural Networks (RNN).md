- Input: sequential data
- Sequential memory: remembers previous informations at each iteration in the sequence.
  ![RNN forward pass](images/ai/ml/dl/models/rnn/forward-pass.png)
  In this visualization, the circles are the hidden state. After each iteration, the hidden state gets updated, an remembers informations about previous iterations. But the further the forward pass goes, the less the network remembers about the firsts iterations. This is called **short-term memory**, and it's due to vanishing gradients.
- Forward pass in python pseudo code:
 ```python
 rnn = RNN()
 ff = FeedForwardNN()
 hidden_state = [0.0, 0.0, 0.0, 0.0]  # Shape depends on RNN's structure

 for word in input:
 output, hidden_state = rnn(word, hidden_state)  # hidden_state gets updated

 prediction = ff(output)
```

## Use Cases

- Speech recognition
- Language translation
- Stock predictions
- Image description

## References

- [The AI Hacker - Illustrated Guide to Recurrent Neural Networks: Understanding the Intuition](https://youtu.be/LHXXI4-IEns)