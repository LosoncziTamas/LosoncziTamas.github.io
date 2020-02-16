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

__Scenario 1:__ Move an object around `target` along the target's _up_ axis.

```
    public class MoveAround : MonoBehaviour
    {
        public GameObject target;
        
        public float distance;
        public float speed;

        private void Start()
        {
            Debug.Assert(target, "Target is not set in the inspector.");
            Debug.Assert(distance > 0.0f, "Incorrect distance value.");
            Debug.Assert(speed > 0.0f, "Incorrect speed value.");
        }

        private void Update()
        {
            var targetTrans = target.transform;
            // Calculating the offset.
            var x = Mathf.Sin(Time.time * speed) * distance;
            var z = Mathf.Cos(Time.time * speed) * distance;
            // Transforming from the target's local to the world space.
            // So it's orientation is taken into account.
            transform.position = targetTrans.TransformVector(
                targetTrans.position + new Vector3(x, 0.0f, z)
            );
        }
    }
```

__Scenario 2:__ Moving an object linearly from `start` to `end` over `targetTimeInSeconds`.

```
    public class MoveFromStartToEnd : MonoBehaviour
    {
        public Transform start;
        public Transform end;
        
        public float targetTimeInSeconds;
        
        private float _timeElapsed;

        private void Start()
        {
            Debug.Assert(targetTimeInSeconds > 0.0f, "Target time is not positive.");
            _timeElapsed = 0.0f;
        }

        private void Update()
        {
            if (_timeElapsed >= targetTimeInSeconds)
            {
                _timeElapsed = 0.0f;
            }
            _timeElapsed += Time.deltaTime;
            transform.transform.position = Vector3.Lerp(
                start.position, 
                end.position, 
                _timeElapsed / targetTimeInSeconds
            );
        }
    }

```

__Scenario 3:__ Make `objectA` face toward `direction`

```

```

__Scenario 4:__ Determining whether `objectA` and `objectB` are facing the same direction

```

```




---