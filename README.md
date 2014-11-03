-------------------------------------------------------------------------------
CIS565: Project 5: WebGL
-------------------------------------------------------------------------------
-------------------------------------------------------------------------------
INTRODUCTION:
-------------------------------------------------------------------------------

There're two parts in this project. The first part is the Image Processor, and the second part is a Wave Vertex Shader.

In the first part of this project, I implemented a GLSL vertex shader to create a dynamic wave animation using code that runs entirely on the GPU.

In the second part of this project, I implemented a GLSL fragment shader to render an interactive globe in WebGL, including texture blending, bump mapping, specular masking, and a moving cloud layer.

-------------------------------------------------------------------------------
Demo:
-------------------------------------------------------------------------------
[![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/video%20cover.JPG)](http://youtu.be/DvqLw0I0swU)

-------------------------------------------------------------------------------
PART 1
-------------------------------------------------------------------------------
**Demo**

[![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part1/video%20cover.JPG)](http://youtu.be/xA6fc0ufyLs)

**Features**

The base code shows an example of how to gamma correct the nighttime texture:

```glsl
float gammaCorrect = 1/1.2;
vec4 nightColor = pow(texture2D(u_Night, v_Texcoord), vec4(gammaCorrect));
```

Fee

* Sin wave
```glsl
	float s_contrib = sin(position.x*2.0*3.14159 + u_time);
	float t_contrib = cos(position.y*2.0*3.14159 + u_time);
	float height = s_contrib*t_contrib;
	```
	
![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part1/sin.JPG)

* Sin wave 2
```glsl
    float dist = distance(position, vec2(0.5,0.5));
	float height = sin(u_time - dist) / (u_time - dist);
	```
	
![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part1/sin2.JPG)

* Simple Butterfly
```glsl
    float t_contrib = cos( (0.5-position.y) * u_time);
    float height = t_contrib * 0.5 * (1.0 + abs(0.5-position.y)/0.5 );
	```
	
![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part1/bt.JPG)

* Complex Butterfly
I use a butterfly function from Wolfram, http://mathworld.wolfram.com/ButterflyFunction.html
```glsl
    float tmp = (pow(position.x,2.0) - pow(position.y,2.0)) / (pow(position.x,2.0) + pow(position.y,2.0));
    float height = tmp * sin((position.x + position.y) / u_time);
	```
	
![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part1/bt2.JPG)

* user interface
User can user color picker to change the color of the grid and also the type of dynamic wave animation.

-------------------------------------------------------------------------------
PART 2 Features
-------------------------------------------------------------------------------
http://liying3.github.io/Project5-WebGL/

Implemented the following basic features:
* Bump mapped terrain
* Rim lighting to simulate atmosphere
* Night-time lights on the dark side of the globe
* Specular mapping
* Moving clouds

Here are some rendered images:

![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/bump.JPG)

Bump mapped terrain

![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/night.JPG)

Night-time lights

![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/cloud.JPG)

Rim lighting and cloud layer

Extra Feature:
* Procedural water rendering and animation using perlin noise 

Only the ocean should be animated here. So I use `u_EarthSpec` to determine if a fragment is on ocean or land. If it is in ocean, I use the perlin noise the get an offset position of it. And check the new position whether it is also in the ocean by 'u_EarthSpec'. If it is, I will replace the origianl texture with the offset one.

However, all the determination is based on the 'u_EarthSpec', which it is not toally seperate land and ocean space. So there's some artifacts in the result image.

![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/ocean.JPG)

* user interface

![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/gui.JPG)

Users can check or unckeck certain effect, like cloud and ocean moving to get better view of other parts of the global. Also, there's FPS and Timing efficiency on the right of the screen to show current computation.

-------------------------------------------------------------------------------
Performance
-------------------------------------------------------------------------------
From the following chart, we can tell that, it costs most time for shader initialization, and then is the sphere initialization.

As for animation each frame, it has average 48 FPS.

![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/fps.JPG)
![ScreenShot](https://github.com/liying3/Project5-WebGL/blob/master/result/part2/timing.JPG)

-------------------------------------------------------------------------------
THIRD PARTY CODE POLICY
-------------------------------------------------------------------------------
* stas.js 

https://github.com/mrdoob/stats.js/

It's a library to visualize realize fps and timing.

* perlin noise 

https://github.com/ashima/webgl-noise/blob/master/src/classicnoise3D.glsl

I use the perlin noise to get an offset position when implementing water animation.
```glsl
    float noise = perlinNoise(v_Position+u_time);
	```
	