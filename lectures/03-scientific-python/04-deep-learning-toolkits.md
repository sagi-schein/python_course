# Deep Learning Toolkits *(optional)*

> **Module:** Scientific Python · **Lecture 4 of 4** · *Optional / advanced*

## Learning objectives

- Understand what deep learning is and when to use it
- Know the major toolkits and how they differ
- Grasp tensors, autograd, and the training loop
- Build a small neural network in PyTorch
- Use a high-level API (Keras) for fast prototyping
- Know good practice and where to go next

---

## 1. What is deep learning?

Deep learning trains **neural networks** — layers of weighted connections — to learn patterns directly from data, without hand-engineered rules. It powers image recognition, language models, speech, recommendation, and more.

A network learns by:

1. **Forward pass** — compute a prediction.
2. **Loss** — measure how wrong it is.
3. **Backward pass** — compute gradients of the loss w.r.t. each weight (backpropagation).
4. **Update** — nudge weights to reduce the loss (gradient descent).

Repeat over many batches and **epochs**.

> Use deep learning when you have lots of data and complex patterns (images, text, audio). For small tabular datasets, classical ML (scikit-learn, gradient boosting) is often better and simpler.

---

## 2. The toolkit landscape

| Toolkit | Notes |
|---------|-------|
| **PyTorch** | Most popular for research; dynamic, Pythonic |
| **TensorFlow** | Production-focused, mature deployment ecosystem |
| **Keras** | High-level API (runs on TF/JAX/PyTorch); fast to prototype |
| **JAX** | NumPy-like with autograd + JIT; cutting-edge research |
| **scikit-learn** | Classical ML, not deep learning, but essential |

```
$ pip install torch scikit-learn
```

---

## 3. Tensors and autograd (PyTorch)

A **tensor** is NumPy's array with two superpowers: it can live on a GPU and it tracks gradients.

```python
import torch

x = torch.tensor([1.0, 2.0, 3.0])
x * 2                       # tensor([2., 4., 6.])
x.mean()

# Autograd: track operations to compute gradients
w = torch.tensor(2.0, requires_grad=True)
y = w ** 2 + 3 * w          # some function of w
y.backward()                # compute dy/dw
print(w.grad)               # 2*w + 3 = 7.0
```

This automatic differentiation is what makes training networks practical.

---

## 4. A neural network in PyTorch

```python
import torch
import torch.nn as nn

# Define a small feed-forward network
model = nn.Sequential(
    nn.Linear(4, 16),     # 4 input features -> 16
    nn.ReLU(),
    nn.Linear(16, 3),     # -> 3 output classes
)

loss_fn = nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.01)
```

### The training loop

```python
for epoch in range(100):
    optimizer.zero_grad()           # 1. reset gradients
    outputs = model(X_train)        # 2. forward pass
    loss = loss_fn(outputs, y_train) # 3. compute loss
    loss.backward()                 # 4. backprop
    optimizer.step()                # 5. update weights

    if epoch % 10 == 0:
        print(f"epoch {epoch}: loss {loss.item():.4f}")
```

These five steps — **zero, forward, loss, backward, step** — are the universal rhythm of training.

---

## 5. The same idea in Keras (higher level)

```python
import keras
from keras import layers

model = keras.Sequential([
    layers.Dense(16, activation="relu", input_shape=(4,)),
    layers.Dense(3, activation="softmax"),
])

model.compile(optimizer="adam",
              loss="sparse_categorical_crossentropy",
              metrics=["accuracy"])

model.fit(X_train, y_train, epochs=100, batch_size=32, validation_split=0.2)
model.evaluate(X_test, y_test)
```

Keras hides the loop — great for prototyping; PyTorch's explicit loop gives more control.

---

## 6. Key concepts to know

- **Activation functions** (ReLU, sigmoid, softmax) add non-linearity.
- **Loss functions** — cross-entropy for classification, MSE for regression.
- **Optimizers** — SGD, Adam — control how weights update.
- **Overfitting** — model memorizes training data; combat with more data, dropout, regularization, early stopping.
- **Train/validation/test split** — never evaluate on data you trained on.
- **GPU** — move model and data with `.to("cuda")` for large training jobs.

---

## 7. Practical workflow

1. **Get and split data** (train / val / test).
2. **Preprocess** — normalize inputs, encode labels.
3. **Start simple** — a small model and a baseline.
4. **Train and monitor** both training and validation loss.
5. **Diagnose** — underfitting (raise capacity) vs. overfitting (regularize).
6. **Tune** hyperparameters (learning rate, layers, batch size).
7. **Evaluate once** on the held-out test set.

> Don't reinvent architectures — use **pretrained models** and **transfer learning** (e.g. Hugging Face) for vision and NLP whenever possible.

---

## Summary

- Deep learning trains layered networks via forward pass → loss → backprop → update.
- PyTorch (flexible) and TensorFlow/Keras (production, high-level) lead the field; scikit-learn covers classical ML.
- Tensors + autograd make training practical; gradients flow automatically.
- The training loop is always: zero grad, forward, loss, backward, step.
- Guard against overfitting and always keep a held-out test set.
- Prefer pretrained models and transfer learning over building from scratch.

## Exercises

1. Use autograd to compute the derivative of `f(w) = w³ + 2w` at `w = 4`; verify by hand.
2. Build and train a PyTorch network to classify the Iris dataset (4 features, 3 classes).
3. Rebuild the same classifier in Keras and compare lines of code and accuracy.
4. Plot training vs. validation loss over epochs and identify where overfitting begins.
5. Use a pretrained image model (e.g. from `torchvision`) to classify a sample image.

## Further reading

- [PyTorch — Learn the Basics](https://pytorch.org/tutorials/beginner/basics/intro.html)
- [Keras Developer Guides](https://keras.io/guides/)
- [scikit-learn user guide](https://scikit-learn.org/stable/user_guide.html)
- 3Blue1Brown, *Neural Networks* (video series)
