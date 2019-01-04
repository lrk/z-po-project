---
layout: post
title: "OP-Z sound engines and sample packs, a first dive into the Z upside-down world"
---

OP-Z sound engines and sample packs, a first dive into the Z upside-down world

#Disclaimer

I write this article for educational purposes as I like to learn and explore possibilities of devices that I purchase, that's all.

Please note that I will not explain operations described in OP-Z user guides. If you don't know how to do something, please RTFM !
You will learn a lot of thing about the device you bougth.

**I will not be responsible for any misuse of patented / copyrighted content.**

**I will not be responsible if you harm of broke your device / computer / cat or dog.**


#A brief summary

With Teenage Engineering's latest instrument, OP-Z, comes default sound engines and sample packs.
You can choose to use the default ones or to use your own. Create them with available community tools, then with OP-Z disk mode, upload them in a free available slot in instrument track's folder.

Available instrument track's folders:
```
1-kick
2-snare
3-perc
4-fx
5-bass
6-lead
7-arpeggio
8-chord
```

Each folder has 10 subfolders matching the 10 plug slots named from `01` to `10`.


#A word about the OP-Z filesystem and filenames

Open your OP-Z in disk mode, go to the `samplepacks` folder and subfolders, you will notice some empty files for the default sounds.
Wait what, OP-Z can work with empty files ? Where are my default samples ???

If you look closely at the filenames, you will notice they all begin with a tilde character.

```
~26.engine
~CuckooC_keypawn.aif
```

The `how_to_import.txt` file, available at the OP-Z root folder, explain that duplicate files are renamed by OP-Z with a tilde at the beginning to optimize space.

If you read the 'import.log' file, you will notice some YAFFS2 related stuffs:

```
[IMPORT STARTED]
Reading content disk...SUCCESS
Calculating used sample space...0.0/24.0 MB
Syncronizing rejected
Syncronizing bounces
  removing /yaffs2/user/bounces/bounce01/project.opz
  removing /yaffs2/user/bounces/bounce01/bounce.wav
Syncronizing config
  importing config/general.json...SUCCESS
Syncronizing projects
Rebuilding plug definitions...SUCCESS
[IMPORT COMPLETE]
```

We can assume that, like in most electronic devices, the inner operating system is a Linux flavor and it use YAFFS2 as filesystem. But for what Linux filesystems are good for ? Mounting points !

So we can assume there is more than one folder inside the OP-Z where samples and sound engines are stored.

But, what if there is more default samples and sound engines available than these displayed in the OP-Z mobile app ?!

As I can not currently gain access to the embedded filesystem, my sole option is to look at the OP-Z mobile app and dive into it's files.


#What have i found


At first glance, the iPhone app will show you some default sound engines and samples in the `Configurator` page.

But if you manage to gan access to the app files, you will discover several things.

First, most of the default resources  available in the embedded filesystem (sound engines, samples, config files), are available in the mobile app.
Second, and it's a win, there is more samples available than these showed in the OP-Z's application !!! It's not much, but it open more possibilities.

The full list of Samples available right now:

```
AlainFX.aif
AlainKicks.aif
AlainPerc.aif
AlainSnares.aif
CuckooC_keypawn.aif
CuckooC_open.aif
CuckooFX.aif
CuckooKicks.aif
CuckooPerc.aif
CuckooSnares.aif
GrantFX.aif
SebaFX.aif
SebaKicks.aif
SebaPerc.aif
SebaSnares.aif
TeFX.aif
TeKicks.aif
TePercs.aif
TeSnares.aif
UserSampler.aif
redFX.aif
redKicks.aif
redPerc.aif
redSnares.aif
sfx.aif
zVinyl.aif
```

The `sfx.aif` file contain all sounds for the metronome, `UserSampler.aif` sound like an easter egg, it only say "Hello, this is OP-ZED".

Fun facts, OP-Z voice call herself OP-ZED, and it's a girl !!!

Bonus, you can re-upload thoses samples to the OP-Z with a new name, or simply create an empty file with the same filename and the tilde prefix.

Be careful, not all files will work for all instruments, and I experienced many OP-Z's app and device crash when using some of them.


#God dammit ! It crashed !

Ok, exploring undocumented features may lead sooner or later to some device crash.

If it append to you, you can try the following steps, provided as-is.

- if it's not already the case, and if you can, reboot your device in disk mode and backup everything.
- reboot the device in upgrade mode and reset it to factory default.


#That's all folks

That's all for today, there is many many thing i want to try and explore, i'll try to keep up and document what i find.
Enjoy.
