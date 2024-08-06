## Binary Cross-Entropy (BCE) (Log Loss)

- For binary classification tasks with labels either 0 or 1.
$$
\text{BCE} = -\frac{1}{n} \sum_{i=1}^{n}(ŷ_i \log(y_i) + (1-ŷ_i) \log(1-y_i))
$$
  Where:
  - $y_i =$ predicted value;
  - $ŷ_i =$ target value, either 0 or 1;
  - $\forall i = 1 \text{, ..., } n$;
  - $n =$ number of samples in the training dataset.
  
## Categorical Cross-Entropy (Softmax Loss)

- For classification tasks;
- Outputs the probability of each class;
- Labels need to be **one-hot encoded**;
- Commonly used with softmax activation function.
$$
\text{CCE} = -\sum_{i=1}^{n} ŷ_i \log(y_i)
$$
  Where:
  - $y_i =$ predicted value;
  - $ŷ_i =$ target value, either 0 or 1;
  - $\forall i = 1 \text{, ..., } n$;
  - $n =$ number of samples in the training dataset.

## Hinge Loss

- For binary classification tasks with labels either -1 or 1;
- Often used in [[Support Vector Machine (SVM)]].
$$
\text{Hinge} = \max(0, 1-ŷy)
$$
  Where:
  - $y =$ predicted value;
  - $ŷ =$ target value, either -1 or 1.
  
## Mean Absolute Error (MAE) (L1)

- For regression tasks;
- Better fitting of the data when there are outliers;
- Slower than [[#Mean Square Error (MSE) (L2)]].
$$
\text{MSE} = \frac{1}{n}\sum_{i=1}^{n}\lvert y_i-ŷ_i\rvert
$$
  Where:
  - $y_i =$ predicted value;
  - $ŷ_i =$ target value;
  - $\forall i = 1 \text{, ..., } n$;
  - $n =$ number of samples in the training dataset.
  
## Mean Square Error (MSE) (L2)

- For regression tasks;
- Because of the square, it treats all sample with equal importance. That means that **outliers will have a significant impact on the loss**. But if the dataset is clean, consistent with no outliers, it fits faster than [[#Mean Absolute Error (MAE) (L1)]].
$$
\text{MSE} = \frac{1}{n}\sum_{i=1}^{n}(y_i-ŷ_i)^2
$$
  Where:
  - $y_i =$ predicted value;
  - $ŷ_i =$ target value;
  - $\forall i = 1 \text{, ..., } n$;
  - $n =$ number of samples in the training dataset.

##  Huber Loss (Smooth Mean Absolute Error (SMAE))

- For regression tasks;
- Mix of both [[#Mean Absolute Error (MAE) (L1)]] and [[#Mean Square Error (MSE) (L2)]] (medium sensitivity to outliers but good convergence speed);
- Quadratic component for small errors (like MSE);
- Linear component for large errors (like MAE).
$$
\text{Huber} = \begin{cases}
\frac{1}{2} (y_i-ŷ_i)^2 & \text{for } |y_i-ŷ_i| \leq \delta \\
\delta (|y_i-ŷ_i| - \frac{1}{2} \delta) & \text{for } |y_i-ŷ_i| \gt \delta
\end{cases}
$$
  Where:
  - $\delta =$ delta parameter, $\delta \in \mathbb{R}_+$;
  - $y_i =$ predicted value;
  - $ŷ_i =$ target value;
  - $\forall i = 1 \text{, ..., } n$;
  - $n =$ number of samples in the training dataset.

## References

1. [Loss Functions in Machine Learning Explained](https://www.datacamp.com/tutorial/loss-function-in-machine-learning)
2. [Choosing Between Mean Squared Error (MSE) and Mean Absolute Error (MAE) in Regression: A Deep Dive](https://medium.com/@nirajan.acharya777/choosing-between-mean-squared-error-mse-and-mean-absolute-error-mae-in-regression-a-deep-dive-c16b4eeee603)
3. [Huber loss](https://en.wikipedia.org/wiki/Huber_loss)