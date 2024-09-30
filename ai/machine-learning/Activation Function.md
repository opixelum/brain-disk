## Exponential Linear Unit (ELU)

- Smooth [[#Rectified Linear Unit (ReLU)]] variant;
- Can outputs negative values;
- Better normalization since negative values push the mean to zero, with lower computational complexity than a [[Batch Normalization Layer]].

- **Activation:**
$$
\text{ELU}(x) = \begin{cases} 
x & \text{if } x \geq 0 \\
\alpha(e^x - 1) & \text{if } x < 0 
\end{cases}
$$
  with parameter $\alpha \in \mathbb{R}_{+}^{*}$.

- **Derivative:**
$$
\frac{d}{dx} \text{ELU}(x) = \begin{cases} 
1 & \text{if } x \geq 0 \\
\alpha e^x & \text{if } x < 0 
\end{cases}
$$
  with parameter $\alpha \in \mathbb{R}_{+}^{*}$.

## Identity (Linear)

- Used as **last activation function for regression tasks**.

- **Activation:**

$$\text{Identity}(x) = x$$

- **Derivative:**
$$\frac{d}{dx} \text{Identity}(x) = 1$$

## Leaky ReLU

- [[#Parametric ReLU (PReLU)]] with $\alpha = 0.01$.

- **Activation:**
$$
\text{Leaky ReLU}(x) = \begin{cases} 
x & \text{if } x \geq 0 \\
0.01x & \text{if } x < 0 
\end{cases}
$$

- **Derivative:**
$$
\frac{d}{dx} \text{Leaky ReLU}(x) = \begin{cases} 
1 & \text{if } x \geq 0 \\
0.01 & \text{if } x < 0 
\end{cases}
$$

## Logistic (Sigmoid)

- Used as **last activation function for binary classification tasks**, representing the probability of belonging to class 0 or 1.
- Has **vanishing gradient problem** in deep neural networks.

- **Activation:**
$$\text{Logistic}(x) = \sigma(x) = \frac{1}{1 + e^{-x}}$$

- **Derivative:**
$$\frac{d}{dx} \text{Logistic}(x) = \sigma(x) \cdot (1 - \sigma(x))$$

## Parametric ReLU (PReLU)

- Uses learnable parameter $\alpha$ to determine the slope of the negative values;
- Solves the dying neurons problem when $\alpha \neq 0$;
- Variant of [[#Rectified Linear Unit (ReLU)]].

- **Activation:**
$$
\text{PReLU}(x) = \begin{cases} 
x & \text{if } x \geq 0 \\
\alpha x & \text{if } x < 0 
\end{cases}
$$
  with parameter $\alpha \in \mathbb{R}$.

- **Derivative:**
$$
\frac{d}{dx} \text{PReLU}(x) = \begin{cases} 
1 & \text{if } x \geq 0 \\
\alpha & \text{if } x < 0 
\end{cases}
$$
  with parameter $\alpha \in \mathbb{R}$.

## Rectified Linear Unit (ReLU)

- Most used activation function in [[Convolutional Neural Network (CNN)]];
- Quick to compute and easy to understand and explain;
- Solves vanishing gradient problem;
- Dying neurons problem: since it outputs 0 for negative input, it prevent any further learning for that neuron.

- **Activation:**
$$
\text{ReLU}(x) = \max(0, x)
$$

- **Derivative:**
$$
\frac{d}{dx} \text{ReLU}(x) = \begin{cases} 
1 & \text{if } x > 0 \\
0 & \text{if } x \leq 0 
\end{cases}
$$

## SELU

- Self-normalizing activation function, meaning that there is no need of [[Batch Normalization Layer]];
- Variant of [[#Exponential Linear Unit (ELU)]].

- **Activation:**
$$
\text{SELU}(x) = \lambda \begin{cases} 
x & \text{if } x > 0 \\
\alpha(e^x - 1) & \text{if } x \leq 0 
\end{cases}
$$
  with parameter $\lambda = 1.0507$ and $\alpha = 1.67326$.

- **Derivative:**
$$
\frac{d}{dx} \text{SELU}(x) = \lambda \begin{cases} 
1 & \text{if } x > 0 \\
\alpha e^x & \text{if } x \leq 0 
\end{cases}
$$
  with parameter $\lambda = 1.0507$ and $\alpha = 1.67326$.

## Sigmoid Linear Unit (SiLU)

- Improved accuracy with less overfitting in deep neural networks;

- **Activation:**
$$
\text{SiLU}(x) = x \cdot \sigma(x) = \frac{x}{1 + e^{-x}}
$$

- **Derivative:**
$$
\frac{d}{dx} \text{SiLU}(x) = \sigma(x) + x \cdot \sigma(x) \cdot (1 - \sigma(x))
$$

## Softplus

- Smooth approximation of [[#Rectified Linear Unit (ReLU)]] which never outputs zero.
- More expensive to compute because it uses both exponential and logarithm functions.

- **Activation:**
$$
\text{Softplus}(x) = \ln(1 + e^x)
$$

- **Derivative:**
$$
\frac{d}{dx} \text{Softplus}(x) = \frac{1}{1 + e^{-x}} = \sigma(x)
$$

## Tanh

- Used in hidden layers of a neural network or as last activation function for binary classification tasks where labels are either -1 or 1;
- Better performances than [[#Logistic (Sigmoid)]];
- Has the vanishing gradient problem in deep neural networks. [[#Rectified Linear Unit (ReLU)]] solves this issue.

- **Activation:**
$$
\text{Tanh}(x) = \tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}
$$

- **Derivative:**
$$
\frac{d}{dx} \tanh(x) = 1 - \tanh^2(x)
$$

## Softmax

- Normalizes the output to a **probability distribution over predicted output classes**;
- Used as the **last activation function** in a neural network for **classification** tasks.
- When implementing it, be careful of big numbers for the inputs, it can cause an overflow. To solve this issue, use the softmax trick.

- **Activation:**
$$
\text{Softmax}(x_i) = \frac{e^{x_i}}{\sum_{j=1}^{n} e^{x_j}}
$$
  $\forall i = 1\text{, ..., }n$.
  $n$ is the number of elements in the vector $X$.

- **Derivative:**

  Since softmax gives a vector as an output, we can't define a gradient to it. The workaround is to take derivatives of the loss function, with respect to the inputs passed to softmax, instead of the outputs passed by softmax. See [references](#references) for more information.

### Softmax Trick

The trick is to reduce big inputs numbers without affecting the softmax result.
If the operation is applied in each side of the division, the result will remain the same as a normal softmax.
$$
\text{SoftmaxTrick}(X) = \frac{e^{x_i - Max(X)}}{\sum_{j=1}^{n} e^{x_j - Max(X)}}
$$
  $\forall i = 1\text{, ..., }n$.
  $n$ is the number of elements in the vector $X$.
  $x$ is an element of the vector $X$.
## References

1. [Activation function](https://en.wikipedia.org/wiki/Activation_function)
2. [ELU](https://ml-cheatsheet.readthedocs.io/en/latest/activation_functions.html#elu)
3. [Exponential Linear Unit](https://paperswithcode.com/method/elu)
4. [Why identity function is generally treated as an activation function?](https://ai.stackexchange.com/a/31722)
5. [Parametric ReLU (PReLU)](https://schneppat.com/parametric-relu-prelu.html)
6. [Why deep learning models still use RELU instead of SELU, as their activation function?](https://datascience.stackexchange.com/a/102810)
7. [What are the disadvantages of using the ReLu when using Neural Networks?](https://www.quora.com/What-are-the-disadvantages-of-using-the-ReLu-when-using-Neural-Networks)
8. [What is SELU?](https://www.educative.io/answers/what-is-selu)
9. [Sigmoid Linear Unit](https://serp.ai/sigmoid-linear-unit)
10. [Is there any particular reason why you use softplus to make sure the scale is non-negative?](https://github.com/tensorflow/probability/issues/751#issuecomment-578921350)
11. [Relationship between sigmoid and tanh activation function](https://anyesh.medium.com/relationship-between-sigmoid-and-tanh-activation-function-53d289889d9a)
12. [Tanh Activation](https://paperswithcode.com/method/tanh-activation)
13. [Vanishing Gradient Problem : Everything you need to know](https://www.engati.com/glossary/vanishing-gradient-problem)
14. [Softmax function](https://en.wikipedia.org/wiki/Softmax_function)
15. [Understanding backpropagation with softmax](https://stackoverflow.com/a/76532286)