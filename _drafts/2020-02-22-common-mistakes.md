---
layout:     post
title:      Common programming mistakes in Unity 
date:       2020-02-22
author:     Tamás Losonczi
categories: Unity3D
tags:
 - unity
 - ui
---

### Using a for loop to unparent child game objects

#### Can you guess where the problem is?
```
    foreach (Transform child in _content.transform)
    {             
        // ReturnItem unparents the child 
        _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
    }
```

However, calling GameObject.Destroy(child) would be fine in a for loop, cause the destroying happens after the update loop has finished.

#### Corrected version
```
    while (_content.childCount > 0)
    {
        var child = _content.GetChild(0);
        _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
    }
```



