---
layout:     post
title:      "Unity interview questions: physics"
date:       2020-03-19
author:     Tamás Losonczi
summary:    Common questions that may pop-up during a technical interview.
categories: Unity3D
tags:
 - unity
 - interview question
 - physics
---

__Q__: What are the main differences between `Update` and `FixedUpdate`. In which scenario would you prefer using one over the other?  
__A__: - 

__Q__: What is the purpose of the `Rigidbody.WakeUp` method?   
__A__: -

__Q__: What are the differences between the `TransformDirection(Vector3 direction)`, `TransformPoint(Vector3 position)` and `TransformVector(Vector3 vector)` methods of the `Transform` class? Could you give use cases for them?  
__A__: `TransformDirection(Vector3 direction)` transforms a direction from local to world space. It only cares about the direction, the returned vector has the same length as the provided direction vector. This is useful when you want to rotate an object to face the same direction as another one. `Quaternion.LookDirection(playerTransform.TransformDirection(Vector3.forward))`
`TransformPoint(Vector3 position)` transforms a point from local to world space. The result is affected by scale, position and rotation (since the world space position will change if anyone of the player's position, rotation, or scale changes). You may want to use it if you want to position something relative to another transform. `var newPos = transform.TransformPoint(Vector3.right * 2);` 
`TransformVector(Vector3 vector)` transforms a vector from local to world space. The result is affected by scale and rotation. You may want to use this when you are dealing with vector quantities such as velocities and acceleration. 

__Q__: Which is the least processor-intensive primitive collider?   
__A__: Sphere collider

__Q__: What is the bare minimum requirement in terms of attached unity components and settings to initiate an `OnTriggerEnter()` callback?   
__A__: An active game object with an enabled Collider where isTrigger set, and another active game object with a (kinematic or non-kinematic) Rigidbody component.

__Q__: What is the bare minimum requirement in terms of attached unity components and settings to initiate an `OnCollisionEnter()` callback?   
__A__: An active game object with a non-kinematic Rigidbody and an enabled Collider component, and another active game object with an enabled Collider component.

__Q__: Is `OnTriggerEnter()`  gets invoked on a game object which is not a trigger?   
__A__: Yes, `OnTriggerEnter()` is called on all objects who participate in the collision (trigger or not).