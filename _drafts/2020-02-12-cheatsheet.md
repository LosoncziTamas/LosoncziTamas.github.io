---
layout:     post
title:      Cheatsheet for common 3D operations
date:       2020-02-12
author:     Tamás Losonczi
summary:    Cheatsheet for common operations you may want to implement in any 3D application written in Unity.
categories: Unity3D
tags:
 - unity
 - 3d
 - cheatsheet
---

__Scenario 1:__ Move an object around `gameObjectB` along the Y axis.

```
public class MoveAround : MonoBehaviour
{
    public GameObject gameObjectB;
    
    public float distance;

    private void Start()
    {
        Debug.Assert(distance > 0.0f, "Incorrect distance value.");
    }

    private void Update()
    {
        var bPos = gameObjectB.transform.position;
        var x = bPos.x + Mathf.Sin(Time.time) * distance;
        var y = bPos.y;
        var z = bPos.z + Mathf.Cos(Time.time) * distance;
        transform.position = new Vector3(x, y, z);
    }
}
```

__Scenario 2:__ Rotate `objectA` around itself

```

```

__Scenario 3:__ Make `objectA` face toward `direction`

```

```

__Scenario 4:__ Determining whether `objectA` and `objectB` are facing the same direction

```

```




---