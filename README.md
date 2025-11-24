# Deep Learning Lab — Practical Explanations + Code

This README contains simple explanations and **the code** for each practical from your Deep Learning Lab Journal.  
(Contents and lab structure referenced from the uploaded journal.) fileciteturn0file0

---

## Practical 1 — Introduction to TensorFlow / Keras

**Explanation:** Import TensorFlow/Keras and check versions; load a sample dataset (MNIST).

**Code (Python):**
```python
# practical_1_imports.py
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Flatten

print("TensorFlow Version:", tf.__version__)

# load sample dataset (MNIST)
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()
print("MNIST Dataset Loaded!")
print("Training Data Shape:", x_train.shape)
print("Testing Data Shape:", x_test.shape)
```

---

## Practical 2 — Loading & Splitting Dataset

**Explanation:** Keras often gives built-in train/test splits; otherwise use `train_test_split`.

**Code:**
```python
# practical_2_load_split.py
import tensorflow as tf
from sklearn.model_selection import train_test_split

# Example using MNIST (already split by Keras)
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()

# If you had a single dataset X, y:
# X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

print("Train:", x_train.shape, y_train.shape)
print("Test :", x_test.shape, y_test.shape)
```

---

## Practical 3 — Data Preprocessing

**Explanation:** Normalization, Flattening, Reshaping, One-hot encoding.

**Code:**
```python
# practical_3_preprocessing.py
import numpy as np
import tensorflow as tf
from tensorflow.keras.utils import to_categorical
import matplotlib.pyplot as plt

(x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()

# 1. Normalization: scale pixel values 0-255 -> 0.0-1.0
x_train = x_train.astype('float32') / 255.0
x_test  = x_test.astype('float32') / 255.0

# 2. Reshape for a Dense network (flatten) or for CNN (add channels)
# For Dense:
x_train_flat = x_train.reshape((x_train.shape[0], -1))   # (60000, 784)
x_test_flat  = x_test.reshape((x_test.shape[0], -1))

# For CNN:
x_train_cnn = x_train.reshape((-1, 28, 28, 1))
x_test_cnn  = x_test.reshape((-1, 28, 28, 1))

# 3. One-hot encode labels (if using categorical_crossentropy)
y_train_cat = to_categorical(y_train, num_classes=10)
y_test_cat  = to_categorical(y_test, num_classes=10)

print("Shapes ->", x_train_flat.shape, x_train_cnn.shape, y_train_cat.shape)
```

---

## Practical 4 — Artificial Neural Networks

### (a) McCulloch-Pitts neuron (ANDNOT)
**Explanation:** Simple threshold neuron implementing x1 AND NOT x2.

**Code (pure Python):**
```python
# practical_4_mcculloch_pitts.py
def andnot(x1, x2):
    # weights and threshold that realize x1 AND NOT x2
    w1, w2 = 1.0, -1.0
    bias = 0.5   # threshold implemented via bias
    s = w1*x1 + w2*x2 + bias
    return 1 if s > 0 else 0

# truth table
inputs = [(0,0),(0,1),(1,0),(1,1)]
for a,b in inputs:
    print(a,b, andnot(a,b))
```

### (b) XOR using a small neural network (Keras + backprop)
**Explanation:** XOR isn't linearly separable; needs a hidden layer.

**Code:**
```python
# practical_4_xor_keras.py
import numpy as np
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense

# XOR dataset
X = np.array([[0,0],[0,1],[1,0],[1,1]])
y = np.array([0,1,1,0])

model = Sequential([
    Dense(4, input_dim=2, activation='relu'),  # hidden layer
    Dense(1, activation='sigmoid')              # output
])

model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
model.fit(X, y, epochs=200, verbose=0)

print("Predictions:")
print(model.predict(X).round())
print("Weights:", model.get_weights())
```

---

## Practical 5 — Regularization Techniques

**Explanation:** Data augmentation, dropout, early stopping — reduce overfitting.

**Code:**
```python
# practical_5_regularization.py
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Conv2D, MaxPooling2D, Flatten, Dense, Dropout
from tensorflow.keras.preprocessing.image import ImageDataGenerator
from tensorflow.keras.callbacks import EarlyStopping

# Simple CNN model
def make_model(input_shape=(28,28,1), num_classes=10):
    model = Sequential([
        Conv2D(32, (3,3), activation='relu', input_shape=input_shape),
        MaxPooling2D((2,2)),
        Dropout(0.25),
        Flatten(),
        Dense(128, activation='relu'),
        Dropout(0.5),
        Dense(num_classes, activation='softmax')
    ])
    model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])
    return model

# Data augmentation example (for images)
datagen = ImageDataGenerator(
    rotation_range=10,
    width_shift_range=0.1,
    height_shift_range=0.1,
    zoom_range=0.1,
    horizontal_flip=False
)

# Early stopping
early_stop = EarlyStopping(monitor='val_loss', patience=5, restore_best_weights=True)
```

