# Driver Drowsiness Detection

A real-time driver drowsiness detection system built with Python, OpenCV, Haar Cascade classifiers, and a CNN model.

The system uses a webcam to detect the driver's face and eyes, classifies each eye as **Open** or **Closed**, and raises an alarm when the eyes remain closed for several frames.

## How It Works

The system works in the following steps:

1. The webcam captures live video frames.
2. Each frame is converted from BGR to grayscale.
3. Haar Cascade classifiers detect the driver's face and eyes.
4. The detected eye regions are cropped from the frame.
5. Each eye image is converted to grayscale, resized to **24 × 24**, and normalized.
6. The trained CNN classifies each eye as **Open** or **Closed**.
7. A running score tracks consecutive closed-eye detections.
8. When the score goes above the threshold, an alarm is played and a red border is shown around the video.

### Pipeline

```text
Webcam
   ↓
Grayscale Conversion
   ↓
Face Detection
   ↓
Left / Right Eye Detection
   ↓
Eye Cropping
   ↓
24 × 24 Resize + Normalization
   ↓
CNN Classification
   ↓
Open / Closed
   ↓
Drowsiness Score
   ↓
Score > 15
   ↓
Alarm + Warning
```

## CNN Model

The CNN is trained to classify eye images into two classes:

- `Closed`
- `Open`

The input to the network is a **24 × 24 grayscale image**.

The model contains:

```text
Input: 24 × 24 × 1
       ↓
Conv2D (32 filters)
       ↓
MaxPooling
       ↓
Conv2D (32 filters)
       ↓
MaxPooling
       ↓
Conv2D (64 filters)
       ↓
MaxPooling
       ↓
Dropout
       ↓
Flatten
       ↓
Dense (128)
       ↓
Dropout
       ↓
Dense (2) + Softmax
```

The model uses the **Adam optimizer**, categorical cross-entropy loss, ReLU activations, and dropout for regularization.

The trained model is saved as:

```text
models/cnnCat2.h5
```

The training script trains the model for **15 epochs** using separate training and validation folders. ([GitHub](https://github.com/shsarv/Machine-Learning-Projects/blob/main/Drowsiness%20detection%20%5BOPEN%20CV%5D/model.py))

## Drowsiness Detection Logic

The main detection script maintains a variable called `score`.

- If both eyes are classified as closed, the score increases by 1.
- If the eyes are open, the score decreases by 1.
- The score is never allowed to become negative.
- When the score becomes greater than **15**, the alarm is triggered.

This prevents the system from immediately declaring someone drowsy because of a single incorrect prediction or normal blink.

When drowsiness is detected:

- `alarm.wav` is played.
- A red border appears around the video.
- The current score is displayed on the screen.
- A captured frame is saved as `image.jpg`. ([GitHub](https://github.com/shsarv/Machine-Learning-Projects/blob/main/Drowsiness%20detection%20%5BOPEN%20CV%5D/drowsinessdetection.py))

## Project Structure

```text
Drowsiness-Detection/
│
├── haar cascade files/
│   ├── haarcascade_frontalface_alt.xml
│   ├── haarcascade_lefteye_2splits.xml
│   └── haarcascade_righteye_2splits.xml
│
├── models/
│   └── cnnCat2.h5
│
├── drowsinessdetection.py
├── model.py
├── alarm.wav
└── README.md
```

### `drowsinessdetection.py`

This is the main program.

It handles:

- Webcam input
- Face detection
- Eye detection
- Eye preprocessing
- CNN prediction
- Drowsiness scoring
- Alarm playback
- Video display

### `model.py`

This file defines and trains the CNN used for eye-state classification.

It loads images from the training and validation directories, preprocesses them, trains the CNN, and saves the trained model.

### `haar cascade files/`

These are pre-trained OpenCV classifiers.

- `haarcascade_frontalface_alt.xml` detects the face.
- `haarcascade_lefteye_2splits.xml` detects the left eye.
- `haarcascade_righteye_2splits.xml` detects the right eye.

They are used for locating the regions that are passed to the CNN. ([GitHub](https://github.com/shsarv/Machine-Learning-Projects/tree/main/Drowsiness%20detection%20%5BOPEN%20CV%5D))

### `models/cnnCat2.h5`

This contains the trained CNN weights used during real-time detection.

### `alarm.wav`

Audio played when the drowsiness score crosses the threshold.

## Dataset

The CNN is trained on eye images divided into two categories:

```text
Open
Closed
```

The original project describes a custom dataset of roughly **7,000 eye images** collected from webcam frames and manually cleaned before training. ([GitHub](https://github.com/shsarv/Machine-Learning-Projects/tree/main/Drowsiness%20detection%20%5BOPEN%20CV%5D))

For training your own model, the data should follow a directory structure such as:

```text
data/
├── train/
│   ├── Open/
│   └── Closed/
│
└── valid/
    ├── Open/
    └── Closed/
```

## Requirements

The project uses:

- Python
- OpenCV
- NumPy
- Keras / TensorFlow
- Pygame

A webcam is also required for real-time detection. ([GitHub](https://github.com/shsarv/Machine-Learning-Projects/tree/main/Drowsiness%20detection%20%5BOPEN%20CV%5D))

Install the main dependencies with:

```bash
pip install opencv-python numpy pygame tensorflow keras
```

## Running the Project

After installing the dependencies and placing the trained model inside `models/`, run:

```bash
python drowsinessdetection.py
```

The webcam will open automatically.

The program continuously processes the video until `q` is pressed.

## Training the Model

If you want to train the CNN yourself, prepare the dataset in the required folder structure and run:

```bash
python model.py
```

The trained model will be saved as:

```text
models/cnnCat2.h5
```

You can then run the main detector:

```bash
python drowsinessdetection.py
```

## Limitations

This approach has some practical limitations:

- Performance can decrease in poor lighting.
- Glasses or sunglasses can interfere with eye detection.
- Large head rotations can make Haar Cascade detection unreliable.
- A wink can affect the scoring logic.
- The system only uses eye closure and does not detect yawning or other signs of fatigue.
- The system should be treated as a warning/assistive system, not as a safety-critical replacement for attentive driving. ([GitHub](https://github.com/shsarv/Machine-Learning-Projects/tree/main/Drowsiness%20detection%20%5BOPEN%20CV%5D))

## Technologies Used

| Component | Technology |
|---|---|
| Language | Python |
| Computer Vision | OpenCV |
| Face/Eye Detection | Haar Cascades |
| Eye Classification | CNN |
| Deep Learning | Keras / TensorFlow |
| Audio Alert | Pygame |
| Image Processing | NumPy |
