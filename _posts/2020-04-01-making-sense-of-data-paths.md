---
layout:     post
title:      Making sense of data paths in Unity
date:       2020-04-01
author:     Tamás Losonczi
summary:    Which file path to use under what circumstance in Unity.
categories: Unity3D
tags:
 - unity
 - data path
 - file
---

According to the Unity docs, there are several data paths available. It may not be obvious which one should be used under what circumstance. So, here is a summary of them with some use cases.

__Application.dataPath__ - Returns the path to the game data folder on the target platform. For example, in the editor, it returns the path to the _Assets_ folder and on Android, it points to the APK. It can be useful when you are developing a custom editor script. On the other hand, I would not rely on it for general cases such as loading/storing gameplay data.

__Application.persistentDataPath__ - Returns a path where you can store runtime data (ex.: configs, session, cache). Files stored at `persistentDataPath` are not erased by app updates, however, they are public, and the user is free to delete them. It is the standard location for storing caches.

__Application.temporaryCachePath__ - Similarly to `persistentDataPath`, `temporaryCachePath` is intended to be used for storing runtime temporary data. Due to it's naming and the lack of documentation (no info about when they are cleared), I would only use it for storing recoverable/non-critical data such as logs/image caches.

__Application.streamingAssetsPath__ - Streaming assets are files located at the `StreamingAssets` folder of the project. These files can be loaded at runtime by using the `streamingAssetsPath` property. It should be only used for retrieving data (hence the name "streaming") and not for saving them. Additionally, on WEBGL and Android, it's not a file path but a URI, so on these platforms, you'll need to implement a custom functionality to resolve the files. For example, on Android it's bundled in the APK, which means first you'll need to decompress the file and then load it to the memory or write it to the file system for later use.

---

##### Related links: 
##### https://docs.unity3d.com/ScriptReference/Application.html
##### https://stackoverflow.com/a/43695023
##### https://answers.unity.com/questions/1151771/does-unity-pack-all-files-from-the-project-in-the.html
##### https://github.com/kimsama/Unity-Paths
##### http://answers.unity.com/answers/1181681/view.html