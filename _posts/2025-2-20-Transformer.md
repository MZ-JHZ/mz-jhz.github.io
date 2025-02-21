---
layout: post
title: What is Transformer
description: What is Transformer
tag: ML
---

# What is Transformer

The Transformer model is a deep learning model based on the **self-attention mechanism**. It is specifically designed for **natural language processing (NLP) tasks**, excelling at sequence-to-sequence tasks such as **machine translation, text generation, and summarization**.

The key innovation of the Transformer is that it relies entirely on **self-attention mechanisms** to capture dependencies within sequences. It eliminates the **sequential dependency** and **computational bottlenecks** of traditional **recurrent neural networks (RNNs)**.

## **Architecture of Transformer**

The Transformer model consists of an **Encoder** and a **Decoder**, each composed of multiple identical layers. The **Encoder** converts the input sequence into an abstract representation, while the **Decoder** generates the target sequence step by step based on **the Encoder's output** and **previously generated outputs**.

## **Encoder**

The Encoder converts the input sequence into a higher-level feature representation. It consists of multiple identical encoder layers stacked together.

Each encoder layer has two main sub-layers: **multi-head attention** and **feed-forward neural network (FFN)**. Each sub-layer is followed by residual connections and layer normalization.

## **Decoder**

The Decoder generates the final output sequence step by step using the output from the Encoder and the already generated parts of the target sequence.

Each decoder layer has three main sub-layers: **Masked Multi-Head Self-Attention**, **Encoder-Decoder Multi-Head Attention**, and **Feed-Forward Neural Network**. Each sub-layer is also followed by residual connections and layer normalization.

## **Positional Encoding**

Unlike RNNs, the Transformer does not have an inherent sequential structure, making it unable to capture positional information directly. To address this, **positional encoding** is added to the input embeddings to provide the model with positional information, allowing it to recognize the order of words in the input sequence.

### **Input Embedding**

For example, when inputting "I like cats.", each word is first converted into a **fixed-dimension word embedding vector**.

If the embedding dimension is `d_model` and the input sequence length is `N`, the embedded input matrix is represented as `X_N*d_model` (N rows and d_model columns, where each row represents a word, and all words share the same dimensionality).

```python
import math
import torch
import torch.nn as nn

class TokenEmbedding(nn.Embedding):
    def __init__(self, vocab_size, d_model):
        super(TokenEmbedding, self).__init__(vocab_size, d_model, padding_idx=1)
```

### **Positional Encoding**

Positional encoding provides each position in the sequence with a unique representation.

#### Formula:

PE(pos, 2i) = sin( pos / 10000^(2i/d_model) )
 PE(pos, 2i+1) = cos( pos / 10000^(2i/d_model) )

Here, `pos` represents the position index in the sequence, and `i` represents the index of the encoding vector's dimension.

#### **Code Implementation:**

```python
import math
import torch
import torch.nn as nn

class PositionalEncoding(nn.Module):
    def __init__(self, d_model, max_len=5000):
        super(PositionalEncoding, self).__init__()
        self.encoding = torch.zeros(max_len, d_model)
        self.encoding.requires_grad = False
        pos = torch.arange(0, max_len).float().unsqueeze(dim=1)
        _2i = torch.arange(0, d_model, step=2).float()

        self.encoding[:, 0::2] = torch.sin(pos / (10000 ** (_2i / d_model)))
        self.encoding[:, 1::2] = torch.cos(pos / (10000 ** (_2i / d_model)))

    def forward(self, x):
        batch_size, seq_len = x.size()
        return x + self.encoding[:seq_len, :]
```

## **Self-Attention (Core of Transformer)**

Attention mechanisms mimic human selective focus when processing information. The goal is to allow each word in a sequence to focus on other words to capture long-range dependencies.

#### 1) Linear Transformation

For each word in the input sequence, corresponding Query, Key, and Value vectors are generated. These vectors are typically obtained by passing the word's embedding vector through a **linear transformation layer**.

The input XX is mapped to the **query matrix** Q, **key matrix** K, and **value matrix** VV through linear transformations:

Q = X * W_Q,  K = X * W_K, V = X * W_V

where  \(W_Q\)、\(W_K\) 和 \(W_V\) are trainable weight matrices.

#### 2) Compute Attention Scores

