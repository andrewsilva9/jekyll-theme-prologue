---
layout: post
title: Making a Me-Chatbot (Part 2, Structuring Your Data)
date: '2018-05-30T00:00:00.000-00:00'
author: Andrew Silva
---

In my previous post, I went over a pretty simple way to get a good bit of data for a chatbot that will attempt to learn how I speak. It's nice to have a bunch of .txt files with my iMessage history, but it's not easy to convert that into usable training data for a chatbot. I still haven't gotten it quite right, but it's reached a point of "good enough" to get off the ground with. As always, however, there's room for improvement.

As mentioned in my previous post, we need to structure this data into input-output pairs. For this use-case that means "Somebody_Else" - "Me" pairs. There are a couple of questions to answer here though. Do we want to consider every message-response pair? Or does the amount of time between messages nullify them (for example, if I ignore somebody for a while and then initiate a new conversation, my intiation is not a response to the last thing they said). When messages are just images or emojis, how do we handle that?

Well to begin, for each text log, we iterate through our text directory and remove blank sequences:
```
for filename in os.listdir(master_dir):
    master_txt = open(os.path.join(master_dir, filename), 'r').read().lower().split('\n')
    # Remove blank sequences
    seqs_to_remove = []
    for seq_ind in range(len(master_txt)):
        sequence = master_txt[seq_ind]
        sequence_intro = sequence.split(':')[0]
        if sequence_intro.split(' ')[0] == '@':
            continue
        elif len(sequence.split(':')) < 2 or len(sequence.split(':')[1].split(' ')) < 2:
            seqs_to_remove.append(sequence)
            for upcoming_seq in range(seq_ind + 1, len(master_txt)):
                seqs_to_remove.append(master_txt[upcoming_seq])
                if len(master_txt[upcoming_seq].split(' ')) > 2 and master_txt[upcoming_seq].split(' ')[1] == '@':
                    break

    for seq in seqs_to_remove:
        if seq in master_txt:
            master_txt.remove(seq)

```
So this is long and confusing, but basically it takes advantage of structure in the Baskup output. It generally puts messages in the format of:
```
me: hello hello hello
 @ 05/30/18, 10:00 AM
other person: hi hi hi
 @ 05/30/18, 10:05 AM
```
So we can look for colons, and plan around them. As you can see in the snippet above, I am taking advantage of this structure and removing messages that are either blank, or follow up blank messages. It still leaves me with plenty of data, and saves me some effort later.

Next up, I want to start pairing things up. This is where you can start to ask questions like "Should I ignore messages that are more than 15 minutes apart?". I'm not asking those questions. I'm just pairing everything up. But you could, if you wanted to.

First, I remove all of the things I say at the start of a conversation, so that the first speaker is not me (again, I want my responses to people, not people's responses to me).
```
if len(master_txt) < 1:
    continue
while master_txt[0].split(' ')[0] == 'me:' or master_txt[0].split(' ')[1] == '@':
    master_txt.remove(master_txt[0])
    if len(master_txt) < 1:
        break
if len(master_txt) < 1:
    continue
```
You can see a couple of checks to make sure I'm not working with an empty message thread, and otherwise just removing things that don't start with a friend speaking.

Now, finally, we start to pair things up.

```
while index < len(master_txt) - 1:
	# clean_message is a helper function to strip out some unwanted characters
    sequence = clean_message(master_txt[index])
    split_seq = sequence.split(' ')
    if len(split_seq) < 1:
    	# empty sequence that slipped through?
        print sequence
        index += 1
        continue
    speaker = sequence.split(':')[0]
    if len(speaker.split(' ')) > 1 and speaker.split(' ')[1] == '@':
        index += 1
        continue
    sequence = " ".join(str(x) for x in sequence.split(':')[1:]).split(' ')

    # Friend still speaker:
    if speaker != 'me' and speaker == last_speaker:
        current_friend.extend(sequence)
        friend_spoken = True
    # I'm still speaking:
    elif speaker == 'me' and speaker == last_speaker:
        current_me.extend(sequence)
        me_spoken = True
    # Friend speaking for the first time:
    elif speaker != 'me' and friend_spoken is False:
        current_friend.extend(sequence)
        friend_spoken = True
    # I'm speaking for the first time:
    elif speaker == 'me' and me_spoken is False:
        current_me.extend(sequence)
        me_spoken = True
    # We've both spoken and are on me/friend for the second time
    else:
        # Create pair of friend / my response
        if len(current_me) > longest:
            longest = len(current_me)
        if len(current_friend) > longest:
            longest = len(current_friend)
        input_text = " ".join(str(x) for x in current_friend)
        output_text = " ".join(str(x) for x in current_me)
        pairs.append((input_text, output_text))
        # print pairs
        # last speaker will be friend but whatever, set to none
        # debug_pairs.append((current_friend, current_me))
        current_friend = []
        current_me = []
        last_speaker = None
        friend_spoken = False
        me_spoken = False
        # Redo this sequence because it didn't get added to anything
        index -= 1
    index += 1
    last_speaker = speaker
```
All that ugliness and hackishness, but it gets us what we want! A list of pairs, where the first element is somebody else, and the second element is me. Those checks for length are sort of just out of curiosity, but we can also take advantage of that later when we set a max length for the model. Unfortunately, it's likely that your longest message is pretty huge (mine was over a hundred words), and anything that big will probably not work out super well for the model. Especially if your average length is down in the 10s / 20s.

Don't forget to save the data!
```
cPickle.dump(pairs, open('pairs.pkl', 'wb'), protocol=cPickle.HIGHEST_PROTOCOL)
```

Check out the repo for the full file which is [here][word_embeds]. The pairing file is `create_seq_pairs.py`.

### Up Next...

Next up is the actual training of the model! If you want, you can also play around with the messages data that you have now, and look at things like the average length of an input or output message, and the standard deviation of your message lengths, most common words, or things like that. They could come in handy when designing your model, or cleaning up the data. 

[word_embeds]:https://github.com/andrewsilva9/my_word_embeds