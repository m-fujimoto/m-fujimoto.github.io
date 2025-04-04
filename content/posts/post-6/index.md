+++
title = 'Post 6'
date = 2025-02-22T20:01:57+09:00
draft = true
categories = ['OpenBSD']
tags = ['FFmpeg']
+++

# Convert m4a to mp3

I faild to record Radio programme. 
So I tried to record it from Internet Radio by audacity.
I can not use audacity both OpenBSD and FreeBSD.
I can not listen any sound on OpenBSD.
I get "An unrecoverable error has occurred during startup" on FreeBSD.
So I used my smartphone app.
Then I must convert m4a file to mp3 file.
Finally I found how to do it on the Internet.

```
$ ffmpeg -i radio.m4a -ar 44100 -ab 64k radio.mp3
```