The scaled attention scores are computed based on the similarity (dot product) between Q and K. This represents the degree of association between each word and the current word. (The dot product measures similarity: if two vectors are perpendicular, they are completely unrelated.)

Using the dot product to compute similarity between **queries** and **keys**, followed by **scaling** to prevent excessively large values:

Attention Scores = QK^T / sqrt(d_k)

#### 3) Normalize Attention Scores

The attention scores matrix is passed through a **Softmax function** to convert it into attention weights:

Attention Weight = softmax（Attention Scores）

#### 4) Compute Weighted Value Representation

The attention weights are applied to the value matrix VV to obtain the self-attention output. This weighted sum vector serves as a new representation of the current word, enriched with contextual information:

Attention（Q,K,V） = Attention Weight * V

#### 5) Code Implementation

Before implementing the code, it's important to understand the linear layer. **`nn.Linear`** is a **fully connected layer (linear layer)** module provided by PyTorch, implementing the following linear transformation internally:

output=X*W^T + b

where XX is the input tensor, WW is a trainable weight matrix, and bb is a trainable bias vector.

`nn.Linear(in_features, out_features)`:

- **`in_features`**: The size of the input vector
- **`out_features`**: The size of the output vector

According to the original paper, `d_model` is divided into multiple subspaces, where each head learns a different "attention subspace" to enable the model to capture richer patterns.

`w_combination`: This is used for the final feature mapping, projecting the results of multi-head attention back to the original dimension.

```python
class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, n_head):
        super(MultiHeadAttention, self).__init__()
        self.n_head = n_head
        self.d_model = d_model
        self.n_d = d_model // n_head
        self.w_q = nn.Linear(d_model, d_model)
        self.w_k = nn.Linear(d_model, d_model)
        self.w_v = nn.Linear(d_model, d_model)
        self.w_combination = nn.Linear(d_model, d_model)
        self.softmax = nn.Softmax(dim=-1)

    def forward(self, q, k, v, mask=None):
        batch, seq_len, _ = q.shape
        q = self.w_q(q).view(batch, seq_len, self.n_head, self.n_d).permute(0, 2, 1, 3)
        k = self.w_k(k).view(batch, seq_len, self.n_head, self.n_d).permute(0, 2, 1, 3)
        v = self.w_v(v).view(batch, seq_len, self.n_head, self.n_d).permute(0, 2, 1, 3)
        score = torch.matmul(q, k.transpose(-2, -1)) / math.sqrt(self.n_d)

        if mask is not None:
            score = score.masked_fill(mask == 0, float('-inf'))

        attention_weights = self.softmax(score)
        attention_output = torch.matmul(attention_weights, v)
        attention_output = attention_output.permute(0, 2, 1, 3).contiguous().view(batch, seq_len, self.d_model)
        output = self.w_combination(attention_output)
        return output
```

## **Layer Normalization**

This code implements **LayerNorm**, which is used to normalize the **last dimension**

```python
class LayerNorm(nn.Module):
    def __init__(self, d_model, eps=1e-12):
        super(LayerNorm, self).__init__()
        self.gamma = nn.Parameter(torch.ones(d_model))
        self.beta = nn.Parameter(torch.zeros(d_model))
        self.eps = eps

    def forward(self, x):
        mean = x.mean(-1, keepdim=True)
        var = x.var(-1, unbiased=False, keepdim=True)
        out = (x - mean) / torch.sqrt(var + self.eps)
        out = self.gamma * out + self.beta
        return out
```

1. `self.gamma`: scaling parameter, initial value is all `1`, shape `(d_model,)`.
2. `self.beta`: offset parameter, initial value is all `0`, shape `(d_model,)`.
3. `self.eps`: a small value (`1e-12`) to prevent division by zero, used to calculate the standard deviation.

Gamma and beta are trainable parameters that will be continuously optimized during training. Calculate the mean and standard deviation of the last dimension

## **Feed-Forward Neural Network (FFN)**

In each encoder and decoder layer, the output of the multi-head self-attention layer is further processed by a feed-forward neural network.

This network consists of two fully connected layers, usually using ReLU as the activation function.

```python
class FFN(nn.Module):
    def __init__(self, d_model, hidden_dim):
        super(FFN, self).__init__()
        self.fc1 = nn.Linear(d_model, hidden_dim)
        self.relu = nn.ReLU()
        self.fc2 = nn.Linear(hidden_dim, d_model)

    def forward(self, x):
        return self.fc2(self.relu(self.fc1(x)))
```

