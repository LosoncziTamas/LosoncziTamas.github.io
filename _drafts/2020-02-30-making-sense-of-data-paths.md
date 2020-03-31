---
layout:     post
title:      Making sense of file paths
date:       2020-03-30
author:     Tamás Losonczi
summary:    Which file path to use under what circumstance.
categories: Unity3D
tags:
 - unity
 - data path
 - file
---

There are several data paths available in Unity and it may not be obvious which one should be used under what circumstance. Here is a summary of them with some use cases.

__Application.dataPath__ - Returns the path to the game data folder on the target platform. For example, in the editor it returns the path to the _Assets_ folder and on Android it points to the APK. It can be useful when you are developing custom editor script. On the other hand, I would not rely on it for general purposes such as loading/storing game play data.

__Application.persistentDataPath__ - Returns a path where you can store runtime data (ex.: configs, session, cache). Files stored at `persistentDataPath` are not erased by app updates, however they are public, and the user is free to delete them. 

__Application.temporaryCachePath__ - Similarly to `persistentDataPath`, `temporaryCachePath` is intended to be used for storing runtime temporary data. Due it's naming and the lack of documentation, I would only use it for storing recoverable/non-critical data such as logs/image caches.

__Application.streamingAssetsPath__ - Streaming assets are files located at the `StreamingAssets` folder of the project. These files can be loaded at runtime via  `streamingAssetsPath`. It should be used for retrieveing data (hence the name "streaming") and not for saving them. Additionally, on WEBGL and Android it's not a file path but an URI, so on these platforms you'll need to implement a custom method to resolve these files. For example, on Android it's bundled in the APK, which means first you'll need to decompress it and then load it to the memory or save the decompressed files.

---

##### Related links: 
##### https://stackoverflow.com/a/43695023
##### https://answers.unity.com/questions/1151771/does-unity-pack-all-files-from-the-project-in-the.html
##### https://github.com/kimsama/Unity-Paths
##### http://answers.unity.com/answers/1181681/view.html