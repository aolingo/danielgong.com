---
title: "🎵 MusicTag"
description: "A personal cli tool to decode song names from my iPod"
date: "2020-04-29"
tags: ["go", "golang", "cli tool"]
draft: false
---
[Project Repo](https://github.com/aolingo/musictag)

### Motivation

I was recently trying to transfer songs from my old iPod Nano to my Android phone and I found out that the process was much more complicated than just a simple drag and drop from the iPod's file system.

Firstly, after connecting the iPod to my Windows PC, I had to enable the "Hidden items" view to see the *iPod_Control* folder and the nested *Music* sub-directory. But instead of having all the mp3 files under this one folder, the song files are divided into 14 more sub folders (*F00* to *F13*), and the original song names are all replaced with random, unique 4 capital letters.

![random song names in iPod](/img/mp3meta/musicdir.png "all random names")

However, all the matching song names show up correctly if I open my iPod through iTunes and unfortunately there isn't an option to export that information from iTunes to an non Apple device. So it seems that all the media files were renamed by iTunes and there should be a way to reconvert those names back to its original when given the right information.

### Solution Research

Upon further inspection of the song files, I noticed that some of them happened to have metadata containing actual information
related to that file such as song name, album name, artist name and etc. Thus, we can just convert the song names back
to its original by reading out the "Title" field in the mp3 file's metadata.

![song metadata in properties](/img/mp3meta/properties.png)

Now we just need to find out the format of the metadata in a mp3 file. After a quick online search, the de facto standard for metadata in MP3 files is [ID3](https://en.wikipedia.org/wiki/ID3) and the two most common specifications are **ID3v1** and **ID3v2** (eg. the former is located in the last 128 bytes of a mp3 file while the latter is usually at the
start of a file and doesn't have a fixed size).

### Implementation 

With the above information in hand, I've decided to write a basic CLI tool in Go (I chose Go for this project as an 
additional learning experience for the language) that will take in a base directory, such as the **Music** folder in the iPod,
and it will replace the name of all **.mp3** files in it (including all nested folders such as *F00* - *F13*) with the *Title* in its ID3 tag (if it has one). 

*some implementation notes*:  
- .mp3 files with already normal names (eg. not of the form of 4 capitalized characters XYZW) will be ignored  
- when renaming a file, if the parsed *Title* from the ID3 tag contains [reserved characters for Windows filenames](https://docs.microsoft.com/en-us/windows/win32/fileio/naming-a-file), the reserved character will be replaced with a whitespace character  
- I used the https://github.com/dhowden/tag library to parse the ID3 tags and based on the default encoding it uses, non-English characters such as Japanese and Chinese may end up as [mojibake](https://en.wikipedia.org/wiki/Mojibake) in the resulting file name

### Future Improvements

As shown above, my current solution only deals with songs that are tagged (have metadata) so the best way to decode all
the song names is to reverse engineer how iTunes convert them in the first place. After digging some more in the "iPod_Control" folder, I was able to find a ".restore_playlist" file along with a bunch of files with an **.itdb** (itunes database) extension. 

The former turned out to be a **xml** file and it seems to list all the songs in the form of trackPersistentIDs (most likely the primary key/unique id for each song in the database).

![iTunes library index XML file](/img/mp3meta/itunesxml.png "iTunes library index XML file")

Thus, it seems like iTunes uses these unique ids and hash them into different buckets (hence the 14 subfolders *F0* to *F13*) and assign them the corresponding 4 character names. To pursue this path further, I have to refer to Apple's official documentations for dealing with iTunes database files and most likely look into learning **Swift** or **Objective-C** to accomplish this task.

Some relevant links for possible future reference:  
- https://discussions.apple.com/thread/7145301  
- https://discussions.apple.com/thread/5787329  
- https://stackoverflow.com/questions/21058852/retrieve-an-itunes-track-object-from-its-high-low-persistent-id  
- https://stackoverflow.com/questions/34234867/finding-a-song-from-persistent-id-using-swift-2-programming