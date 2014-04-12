---
layout: post
title: "Random Access Memories and nonsensical metada"
date: 2013-05-25 12:30:00 +0200
comments: true
categories: 
- software
---

I’ve just lost[^1] a full post.
yeap, just finished writing it in gvim, hadn’t saved it nor copied it to wordpress online editor and saw miself forced to reboot my computer because.. I ran out of RAM.  I couldn’t switch programs, nor input anything. It was frozen. And it had plenty of space to use for swap (Mac OS X uses a swap file by default) but somehow it couldn’t handle it.

So what was it about?

Some weirdness I was confronted after ripping the new Daft Punk CD (I couldn’t resist, I bought it the same 21st of May it was launched).

The problem came from some alac rips (it did not happened with flac).

The files showed some strange creation dates when opened with [MPlayer](https://mplayerhq.hu/design7/news.html). Some ordered dates starting in the 20′s and ending in the 80′s.

{% img center /assets/images/2013-05-25-random-access-memories-and-nonsensical-metada/005.jpg 600 MPlayer screenshot including some strange metadata %}

At first I though it was some easter egg but after ripping the files a second time I realised those new files showed some similar dates, but not the same. That’s why I blamed XLD (the program I used to transcode from aiff to alac) but that was another mistake. XLD did a great job in finding metadata in a few seconds and ripping the whole album in under 5 min. I ended up blaming MPlayer, because no other player showed that metadata. THey all showed only ’2013′ as the date (which was also showed in a different line by MPlayer, as shown in the screenshot). Even the OS showed a real creation date which is, as expected, the day the files were created by XLD.

What do you think makes MPlayer show those dates consistently every time I play the songs?

[^1]: sorry Apple, your Darwin kernel behaved properly. It was Firefox (from my beloved [Mozilla](https://www.mozilla.org/)) which was responsible for the soft-crash. I have it setup to ask me if I want to accept cookies on every new website I visit and somehow one of them broke the app (oddly enough, I was, for once, running the release channel Firefox (stable) instead of [Nightly](http://nightly.mozilla.org/) (daily build). That won’t change my mind about the browser and might soon sign up to collaborate with the developers (I came back to Firefox, after having switched to chrome almost at its debut, for [philosophical](https://www.mozilla.org/en-US/mission/) reasons, 2 years ago).

