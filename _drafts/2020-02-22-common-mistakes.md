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

### Initializing states at inproper events functions.


### Destroying a script component instead of the game object.


### Querying components on inactive game objects with GetComponentInChildern.


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
    transform.position = new Vector3(currPos.x + _direction.x * _speed, currPos.y + _direction.y * _speed, currPos.z);
```

### Failing to unsubscribe from an event

This could easily cause leaks and `MissingReferenceException` in your app. Until you unsubscribe from an event, the delegate underlying the event in the publishing object has a reference to the subscriber's event handler. Hence preventing the GC to delete the subscriber object. 

Let's consider a case where you use a static event handler that is triggered when something happens that affects the app globally (ex.: losing life). Here is the publisher code:
```
    public class AppController : MonoBehaviour
    {
        public static event EventHandler<AppState> AppStateChangeEvent;

        public class AppState : EventArgs
        {
            public readonly int LifeCount;

            public AppState(int lifeCount)
            {
                LifeCount = lifeCount;
            }
        }

        private int _lifeCount;

        private void PublishAppStateChangeEvent()
        {
            AppStateChangeEvent?.Invoke(this, new AppState(_lifeCount));
        }

        // The rest are omitted for clarity
    }
```
And here is the code for a subscriber:
```
    public class LifeCounterUI : MonoBehaviour
    {
        private void Start()
        {
            AppController.AppStateChangeEvent += OnAppStateChanged;
        }

        private void OnAppStateChanged(object sender, AppController.AppState args)
        {
            // Do something with args
        }
    }
```
When `LifeCounterUI` is no longer active or even the corresponding game object is destroyed (because of a scene load for example), the `AppStateChangeEvent` event handler still stores a reference to it and will keep notifying it. A correct way would be to subscribe/unsubscribe in the appropriate event function (ex.: OnEnable()/OnDisable() or Awake()/OnDestroy()).

```
    public class LifeCounterUI : MonoBehaviour
    {
        private void OnEnable()
        {
            AppController.AppStateChangeEvent += OnAppStateChanged;
        }

        private void OnDisable()
        {
            AppController.AppStateChangeEvent -= OnAppStateChanged;
        }

        private void OnAppStateChanged(object sender, AppController.AppState args)
        {
            // Do something with args
        }
    }
```
