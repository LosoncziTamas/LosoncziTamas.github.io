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

__Can you spot the cause?__
```
    foreach (Transform child in _content.transform)
    {             
        _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
    }
```

The cause of the problem is the `_pool.ReturnItem()` call, which unparents the child object internally.

__Corrected version__
```
    while (_content.childCount > 0)
    {
        var child = _content.GetChild(0);
        _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
    }
```

### Using a while loop to destroy children objects

Since destroying happens after the update loop has finished, the following code will result in an infinite loop.

```
    while (transform.childCount > 0)
    {
        Destroy(transform.GetChild(0).gameObject);
    }
```

In this case the foreach loop is a proper alternative.

```
    foreach (Transform child in transform)
    {
        Destroy(child.gameObject);
    }
```

### Modifying the game object's position direcly

You cannot modify the position of the GameObject directly (by calling transform.position), you'll either have to use the Translate method on the Transform or assign/set a new vector to transform.position.

__This doesn't work__
```
    var currPos = transform.position;
    transform.position.Set(currPos.x + _direction.x * _speed, currPos.y + _direction.y * _speed, currPos.z);
```

`Vector3` is a C# struct which means when you are assigning an instance of it to a variable, or pass it around, you are using a copy not a reference.

__This does__
```
    var currPos = transform.position;
    transform.position = new Vector3(currPos.x + _direction.x * _speed, currPos.y + _direction.y * _speed, currPos.z)–
```


