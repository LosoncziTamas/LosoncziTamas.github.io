---
layout:     post
title:      Common programming mistakes in Unity (part 1)
date:       2020-04-11
author:     Tamás Losonczi
summary:    Common programming mistakes encountered when developing with Unity
categories: Unity3D
tags:
 - unity
 - common mistakes
---

__Using a foreach loop to unparent child transforms__
```
foreach (Transform child in _content.transform)
{             
    _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
}
```
Here `_pool.ReturnItem()` internally invokes a `SetParent` to change the parent transform. On each iteration, the value of the parent `childCount` gets decremented while the index (used by the transforms `Enumerator`) gets incremented. Causing to unparent only half of the original child count.   
A correct version could be the following:
```
while (_content.childCount > 0)
{
    var child = _content.GetChild(0);
    _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
}
```
__Using a while loop to destroy child transforms__
```
while (transform.childCount > 0)
{
    Destroy(transform.GetChild(0).gameObject);
}
```
Since destruction of game objects happens after the update loop has finished, the previous code runs infinitely. Using a foreach loop is a proper alternative.

```
foreach (Transform child in transform)
{
    Destroy(child.gameObject);
}
```
__Failing to unsubscribe from an event__   
Mismanaged event subscription can cause leaks and `MissingReferenceException` in your app. Until you unsubscribe from an event, the delegate underlying the event in the publishing object has a reference to the subscriber's event handler. Hence preventing the GC to delete the subscriber object. 

Let's consider a case where you use a static event handler that is triggered when something happens that affects the app globally (ex.: losing a life). Here is the publisher code:
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

    // The rest is omitted for clarity
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
When `LifeCounterUI` is no longer active or even the corresponding game object is destroyed (because of a scene load for example), the `AppStateChangeEvent` event handler still stores a reference to it and will keep notifying it. A correct way would be to subscribe/unsubscribe in the appropriate event function (ex.: `OnEnable()`/`OnDisable()` or `Awake()`/`OnDestroy()`).

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
__Treating structs as reference types__   
In C# besides classes you can use structs to encapsulate data. However, structs are value types, which means that when you are assigning it to a variable, or passing it around, you are using a copy not a reference. For example the following doesn't change the position:
```
var currPos = transform.position;
transform.position.Set(currPos.x + _direction.x * _speed, currPos.y + _direction.y * _speed, currPos.z);
```
`Vector3` is a struct, so you when you call `transform.position.Set()` you are setting the values for a copy. You'll either have to use the `Translate` method on the `Transform` or assign a vector to `transform.position`, like this:
```
var currPos = transform.position;
transform.position = new Vector3(currPos.x + _direction.x * _speed, currPos.y + _direction.y * _speed, currPos.z);
```