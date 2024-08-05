## Exponential Linear Unit (ELU)

- **Activation**:

$$
\text{ELU}(x) = \begin{cases} 
x & \text{if } x \geq 0 \\
\alpha(e^x - 1) & \text{if } x < 0 
\end{cases}
$$
	with parameter $\alpha$.
	
- **Derivative**:

$$
\frac{d}{dx} \text{ELU}(x) = \begin{cases} 
1 & \text{if } x \geq 0 \\
\alpha e^x & \text{if } x < 0 
\end{cases}
$$
	with parameter $\alpha$.

## Gaussian Error Linear Unit (GELU)

- **Activation**:
$$
\text{GELU}(x) = x \cdot \Phi(x) = x \cdot \frac{1}{2}\left[1 + \text{erf}\left(\frac{x}{\sqrt{2}}\right)\right]
$$

- **Derivative**:

$$
\frac{d}{dx} \text{GELU}(x) = \Phi(x) + x \cdot \frac{d\Phi(x)}{dx}
$$

## Leaky ReLU
## Linear (Identity)
## Logistic (Sigmoid)
## PReLU
## ReLU
## SELU
## SiLU
## Softplus
## Tanh
## Gaussian
## Softmax
## References

- [Activation function](https://en.wikipedia.org/wiki/Activation_function)