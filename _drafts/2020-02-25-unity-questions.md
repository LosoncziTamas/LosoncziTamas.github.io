---
layout:     post
title:      Unity interview questions and answers
date:       2020-02-25
author:     Tamás Losonczi
summary:    Common questions that may pop-up during a technical interview.
categories: Unity3D
tags:
 - unity
 - interview question
---

### Input
------
__Q__: What is the difference between `Input.GetAxis(string axisName)` and `Input.GetAxisRaw(string axisName)`?  
__A__: `Input.GetAxis(string axisName)` returns a smoothed value, which means the value will be in a range of [-1, 1] (except when the axis is setup to be delta mouse movement). On the other hand, `Input.GetAxisRaw(string axisName)` returns a "raw" value which is either -1, 0 or 1.


__Q__: What change should I make in a project to make the following code run without errors: `Input.GetAxis("foo")`?  
__A__: Add a new axis named "foo" under Project Settings/Input.

__Q__: Can you affect the smoothing of the value returned by `Input.GetAxis(string axisName)`?  
__A__: Yes, smoothing depends on the axis parameters set in the Input settings.

### Interaction
---

__Q__: What are the required steps to make a user gesture (ex.: touch) interact with an object in the virtual world? For example, selecting an object in a 3D scene by screen tapping.  
__A__: First, you'll need to detect the position of the tap in screen space: `GetTouch(int index)`. Then you'll need to determine whether the tap overlaps the object in the scene. In 3D this is done by performing raycasting from the camera:
```
    var ray = Camera.main.ScreenPointToRay(new Vector3(screenPosition.x, screenPosition.y, 0f));
    if (Physics.Raycast(ray, out var hit))
    {
        // Hit found
    }
```
You can get a reference to the transform of the object via the `RaycastHit` structure which then can be used to indicate the selection (ex.: scaling up).

### Rendering
---

__Q__: What are the 4 coordinate systems in Unity? What are the differences between them?  
__A__: Screen coordinates, GUI coordinates, Viewport coordinates and World coordinates.

__Q__: How do you convert from one to the other?  
__A__: 

__Q__: What is the relationship between a Mesh Filter and Mesh Renderer?  
__A__: A Mesh Filter holds information about the mesh instance. A Mesh Renderer does the actual rendering by using a Mesh Filter. 

### Coding best pratices & tools

---



### Physics
---

__Q__: What are the main differences between `Update` and `FixedUpdate`. In which scenario would you prefer using one over the other?  
__A__: 

__Q__: What are the differences between the `TransformDirection(Vector3 direction)`, `TransformPoint(Vector3 position)` and `TransformVector(Vector3 vector)` methods of the `Transform` class? Could you give use cases for them?  
__A__: `TransformDirection(Vector3 direction)` transforms a direction from local to world space. It only cares about the direction, the returned vector has the same length as the provided direction vector. This is useful when you want to rotate an object to face the same direction as another one. `Quaternion.LookDirection(playerTransform.TransformDirection(Vector3.forward))`
`TransformPoint(Vector3 position)` transforms a point from local to world space. The result is affected by scale, position and rotation (since the world space position will change if any one of the player's position, rotation, or scale changes). You may want to use it if you want to position something relative to other transform. `var newPos = transform.TransformPoint(Vector3.right * 2);` 
`TransformVector(Vector3 vector)` transforms a vector from local to world space. The result is affected by scale and rotation. You may want to use this when you are dealing with vector quantities such as velocities and acceleration. 

---
##### Resources
###### https://www.reddit.com/r/Unity3D/comments/2b4k88/what_exactly_is_world_space_and_screen_space/
###### http://answers.unity.com/answers/781966/view.html
###### http://codesaying.com/understanding-screen-point-world-point-and-viewport-point-in-unity3d/
###### https://forum.unity.com/threads/gui-coordinates.67063/
###### https://www.quora.com/What-are-some-must-know-Unity-methods-for-a-beginner