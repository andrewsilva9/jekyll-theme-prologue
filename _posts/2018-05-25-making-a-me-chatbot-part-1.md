---
layout: post
title: Making a Me-Chatbot (Part 1, Gathering Some Data)
date: '2018-05-25T00:00:00.000-00:00'
author: Andrew Silva
---

Chatbots are cool, and growing in popularity. They're also a very tangible / interactive way of being introduced to many foundational elements of machine learning;. In this post, I'm look specifically at the often-understated challenge of putting together a dataset for any given task. Again, our task here is a chatbot, and it would be fun to make a chatbot that attempts to sound like me. So I'm using my own iMessage history as training data.

### Data Structure:

We need the data to be in a format that the ultimate product is going to expect, so I'm going to be making pairs of sentences. The first sentence in the pair is the input (what somebody says to me), and the second sentence is the output (what I said back). Ultimately, the model will take in the input, generate something, see if it looks at all like the output, and update itself accordingly. That's the idea of machine learning in general...

### Getting iMessage Data:

Fortunately, some people have already made tools for scraping iMessage histories and putting them into a more usable format. I'm going to use [Baskup][baskup-link]. It's super easy and a great tool, all credit goes to Peter Kaminski for building it out. Go ahead and download it, load the .dmg file, turn off the "Backup Attachments" option, and run it. Once it finishes, click "show the files" and move all of those directories into the same directory as [this script][to_txt script] so you have something like this:
```
baskup_output
|
--Friend1
   |
   --Friend1
   --Attachments
--Friend2
   |
   --Friend2
   --Attachments
...
to_txt.sh
```
For convenience, I want to put ".txt" on all of the extension-less files, and to move all of the new .txts into a single "master" directory that I can iterate over later. Running to `to_txt.sh` script does exactly that. Navigate to the directory where you have the baskup output and the `to_txt.sh` script, and just run:
```
$ sh to_txt.sh
```

This adds the extension to all of the conversation files, and moves them into that single master directory located at `~/Desktop/master`. Once that's done, you have all of your conversation history packaged conveniently into a master directory on your desktop, which we will eventually turn into training data and build a chatbot around.

### Up Next...

The next part of this set of posts will cover turning the raw data into input-output pairs for training a model on. The repo where most of this lives is already live, but not well-documented, so hopefully this documentation will serve to help make that repo a bit clearer. But, if you want to just start messing around with iPython notebooks, you're welcome to [check it out][word_embeds].


[baskup-link]:http://peterkaminski.me/baskup/
[to_txt script]:https://github.com/andrewsilva9/my_word_embeds/blob/master/to_txt.sh
[word_embeds]:https://github.com/andrewsilva9/my_word_embeds