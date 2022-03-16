# Godot 4 SDFGI vs Unreal 5 Lumen Comparison

I have attempted to set up the same scene in Godot 4 and Unreal 5 in order to compare their two global illumination systems: SDFGI and Lumen. Lumen is running the non-raytraced version, as Godot's SDFGI does not yet support ray tracing.

* Both renders are tonemapped with ACES. How close the curves between the two engines is unknown, they were matched visually through exposure setting adjustments.
* This scene in comparison has a single directional light pointing downwards at a slight angle. 
* The indirect lighting intensity/energy for this directional light is increased 7x the direct lighting intensity.

What I'll ask you to ignore in this comparison, as it's irrelevant:
* **Texture/Normal Map Detail**: Yes, the Unreal screenshot's textures are missing a lot of detail when viewed from a distance. This is likely due to a difference in anisotropic filtering implementation. While Godot looks more detailed from a screenshot comparison, it also has more aliasing noise when moving around in game.
* **Anti-Aliasing/Smoothing**: Both engines are using FXAA, as that's the only algorithm they have in common for these settings. I would use MSAA, however Unreal does not support it with their deferred rendering pipeline.
* **Specular Highlights**: Unreal handles direct lighting differently from Godot; it's hard for me to quantify.

> ## Screenshot 1

Godot 4
![Godot Screenshot 1](./godot1.png)

Unreal 5
![Unreal Screenshot 1](./unreal1.png)

### Shadows

Godot's SDFGI appears to retain more shadows in small detailed areas such as the under-side of these ledges. Unreal is creating shadows in these areas, but Lumen is brightening them almost completely in this scenario.

[Left: Godot 4, Right: Unreal Engine 5]

![Godot Left Unreal Right](./comparison1-shadows.png)

Lumen has a tendency to create dark blotches in areas with complex geometry such as the center of all of these leaves. You can find examples of this all over the image. Godot's result looks more natural in this case, as the lighting across the area is more even.

[Left: Godot 4, Right: Unreal Engine 5]

![Godot Left Unreal Right](./comparison1-occlusion-in-complex-geometry.png)

The cast shadow from this stairway wall in Unreal is notably darker than the Godot counterpart. How accurate either is to photorealism is difficult to say. 

[Left: Godot 4, Right: Unreal Engine 5]

![Godot Left Unreal Right](./comparison1-shadow-area-contrast.png)

[THIS DOCUMENT IS A WORK IN PROGRESS]