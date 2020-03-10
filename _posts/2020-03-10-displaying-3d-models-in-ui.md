---
layout:     post
title:      Displaying 3D models as part of the UI in Unity
date:       2020-03-10
author:     Tamás Losonczi
summary:    Steps for displaying 3D models in the UI.
categories: Unity3D
tags:
 - unity
 - ui
---

1. Create a canvas using the default settings (Screen Space - Overlay). 
2. Add a camera to the hierarchy which is going to look at the 3D models.
   * Make sure to set-up the clipping panes properly, so the models are visible and nothing else obscures the view.
3. Position the 3D models/camera/lights to have the desired look & feel.
4. Create a Render Texture and set the camera's Target Texture to it.
5. Add a RawImage to the Canvas and set the RawImage's Texture to the Render Texture.

| ![brick_selector.gif](../../../../../images/displaying-3d-models-in-ui/brick_selector.gif) | 
|:--:| 
| *An info panel featuring 3D objects.* |