## **Transformer Core Workflow**

Suppose we have an input data -> We process 2 samples at a time, each sample has 4 tokens, and the feature dimension of each token is 8 (Transformer defaults to 512, we use 8 to simplify). 2 attention heads, vocabulary size is 10

So the dimension of x is (batch_size, seq_len, d_model) = (2,4,8)

Then it goes through **Multi-Head Attention**, **LayerNorm**, **Feed-Forward**, and then outputs.

1）Enter the initial word

```python
import torch
import torch.nn as nn

batch_size = 2
seq_len = 4
d_model = 8
vocab_size = 10
tokens = torch.randint(0, vocab_size, (batch_size, seq_len))
print(tokens.shape)  # (2, 4)
embedding_layer = nn.Embedding(vocab_size, d_model)  
embedded = embedding_layer(tokens)
print( embedded.shape)  # (2, 4, 8)

import math

def positional_encoding(seq_len, d_model):
    pe = torch.zeros(seq_len, d_model)  # (seq_len, d_model)
    position = torch.arange(0, seq_len).unsqueeze(1)  # (seq_len, 1)
    div_term = torch.exp(torch.arange(0, d_model, 2) * (-math.log(10000.0) / d_model))

    pe[:, 0::2] = torch.sin(position * div_term)
    pe[:, 1::2] = torch.cos(position * div_term)
    return pe


pos_encoding = positional_encoding(seq_len, d_model)
print( pos_encoding.shape)  # (4, 8)

embedded += pos_encoding
print( embedded.shape)  # (2, 4, 8)

```

2）Let's first define the dimension of x

```)python
import torch

x = torch.randn(2,4,8) # (batch_size, seq_len, d_model)
```

3） Then go to the self-attention layer

```python
w_q = torch.nn.Linear(d_model, d_model)  
w_k = torch.nn.Linear(d_model, d_model)
w_v = torch.nn.Linear(d_model, d_model)

q = w_q(x)  
k = w_k(x)  
v = w_v(x)
print("Q, K, V:", q.shape)  # (batch_size, seq_len, d_model) -> (2,4,8)
```

4）Divide into multiple heads Note: d_k = d_model / n_head = 8 / 2 = 4

```python
n_head = 2
d_k = d_model // n_head

q = q.view(2, 4, 2, 4).permute(0, 2, 1, 3)  # (batch_size, n_head, seq_len, d_k)
k = k.view(2, 4, 2, 4).permute(0, 2, 1, 3)
v = v.view(2, 4, 2, 4).permute(0, 2, 1, 3)

print("Q, K, V after reshape:", q.shape) # (batch_size, n_head, seq_len, d_k) -> (2,2,4,4)
```

5）Calculating attention scores

```python
attn_score = torch.matmul(q, k.transpose(-2, -1)) / torch.sqrt(torch.tensor(d_k))
print("Attention Score:", attn_score.shape) # attn_score.shape = (2, 2, 4, 4)
```

6）计算注意力权重

```python
attn_weights = torch.nn.functional.softmax(attn_score, dim=-1)
print("Attention Weights:", attn_weights.shape) # attn_weight.shape = (2, 2, 4, 4)
```

7）Computing attention output

```python
attn_output = torch.matmul(attn_weights, v)
print("Attention Output:", attn_output.shape) # attn_output.shape = (2, 2, 4, 4)
```

8）Merging multiple attention heads

```python
attn_output = attn_output.permute(0, 2, 1, 3).contiguous().view(2, 4, 8)
print("Final Attention Output:", attn_output.shape) # attn_output = (2,4,8)
```

8） layernorm

```python
layer_norm = torch.nn.LayerNorm(d_model)
x = layer_norm(attn_output + x)
print("LayerNorm Output:", x.shape) # x = (2,4,8)
```

10）FNN

```python
ffn = torch.nn.Sequential(
    torch.nn.Linear(d_model, 16),
    torch.nn.ReLU(),
    torch.nn.Linear(16, d_model)
)
ffn_output = ffn(x)
print("FFN Output:", ffn_output.shape) # ffn_output = (2,4,8)

x = layer_norm(ffn_output + x)
print("Final Encoder Output:", x.shape) # x = (2,4,8)
```
