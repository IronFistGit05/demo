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
Image Classifying Code:
```python
#Import Libraries
import tensorflow as tf
from tensorflow.keras.datasets import cifar10
from tensorflow.keras.models import Sequential, load_model
from tensorflow.keras.layers import Dense, Dropout, Activation, Flatten, Conv2D, MaxPooling2D
from tensorflow.keras.utils import to_categorical
import numpy as np
import matplotlib.pyplot as plt
import tensorflow.keras.preprocessing.image as image
from google.colab import files

#Load CIFAR-10 dataset
(x_train, y_train), (x_test, y_test) = cifar10.load_data()

#Normalize(0-1 scalae)
x_train, x_test = x_train / 255.0, x_test / 255.0

#One-hot encode labels
y_train = to_categorical(y_train, 10)
y_test = to_categorical(y_test, 10)

#CIFAR-10 class labels
class_names = ['airplane', 'automobile', 'bird', 'cat', 'deer', 'dog', 'frog', 'horse', 'ship', 'truck']

#Define CNN Model
model = Sequential([
    Conv2D(32, (3, 3), activation='relu', input_shape=(32, 32, 3)),
    MaxPooling2D((2, 2)),
    Conv2D(64, (3, 3), activation='relu'),
    MaxPooling2D((2, 2)),
    Conv2D(128, (3, 3), activation='relu'),
    MaxPooling2D((2, 2)),
    Flatten(),
    Dense(128, activation='relu'),
    Dropout(0.5),
    Dense(10, activation='softmax')
])

#Compile Model
model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])

#Train the model
history = model.fit(x_train, y_train, epochs=10, batch_size=64, validation_split=0.2,verbose=1)

#Evaluate the model
test_loss, test_acc = model.evaluate(x_test, y_test,verbose=2)
print('\nTest accuracy: {:.2f}%'.format(test_acc * 100))

#Save Model
model.save('cnn_cifar10.h5')
print("Model saved as cnn_cifar10.h5")

#Load saved model
model = load_model("cnn_cifar10.h5")

#Upload and Predict
uploaded = files.upload()
for filename in uploaded.keys():
  img = image.load_img(filename, target_size=(32, 32))
  img_array = image.img_to_array(img)
  img_array = np.expand_dims(img_array, axis=0)
  img_array = img_array / 255.0

  #Predict class
  prediction = model.predict(img_array)
  predicted_class = class_names[np.argmax(prediction)]

  #Show image + prediction
  plt.imshow(image.load_img(filename))
  plt.title(f"Prediction: {predicted_class}")
  plt.axis('off')
  plt.show()
```

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
Code for Manual
```python
#Pratical 8
# Step 1: Import Libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, LSTM, Dense, Dropout
from tensorflow.keras.utils import to_categorical
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
from tensorflow.keras.callbacks import EarlyStopping
import tensorflow as tf

print("TensorFlow version:", tf.__version__)

# Step 2: Load Dataset
# Option A: Built-in sample dataset
sample_data = {
    "text": [
        "I love this movie, it’s amazing!",
        "This is the worst product I have ever used.",
        "It was okay, nothing special.",
        "The experience was fantastic!",
        "Not bad, but could be better.",
        "Absolutely terrible service.",
        "I’m really happy with the results!",
        "Mediocre performance, not impressive.",
        "The food was delicious and the staff were friendly.",
        "I didn’t like the quality at all."
    ],
    "sentiment": [
        "Positive", "Negative", "Neutral", "Positive", "Neutral",
        "Negative", "Positive", "Neutral", "Positive", "Negative"
    ]
}

df = pd.DataFrame(sample_data)
print("✅ Sample dataset loaded successfully!")

# Step 3: Optional – Upload your own CSV file
# Uncomment below to use your own file (make sure it has 'text' and 'sentiment' columns)
#from google.colab import files
#uploaded = files.upload()
#df = pd.read_csv(list(uploaded.keys())[0])
#print("✅ Custom dataset loaded successfully!")

# Step 4: Encode sentiment labels (e.g., Negative=0, Neutral=1, Positive=2)
label_encoder = LabelEncoder()
df['label'] = label_encoder.fit_transform(df['sentiment'])
num_classes = len(label_encoder.classes_)
print("\nLabel mapping:", dict(zip(label_encoder.classes_, range(num_classes))))

# Step 5: Text preprocessing
max_words = 5000   # max number of words to keep
max_len = 100      # max length of each sequence

tokenizer = Tokenizer(num_words=max_words, oov_token="<OOV>")
tokenizer.fit_on_texts(df['text'])
X = tokenizer.texts_to_sequences(df['text'])
X = pad_sequences(X, maxlen=max_len, padding='post', truncating='post')

y = to_categorical(df['label'], num_classes=num_classes)

# Step 6: Split into training and testing sets
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Step 7: Build LSTM Model
model = Sequential([
    Embedding(max_words, 128, input_length=max_len),
    LSTM(128, dropout=0.2, recurrent_dropout=0.2),
    Dense(64, activation='relu'),
    Dropout(0.3),
    Dense(num_classes, activation='softmax')
])

model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
model.summary()

# Step 8: Train Model
early_stop = EarlyStopping(monitor='val_loss', patience=3, restore_best_weights=True)

history = model.fit(
    X_train, y_train,
    validation_split=0.2,
    epochs=10,
    batch_size=32,
    callbacks=[early_stop],
    verbose=1
)

# Step 9: Evaluate Model
loss, accuracy = model.evaluate(X_test, y_test, verbose=0)
print(f"\n🧾 Test Loss: {loss:.4f}")
print(f"✅ Test Accuracy: {accuracy:.4f}")

# Step 10: Plot Training History
plt.figure(figsize=(12,5))

plt.subplot(1,2,1)
plt.plot(history.history['accuracy'], label='Train Accuracy')
plt.plot(history.history['val_accuracy'], label='Val Accuracy')
plt.title('Model Accuracy')
plt.xlabel('Epochs')
plt.ylabel('Accuracy')
plt.legend()

plt.subplot(1,2,2)
plt.plot(history.history['loss'], label='Train Loss')
plt.plot(history.history['val_loss'], label='Val Loss')
plt.title('Model Loss')
plt.xlabel('Epochs')
plt.ylabel('Loss')
plt.legend()

plt.show()

# Step 11: Predict Sentiment for New Text
def predict_sentiment(texts):
    seq = tokenizer.texts_to_sequences(texts)
    padded = pad_sequences(seq, maxlen=max_len, padding='post')
    preds = model.predict(padded)
    pred_classes = np.argmax(preds, axis=1)
    results = label_encoder.inverse_transform(pred_classes)
    for t, r in zip(texts, results):
        print(f"Text: {t}\nPredicted Sentiment: {r}\n")

# Example predictions
test_sentences = [
    "I love this movie, it’s amazing",
    "It was an average experience, nothing special.",
    "The product was disappointing and bad.I didn’t like the quality at all"
]
predict_sentiment(test_sentences)

```
---

