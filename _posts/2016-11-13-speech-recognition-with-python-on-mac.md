---
layout: post
title: Speech Recognition with Python on a Mac
date: '2016-11-13T16:58:00.003-05:00'
author: Andrew Silva
tags: 
modified_time: '2016-11-13T16:58:49.930-05:00'
blogger_id: tag:blogger.com,1999:blog-8622339093465720931.post-6911381618708976791
blogger_orig_url: http://www.andrew-silva.com/2016/11/speech-recognition-with-python-on-mac.html
---

Setting up speech recognition and transcription on a Mac is surprisingly easy using Python. I'm using Python 3.5, but the library I'll be using works for 2.7 and 3.x.

First you'll need to have homebrew, which you can find [here][brew]. Homebrew is an excellent package manager for Mac, and makes it easy to install a better version of Python than what comes bundled with the Mac.

Once you have homebrew (and the Python installation of your choice) open up a terminal and run:

```
$ brew install portaudio
```
This installs the requirement for the [Speech Recognition library][sr-py] for Python. Afterwards, you can run: 
```
$ pip install pyaudio 
$ pip install SpeechRecognition
```
And you have the requirements in place! You can test it out by running: 
```
$ python -m speech_recognition
```
And you should see it working in your terminal.

To get it up and running in your own Python script is also very easy. Here is an example using your built-in microphone and the Google Speech API:
```
import speech_recognition as sr

rec = sr.Recognizer()
with sr.Microphone() as source:
	print("I'm listening!")
	audio = rec.listen(source=source)
	try:
		transcription = rec.recognize_google(audio)
	except sr.UnknownValueError:
		transcription = "Google Speech API is confused"
	except sr.RequestError as e:
		transcription = "Request to Google Speech API failed, request error: ", e
	print(transcription)
```

It's that simple!

[brew]: http://brew.sh/
[sr-py]: https://pypi.python.org/pypi/SpeechRecognition/