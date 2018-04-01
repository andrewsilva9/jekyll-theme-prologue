---
layout: post
title: Facial Expression Recognition with OpenCV 3+ and Python 3
date: '2016-11-09T09:28:00.000-05:00'
author: Andrew Silva
---

Facial expressions are a very interesting problem for a variety of reasons. Of interest to me particularly is trying to mirror facial expressions in robotics, to make people more comfortable or to ease communication between humans and robots. As a starting point, if you want to train your own classifiers on the small dataset that my project references, you'll need to work with the Cohn and Kanade dataset, [available here][small-dataset]. Similarly, the "giant dataset" that my code refers to is the fer2013.csv file that you can [download from Kaggle][giant-dataset]. However, you can just use the models bundled up in my repo, which you can [pull down here][my-repo]. In order to clean up the data, I followed and tweaked a tutorial [over here][tutorial], but it isn't originally in OpenCV3 or Python3.

The basic idea of the facial expression classifier I'm using is a boosted fisherface model. I trained 10 fisherface classifiers built into OpenCV on random subsets of the small dataset. At runtime, each incoming image is scanned for a face, scaled to be the same size as my small dataset images, and then run through all 10 of the classifiers. I take the mode of the 10 responses, and consider that to be the emotion represented by the incoming face.

```
def classify_emotion(self, input_image):
    emotion_guesses = np.zeros((len(self.models), 1))
    for index in range(len(self.models)):
        prediction, confidence = self.models[index].predict(input_image)
        emotion_guesses[index][0] = prediction
    return int(stats.mode(emotion_guesses)[0][0])
```

As for the incoming images, you could supply them yourself. My code will turn on your webcam and use those, so that whoever is in front of the computer will be run through the recognizer in realtime. The performance is actually pretty decent as long as you're dedicating your face to the emotion, and not obscured somehow. Pulling in the webcam using OpenCV is actually very simple:

```
capture = cv2.VideoCapture(0)
    while True:
        ret, frame = capture.read()
        # Our operations on the frame come here
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        face1 = self.face_finder.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=10, minSize=(5, 5),
                                              flags=cv2.CASCADE_SCALE_IMAGE)
        face2 = self.face_finder2.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=10, minSize=(5,5),
                                               flags=cv2.CASCADE_SCALE_IMAGE)
        face3 = self.face_finder3.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=10, minSize=(5, 5),
                                          flags=cv2.CASCADE_SCALE_IMAGE)
        face4 = self.face_finder4.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=10, minSize=(5, 5),
                                          flags=cv2.CASCADE_SCALE_IMAGE)

        # Go over detected faces, stop at first detected face, return empty if no face.
        if len(face1) == 1:
            face = face1[0]
        elif len(face2) == 1:
            face = face2[0]
        elif len(face3) == 1:
            face = face3[0]
        elif len(face4) == 1:
            face = face4[0]
        else:
            continue
        # Cut and save face
        # scale image
        gray = gray[face[1]:face[1] + face[3],
                    face[0]:face[0] + face[2]]
        out = cv2.resize(gray, (self.image_height, self.image_width))  # Resize face so all images have same size
        print(self.emotions[self.classifier.classify_emotion(out)])
```

If you're interested in working with the larger dataset, the files are all there. "train_bigger_emotion_classifier" will get you started by getting the data ready and showing how to train a linear SVM on it, but fair warning: it is hard to do well on that dataset. The winner of the Kaggle competition got something around 77% accuracy, working with a convolutional neural network (something my repo doesn't include).

To run my project, simply throw all of the files into the same directory and run the `emotion_runner.py` file. It'll take a second to load up the models and set up the webcam, and then you'll start seeing classifications of your face!

![PyCharm showing me classifications of my face][screenshot]

Overall, performance isn't too terrible. For happy, neutral, angry, and surprised you can count on it to work pretty reliably. The other emotions in the small dataset (disgust, contempt, sadness, and fear) are tougher to get, presumably because they are very specific in the training data and hard to replicate naturally.

Future work will involve trying to classify facial expressions without needing the mouth as much. If you want to reciprocate an expression while someone is speaking, you need to be able to capture that expression without their full face (since the mouth will be moving to speak). Obviously this is a bigger challenge, but certainly seems to be a more relevant problem.

### References:

1. van Gent, P. (2016). Emotion Recognition With Python, OpenCV and a Face Dataset. A tech blog about fun things with Python and embedded electronics. Retrieved from: http://www.paulvangent.com/2016/04/01/emotion-recognition-with-python-opencv-and-a-face-dataset/

2. Kanade, T., Cohn, J. F., & Tian, Y. (2000). Comprehensive database for facial expression analysis. Proceedings of the Fourth IEEE International Conference on Automatic Face and Gesture Recognition (FG’00), Grenoble, France, 46-53.

3. Lucey, P., Cohn, J. F., Kanade, T., Saragih, J., Ambadar, Z., & Matthews, I. (2010). The Extended Cohn-Kanade Dataset (CK+): A complete expression dataset for action unit and emotion-specified expression. Proceedings of the Third International Workshop on CVPR for Human Communicative Behavior Analysis (CVPR4HB 2010), San Francisco, USA, 94-101.

[small-dataset]: http://www.consortium.ri.cmu.edu/ckagree
[giant-dataset]: https://www.kaggle.com/c/challenges-in-representation-learning-facial-expression-recognition-challenge/data
[my-repo]: https://github.com/andrewsilva9/ExpressionRecognizer
[tutorial]: http://www.paulvangent.com/2016/04/01/emotion-recognition-with-python-opencv-and-a-face-dataset/
[screenshot]: https://github.com/andrewsilva9/jekyll-theme-prologue/blob/master/assets/images/opencv3_er.png