## Notes & Next Steps

- Each code snippet is self-contained and suitable for a lab environment.  
- For heavy training, enable GPU acceleration and increase epochs/batch sizes appropriately.  
- If you want, I can:
  - Insert the actual exact code copied *verbatim* from your uploaded PDF pages into this README (I used representative, runnable code here).  
  - Create separate `.py` files for each practical in a zip/repository structure.
  - Add inline outputs, visualizations, or a more polished GitHub README layout with badges and TOC.
<img width="360" height="689" alt="image" src="https://github.com/user-attachments/assets/446fa5e7-84b7-487e-8b8e-3ff6a6b80514" />

VIVA Questions
Practical 1 – TensorFlow/Keras Basics
 Q: What is TensorFlow?
 A: TensorFlow is an open-source deep learning framework used to build and train neural networks.
 Q: What is Keras?
 A: A simple high-level API inside TensorFlow for building neural networks easily.
 Q: Why import libraries?
 A: To use prebuilt functions and modules instead of writing everything from scratch.
 Q: What is MNIST?
 A: A dataset of handwritten digit images widely used for image classification tasks.
 
 Practical 2 – Loading and Splitting Dataset
 Q: Why split data?
 A: To train the model on one set and test its performance on another.
 Q: Difference between training and testing data?
 A: Training teaches the model; testing checks accuracy on unseen data.
 Q: Which function is used to split data?
 A: train_test_split() from Scikit-Learn.
 
 Practical 3 – Data Preprocessing
 Q: Why normalize?
 A: To scale data for faster and stable training.
 Q: What is Flattening?
 A: Converting 2D image matrices into 1D vectors.
 Q: What is One-hot encoding?
 A: Turning labels into vector format to be understood by neural networks.
 Q: Why reshape images?
 A: CNN models require images to have channel dimensions.
 
 Practical 4 – ANN
 Q: What is a McCulloch-Pitts neuron?
 A: A basic binary neuron model taking weighted inputs and a threshold.
 Q: What is ANDNOT?
A: A logic function that outputs 1 only when x1=1 and x2=0.
 Q: Why XOR needs multiple layers?
 A: XOR is not linearly separable and needs a hidden layer.
 Q: Which algorithm trains XOR networks?
 A: Backpropagation.
 
 Practical 5 – Regularization Techniques
 Q: What is regularization?
 A: Techniques used to reduce overfitting.
 Q: What is Dropout?
 A: Randomly turning off neurons during training.
 Q: What is Early Stopping?
 A: Stopping training when validation loss stops improving.
 Q: What is Data Augmentation?
 A: Creating modified versions of images to expand the dataset.
 
 Practical 6 – CNN
 Q: Why use CNNs?
 A: They extract important spatial features from images.
 Q: What is Convolution?
 A: Applying small filters to detect patterns in images.
 Q: What is Max Pooling?
 A: Reducing image size to keep only important features.
 Q: Example dataset?
 A: CIFAR-10.
 
 Practical 7 – RNN
 Q: What is RNN?
 A: A neural network that processes sequence or time-based data.
 Q: Why useful for character recognition?
 A: RNNs remember previous steps and use context.
 Q: Drawback of RNN?
A: They suffer from vanishing gradients.

 Practical 8 – LSTM
 Q: What is LSTM?
 A: A type of RNN that handles long-term dependencies using gates.
 Q: Why used in sentiment analysis?
 A: It understands context such as negations like 'not good'.
 Q: Name LSTM gates.
 A: Forget gate, Input gate, Output gate.
 Q: What input does an LSTM take?
 A: Tokenized sequences of text
---

**Download updated README with code:**  
[sandbox:/mnt/data/Deep_Learning_Lab_Readme.md](/mnt/data/Deep_Learning_Lab_Readme.md)
