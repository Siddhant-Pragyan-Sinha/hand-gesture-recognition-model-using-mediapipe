# README


# hand-gesture-recognition-using-mediapipe
Estimate hand pose using MediaPipe(Python version).<br> This is a sample program that recognizes hand signs and finger gestures with a simple MLP using the detected key points.
<br>
![1st](https://github.com/user-attachments/assets/c22564a6-ecd3-46ef-b496-f2b483f1d824)



This repository contains the following contents.
* Sample program
* Hand sign recognition model(TFLite)
* Finger gesture recognition model(TFLite)
* Learning data for hand sign recognition and notebook for learning
* Learning data for finger gesture recognition and notebook for learning

# Requirements
* mediapipe 0.8.1
* OpenCV 3.4.2 or Later
* Tensorflow 2.3.0 or Later<br>tf-nightly 2.5.0.dev or later (Only when creating a TFLite for an LSTM model)
* scikit-learn 0.23.2 or Later (Only if you want to display the confusion matrix)
* matplotlib 3.3.2 or Later (Only if you want to display the confusion matrix)

# Demo
Here's how to run the demo using your webcam.
```bash
python app.py
```

Here is how to run the demo using Docker and a webcam.
```bash
docker build -t hand_gesture .

xhost +local: && \
docker run --rm -it \
--device /dev/video0:/dev/video0 \
-v `pwd`:/home/user/workdir \
-v /tmp/.X11-unix/:/tmp/.X11-unix:rw \
-e DISPLAY=$DISPLAY \
hand_gesture:latest

python app.py
```

The following options can be specified when running the demo.
* --device<br>Specifying the camera device number (Default：0)
* --width<br>Width at the time of camera capture (Default：960)
* --height<br>Height at the time of camera capture (Default：540)
* --use_static_image_mode<br>Whether to use static_image_mode option for MediaPipe inference (Default：Unspecified)
* --min_detection_confidence<br>
Detection confidence threshold (Default：0.5)
* --min_tracking_confidence<br>
Tracking confidence threshold (Default：0.5)

# Directory
<pre>
│  app.py
│  keypoint_classification.ipynb
│  point_history_classification.ipynb
│
├─model
│  ├─keypoint_classifier
│  │  │  keypoint.csv
│  │  │  keypoint_classifier.hdf5
│  │  │  keypoint_classifier.py
│  │  │  keypoint_classifier.tflite
│  │  └─ keypoint_classifier_label.csv
│  │
│  └─point_history_classifier
│      │  point_history.csv
│      │  point_history_classifier.hdf5
│      │  point_history_classifier.py
│      │  point_history_classifier.tflite
│      └─ point_history_classifier_label.csv
│
└─utils
    └─cvfpscalc.py
</pre>
### app.py
This is a sample program for inference.<br>
In addition, learning data (key points) for hand sign recognition,<br>
You can also collect training data (index finger coordinate history) for finger gesture recognition.

### keypoint_classification.ipynb
This is a model training script for hand sign recognition.

### point_history_classification.ipynb
This is a model training script for finger gesture recognition.

### model/keypoint_classifier
This directory stores files related to hand sign recognition.<br>
The following files are stored.
* Training data(keypoint.csv)
* Trained model(keypoint_classifier.tflite)
* Label data(keypoint_classifier_label.csv)
* Inference module(keypoint_classifier.py)

### model/point_history_classifier
This directory stores files related to finger gesture recognition.<br>
The following files are stored.
* Training data(point_history.csv)
* Trained model(point_history_classifier.tflite)
* Label data(point_history_classifier_label.csv)
* Inference module(point_history_classifier.py)

### utils/cvfpscalc.py
This is a module for FPS measurement.

# Training
Hand sign recognition and finger gesture recognition can add and change training data and retrain the model.

### Hand sign recognition training
#### 1.Learning data collection
Press "k" to enter the mode to save key points（displayed as 「MODE:Logging Key Point」）<br>
<img src="https://github.com/user-attachments/assets/9f599a47-4707-4838-8b15-5f9ee867d756" width="60%"><br><br>
If you press "0" to "9", the key points will be added to "model/keypoint_classifier/keypoint.csv" as shown below.<br>
1st column: Pressed number (used as class ID), 2nd and subsequent columns: Key point coordinates<br>
<img src="https://github.com/user-attachments/assets/a2f90fd8-2983-4749-8ae3-b2c5083417fd" width="80%"><br><br>
The key point coordinates are the ones that have undergone the following preprocessing up to ④.<br>
<img src="https://github.com/user-attachments/assets/736cefe0-b615-4c7b-a9ec-c493908210b7" width="80%">
<img src="https://github.com/user-attachments/assets/bdd3a517-2bdf-4bae-8cbb-7c4547223aaa" width="80%"><br><br>
In the initial state, three types of learning data are included: open hand (class ID: 0), close hand (class ID: 1), and pointing (class ID: 2).<br>
If necessary, add 3 or later, or delete the existing data of csv to prepare the training data.<br>
<img src="https://github.com/user-attachments/assets/7f5863f5-a97d-41fb-940c-d669b0af1311" width="25%">　<img src="https://github.com/user-attachments/assets/64f23a41-1392-409b-9a90-023f6b69987f" width="25%">　<img src="https://github.com/user-attachments/assets/b6fc82a8-12f0-49ad-9288-e31ff78c46b2" width="25%">

#### 2.Model training
Open "[keypoint_classification.ipynb](keypoint_classification.ipynb)" in Jupyter Notebook and execute from top to bottom.<br>
To change the number of training data classes, change the value of "NUM_CLASSES = 3" <br>and modify the label of "model/keypoint_classifier/keypoint_classifier_label.csv" as appropriate.<br><br>

#### X.Model structure
The image of the model prepared in "[keypoint_classification.ipynb](keypoint_classification.ipynb)" is as follows.
<img src="https://github.com/user-attachments/assets/b2b7daee-c875-4fdc-ab5b-a73998ca16a9" width="50%"><br><br>

### Finger gesture recognition training
#### 1.Learning data collection
Press "h" to enter the mode to save the history of fingertip coordinates (displayed as "MODE:Logging Point History").<br>
<img src="https://github.com/user-attachments/assets/ee1255d6-13e8-4e57-b426-327896bbd32c" width="60%"><br><br>
If you press "0" to "9", the key points will be added to "model/point_history_classifier/point_history.csv" as shown below.<br>
1st column: Pressed number (used as class ID), 2nd and subsequent columns: Coordinate history<br>
<img src="https://github.com/user-attachments/assets/b4d4ef3d-ec7b-4538-b7ff-c661b3e513e7" width="80%"><br><br>
The key point coordinates are the ones that have undergone the following preprocessing up to ④.<br>
<img src="https://github.com/user-attachments/assets/662a0fdb-8062-4d35-bcf6-acd15bc95362" width="80%"><br><br>
In the initial state, 4 types of learning data are included: stationary (class ID: 0), clockwise (class ID: 1), counterclockwise (class ID: 2), and moving (class ID: 4). <br>
If necessary, add 5 or later, or delete the existing data of csv to prepare the training data.<br>
<img src="https://github.com/user-attachments/assets/0c565662-3966-47b8-8d82-d9f1fa7b4bf3" width="20%">　<img src="https://github.com/user-attachments/assets/b4a1c681-a00b-4266-9bfb-9a863d9477b7" width="20%">　<img src="https://github.com/user-attachments/assets/b0b45534-ab4a-4896-8a0c-37dd6eb56d36" width="20%">　<img src="https://github.com/user-attachments/assets/4c3853f3-721e-4c9f-9497-ac2613e33118" width="20%">

#### 2.Model training
Open "[point_history_classification.ipynb](point_history_classification.ipynb)" in Jupyter Notebook and execute from top to bottom.<br>
To change the number of training data classes, change the value of "NUM_CLASSES = 4" and <br>modify the label of "model/point_history_classifier/point_history_classifier_label.csv" as appropriate. <br><br>

#### X.Model structure
The image of the model prepared in "[point_history_classification.ipynb](point_history_classification.ipynb)" is as follows.
<img src="https://user-images.githubusercontent.com/37477845/102246771-7481ff00-3f42-11eb-8ddf-9e3cc30c5816.png" width="50%"><br>
The model using "LSTM" is as follows. <br>Please change "use_lstm = False" to "True" when using (tf-nightly required (as of 2020/12/16))<br>
<img src="https://user-images.githubusercontent.com/37477845/102246817-8368b180-3f42-11eb-9851-23a7b12467aa.png" width="60%">

# Application example
Here are some application examples.
* [Control DJI Tello drone with Hand gestures](https://towardsdatascience.com/control-dji-tello-drone-with-hand-gestures-b76bd1d4644f)
* [Classifying American Sign Language Alphabets on the OAK-D](https://www.cortic.ca/post/classifying-american-sign-language-alphabets-on-the-oak-d)

# Reference
* [MediaPipe](https://mediapipe.dev/)
* [Kazuhito00/mediapipe-python-sample](https://github.com/Kazuhito00/mediapipe-python-sample)

# License
hand-gesture-recognition-using-mediapipe is under [Apache v2 license](LICENSE).
