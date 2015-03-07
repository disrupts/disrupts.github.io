---
layout: post
title: "OpenBSD's great base system to test &amp; play with MIDI interfaces"
date: 2015-02-14 12:00:00 +0100
published: true
comments: true
categories:
 - software
 - arts
keywords: music, software, midi, OpenBSD
description: 
---

A little background story: I wanted to play some music with my MIDI keyboard
and had a Pentium 4 desktop laying around with no display.

{% img right /assets/images/2015-02-14-openbsd-great-base-system-to-test-and-play-midi/Rock.jpg 200 %}

I had already installed OpenBSD on it to give it a try (and update the Ubuntu
10.10 install I hadn’t touched in years). During the install I decided to keep
the default OpenSSH running and configure the WiFi network. That's it, I had a
server to connect to! And I found out there was a console-based MIDI sequencer
for OpenBSD: [midish][midish]!

The end result is simple, everything works!

Now I’ll give you the details of this fast setup.

The first thing is to check if the OS is registering the MIDI input properly.
The [OpenBSD FAQ][faq] has good info about it. Just connect it, check
<code>dmesg</code> for a new midi interface and cat the input to the screen!
Here’s the one liner from the FAQ:

    aucat -Mq rmidi/1 -o - | hexdump -e '1/1 "%02x\n"'

I kind of messed up everything from here because my USB keyboard (Roland)
wasn’t outputting [standard MIDI][umidi]. Just have a look at the user manual
of your instruments and check if they do, or if there is a way to fallback to
standard (as I found out later).

Now it’s time to start the sequencer and choose a sound module! If you have a
hardware one, good for you. I picked the first I found on the ports tree:
[fluidsynth][fluidsynth]. Pick your install method, I chose the package.

Ok, now all pieces are there, the last step is to set them up.

The [sndiod man-page][sndiod] has some good info but the midish one is pretty
sparce for what we are trying to accomplish. Go to the [official manual][manual],
there the docs are a lot better.

Here’s [my midishrc][midishrc] in case you want some inspiration:

    # Device 0: Roland      # keys: 0, drumpad: 9
    dnew 0 "rmidi/1"    ro
    inew roland_keys    {0 0}
    inew roland_drumpad {0 9}

    # Device 1: fluidsynth  # drums: 9, piano: others
    dnew 1 "midithru/0" wo
    onew fluid_piano    {1 0}
    onew fluid_drums    {1 9}

    # Connect Roland to fluidsynth
    fnew fluidsynth
    fmap {any roland_keys}    {any fluid_piano}
    fmap {any roland_drumpad} {any fluid_drums}

It should be pretty much self explanatory. I use midish to send the midi input
from the keyboard to the synth. You could do that by piping the output of the
interface straight to fluidsynth (with <code>aucat</code> just like the earlier
test) but you lose lots of control and the ability to record what you’re
playing.

Everything is ready, start the soft-synth if you use one, start midish and
enter an interactive mode (<code>i</code> for example). You should be able to
play and hear some sounds!

However I still had a big problem: lag. There was half a second between a key
press and the audio. Not great.

So I went back to the mailing lists (I checked some to setup midish properly).
And there I found the [missing answer][answer]. Alexandre Ratchov, author of
both <code>sndiod</code> and <code>midish</code> had already posted an answer
with the config values he uses to play!

And it worked. Here they are, all together:

    sndiod -r48000 -b240 -z120
    rmidish # (then start with i, or p or other to save, with a midishrc)
    fluidsynth -c 2 -z 128 -g 0.5 /usr/local/share/generaluser-gs/GeneralUser_GS.sf2

A few more comments:

* You may configure <code>rmidish</code> on the fly instead of writting a
  midishrc file, the interpreter works nicely!

* <code>sndiod</code> daemon should already be runnning but with the default
  flags from <code>rc.conf</code>; restarting it without a reboot works without
  issues and if you have a dedicated machine you might want to edit
  <code>rc.conf.local</code>

* Fluidsynth needs an [soundfont][soundfont]. Luckily there’s one in the ports
  tree to: [generaluser-gs-soundfont][generaluser]. It is the one used in the
  previous example.

* You’ll also need to keep fluidsynth in the foreground otherwise it won’t play
  back sound. <code>tmux</code>, <code>dtach</code> or <code>abduco</code>
  should do the trick if you want to stop the connection.

* Ah and the <code>-g 0.5</code> flag to fluidsynth is just to increase the
  gain, it has nothing to do with MIDI.

Just one more thing, there's a [midish mailing list][mail_list]. You might want
to check out the [archive][mail_archive].

*UPDATE 16/02*

I hadn't played piano in 4 years, but it wasn't as bad as I expected. My
fingers felt numb and unresponsive yet my muscle memory was surprisingly good.
After a few minutes practising [*Across the Stars*][acrossstars] the cords were
coming back!

I also managed to bring back from the dead my old (over 12 years old) Yamaha
keyboard. Nothing was wrong with it but around 6 years ago I bought a MIDI/USB
interface to use it as a controller but never managed to get it working (it was
on Windows XP back in the day). Yesterday I remembered the interface was laying
around and after a few minutes, it was connected and <code>dmesg</code> showed
OpenBSD had found it! After a little more [tweaking][midishrc], I was playing
with both keyboards!

*Merci Alexandre*, your code is awesome!


*Image comes from* [*OpenBSD 3.0*][OBSD3.0]

[midish]:       http://www.midish.org
[manual]:       http://www.midish.org/manual.html
[faq]:          http://www.openbsd.org/faq/faq13.html#midi
[umidi]:        http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man4/umidi.4
[sndiod]:       http://www.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man1/sndiod.1
[midishrc]:     https://github.com/disrupts/dotfiles/blob/master/.midishrc
[fluidsynth]:   http://openports.se/audio/fluidsynth
[soundfont]:    https://en.wikipedia.org/wiki/SoundFont
[generaluser]:  http://openports.se/audio/generaluser-gs-soundfont
[answer]:       http://ports.openbsd.narkive.com/IRU25u0b/midi-softsynth
[mail_list]:    http://www.midish.org/mail.html
[mail_archive]: http://www.midish.org/arch/
[OBSD3.0]:      http://openbsd.org/30.html
[acrossstars]:  https://www.youtube.com/watch?v=vIbmfDvQA5U
