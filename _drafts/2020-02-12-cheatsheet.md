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

__Scenario 1:__ Move `objectA` around `objectB`

```
    public class MoveAround : MonoBehaviour
    {
        public GameObject gameObjectB;
        
        private Vector3 _offset;

        private void Start()
        {
            // Offset between two objects
            _offset = gameObjectB.transform.position - transform.position;
        }

        private void Update()
        {
            var translation = new Vector3();
            transform.Translate(translation);
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