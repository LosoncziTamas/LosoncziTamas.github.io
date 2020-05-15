---
layout:     post
title:      Ocean shader
date:       2020-05-15
author:     Tamás Losonczi
summary:    Ocean shader example 
categories: Unity3D
tags:
 - unity
 - shader
 - shaderlab
---

A shader written in _ShaderLab_ that is trying to replicate ocean waves. This solution uses sin function to modify the vertices and the normals. Also, two textures are used and they are being moved at different rates.

```
Shader "Custom/Ocean"
{
    Properties
    {
        _WaterTex("Water Texture", 2D) = "white" {}
        _FoamTex("Foam Texture", 2D) = "white" {}
        _Tint("Tint Color", Color) = (0, 0, 0, 0)
        _Amplitude("Amplitude", Range(0, 5)) = 1
        _Frequency("Frequency", Range(0, 5)) = 1
        _Speed("Speed", Range(0, 10)) = 1 
        _WaveColorStrength("Wave Color Strength", Range(0, 5)) = 2
        _ScrollX("Scroll X Rate", Range(0, 2)) = 1
        _ScrollY("Scroll Y Rate", Range(0, 2)) = 1
    }
    SubShader
    {
        CGPROGRAM
            #pragma surface surf Lambert vertex:vert
            
            #include "UnityCG.cginc"
            
            struct appdata
            {
                float4 vertex: POSITION;
                float3 normal : NORMAL;
                // since we are using custom vertex shader we'll need to make 
                // the appdata compatible with the internal vertex shader
                float4 texcoord : TEXCOORD0;
                float4 texcoord1 : TEXCOORD1;
                float4 texcoord2 : TEXCOORD2;
            };
            
            struct Input
            {
                float2 uv_WaterTex;
                float2 uv_FoamTex;
                float3 color;
            };
            
            sampler2D _WaterTex;
            sampler2D _FoamTex;
            float _Amplitude;
            float _Frequency;
            float _Speed;
            float _WaveColorStrength;
            float _ScrollX;
            float _ScrollY;
            float4 _Tint;
            
            void vert(inout appdata v, out Input o)
            {
                // Initializes o to zero.
                UNITY_INITIALIZE_OUTPUT(Input, o);
                float t = _Time * _Speed;
                float waveHeightX = sin(t + v.vertex.x * _Frequency) * _Amplitude;
                float waveHeightZ = sin(t + v.vertex.z * _Frequency) * _Amplitude;
                // Alter the height of the mesh with waveHeight.
                v.vertex.y = v.vertex.y + waveHeightX + waveHeightZ; 
                // Recalculate normals so the shadows are properly casted.
                v.normal = normalize(float3(v.normal.x + waveHeightX, v.normal.y, v.normal.z + waveHeightZ));
                // Color value for lightening.
                o.color = waveHeightX + waveHeightZ +_WaveColorStrength;
            }
            
            void surf(Input IN, inout SurfaceOutput o)
            {
                float rate = _Time * _Speed;
                float2 uvOffset = float2(_ScrollX, _ScrollY) * rate;
                fixed4 water = tex2D(_WaterTex, IN.uv_WaterTex + uvOffset);
                // Use different rates for the two textures.
                fixed4 foam = tex2D(_FoamTex, IN.uv_FoamTex + uvOffset * 0.5f);
                // Mix the textures with the colors.
                o.Albedo = (water + foam).rgb * 0.5f * IN.color * _Tint; 
            }
            
        ENDCG
    }
}
```

| ![ocean_shader.gif](../../../../../images/ocean-shader/ocean_shader.gif) | 
|:--:| 
| *The shader in action.* |

##### Full project with source available [here](https://github.com/LosoncziTamas/UnityShaders/tree/master/Assets/Ocean). 