---

## Practical 6 — Convolutional Neural Network (CNN)

**Explanation:** Use Conv layers + pooling to learn image features.

**Code (CIFAR-10 example):**
```python
# practical_6_cnn_cifar10.py
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Conv2D, MaxPooling2D, Flatten, Dense, Dropout
from tensorflow.keras.utils import to_categorical

# Load dataset
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.cifar10.load_data()
x_train = x_train.astype('float32') / 255.0
x_test  = x_test.astype('float32') / 255.0
y_train = to_categorical(y_train, 10)
y_test  = to_categorical(y_test, 10)

model = Sequential([
    Conv2D(32, (3,3), activation='relu', input_shape=x_train.shape[1:]),
    MaxPooling2D((2,2)),
    Conv2D(64, (3,3), activation='relu'),
    MaxPooling2D((2,2)),
    Flatten(),
    Dense(128, activation='relu'),
    Dropout(0.5),
    Dense(10, activation='softmax')
])

model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])
model.summary()

# Train (small example)
# model.fit(x_train, y_train, epochs=10, validation_split=0.1, batch_size=64)
```

To predict a local image:
```python
# predict_local_image.py (snippet)
from tensorflow.keras.preprocessing import image
import numpy as np

img = image.load_img('my_image.png', target_size=(32,32))
arr = image.img_to_array(img) / 255.0
arr = np.expand_dims(arr, 0)  # batch dimension
pred = model.predict(arr)
print("Predicted class:", pred.argmax())
```

---

## Practical 7 — Recurrent Neural Network (RNN)

### (a) Character recognition (sequence model)
**Explanation:** RNNs/LSTMs process sequences (e.g., strokes or time-series features).

**Code (simple RNN on character sequences — illustrative):**
```python
# practical_7_rnn_char.py
import numpy as np
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, SimpleRNN, Dense

# Example: tiny dataset of tokenized character sequences
# (This is an illustrative placeholder; real OCR uses CNN+RNN + CTC)
vocab_size = 50
maxlen = 20

X = np.random.randint(1, vocab_size, size=(1000, maxlen))
y = np.random.randint(0, 2, size=(1000, 1))  # dummy binary labels

model = Sequential([
    Embedding(vocab_size, 32, input_length=maxlen),
    SimpleRNN(64),
    Dense(1, activation='sigmoid')
])

model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
# model.fit(X, y, epochs=5, batch_size=32)
```

### (b) Web traffic image classification
Use sequence models if your data has a time dimension. If images only, prefer CNNs (see Practical 6).

---

## Practical 8 — LSTM for Sentiment Analysis

**Explanation:** LSTM remembers context (negations, intensity) — good for sentiment.

**Code (Keras LSTM for sentiment):**
```python
# practical_8_lstm_sentiment.py
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, LSTM, Dense
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

# Example data (replace with your dataset)
sentences = [
    "I loved the movie, it was fantastic",
    "I did not like the film, it was boring",
    "An average movie, not too bad"
]
labels = [1, 0, 1]  # 1 = positive, 0 = negative

tokenizer = Tokenizer(num_words=5000)
tokenizer.fit_on_texts(sentences)
sequences = tokenizer.texts_to_sequences(sentences)
X = pad_sequences(sequences, maxlen=50)
y = tf.keras.utils.to_categorical(labels, num_classes=2)

model = Sequential([
    Embedding(input_dim=5000, output_dim=64, input_length=50),
    LSTM(64),
    Dense(2, activation='softmax')
])
model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])
# model.fit(X, y, epochs=10, batch_size=8)
```

---

## Notes & Next Steps

- Each code snippet is self-contained and suitable for a lab environment.  
- For heavy training, enable GPU acceleration and increase epochs/batch sizes appropriately.  
- If you want, I can:
  - Insert the actual exact code copied *verbatim* from your uploaded PDF pages into this README (I used representative, runnable code here).  
  - Create separate `.py` files for each practical in a zip/repository structure.
  - Add inline outputs, visualizations, or a more polished GitHub README layout with badges and TOC.

---

**Download updated README with code:**  
[sandbox:/mnt/data/Deep_Learning_Lab_Readme.md](/mnt/data/Deep_Learning_Lab_Readme.md)
