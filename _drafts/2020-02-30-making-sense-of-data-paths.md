---
layout:     post
title:      Making sense of data paths
date:       2020-03-30
author:     Tamás Losonczi
summary:    Summary with legit uses cases for using various data paths.
categories: Unity3D
tags:
 - unity
 - data path
 - file
---


### Making sense of data paths
There are several data paths available in Unity and it may not be obvious which one should be used under what circumstance. Here is a brief summary of them with some use cases.

Application.dataPath - Returns the path to the game data folder on the target platform. For example, in the editor it returns the path to the Assets folder and on Android it points to the APK. It can be useful when you are developing custom editor scripts, or when you want to.... On the other hand, I would not rely on it for general purposes such as loading/storing game play data.
Application.persistentDataPath - Returns a path where you can store runtime data (ex.: configs, session, cache). Files stored at persistentDataPath are not erased by app updates, however they are public, and the user is free to delete them. 
Application.temporaryCachePath - Similarly to persistentDataPath, temporaryCachePath is intended to be used for storing runtime temporary data. Due it's naming and lack of documentation I would only use it for storing recoverable/non-critical data such as logs/image caches. 
Application.streamingAssetsPath - Streaming assets are files put in to the `StreamingAssets` folder of the project. These files can be accessed at runtime via  `streamingAssetsPath`. It should be used for retrieveing data (hence the name "streaming") and not for saving them. Additionally, on WEBGL and Android it's not a file path but a URI, so on these platforms you'll need to use implement a custom method to access them. For example, on Android it's part of the APK, which means first you'll need to decompress it and then save the decompressed file to persistentDataPath.

Related links: 
https://stackoverflow.com/a/43695023
https://answers.unity.com/questions/1151771/does-unity-pack-all-files-from-the-project-in-the.html
https://github.com/kimsama/Unity-Paths
http://answers.unity.com/answers/1181681/view.html