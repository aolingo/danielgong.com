---
title: "🎵 MusicTag"
description: "A personal cli tool to decode song names from my iPod"
date: "2020-04-29"
tags: ["go", "golang", "cli tool"]
draft: true
---

### Project Motivation

I was recently trying to transfer songs from my old iPod Nano to my Android phone and I found out that the process was much more complicated than just a simple drag and drop from the iPod's file system.

Firstly, after connecting the iPod to my Windows PC, I had to enable the "Hidden items" view to see the "iPod_Control" folder and the nested "Music" sub-directory. But instead of having all the mp3 files under this one folder, the song files are divided into 14 more sub folders (F00 to F13), and the original song names are all replaced with random, unique 4 capital letters.

![random song names in iPod](/img/mp3meta/musicdir.png "all random names")

However, all the matching song names show up correctly if I open my iPod through iTunes and unfortunately there isn't an option to export that information from iTunes to an non Apple device. So it seems that all the media files were renamed by iTunes and there should be a way to reconvert those names back to its original when given the right information.

### Solution Research

After digging some more in the "iPod_Control" folder, I was able to find a ".restore_playlist" file along with a bunch
of files with an .itdb (itunes database) extension. The former turned out to be a xml file when opened with a text editor
and it seems to list all the songs in the form of its trackPersistentIDs (most likely the primary key/unique id for
each song in the database).

![iTunes library index XML file](/img/mp3meta/itunesxml.png "iTunes library index XML file")



### Future Improvements
