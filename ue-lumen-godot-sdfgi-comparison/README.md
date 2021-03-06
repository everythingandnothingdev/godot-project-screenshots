# Godot 4 SDFGI vs Unreal 5 Lumen Comparison

I have attempted to set up the same scene in Godot 4 and Unreal 5 in order to compare their two global illumination systems: SDFGI and Lumen. Lumen is running the non-raytraced version, as Godot's SDFGI does not yet support ray tracing.

This scene in comparison below has a single directional light pointing downwards at a slight angle. The indirect lighting intensity/energy for the directional light is increased 7x the direct lighting intensity, which significantly brightens the areas in shadow.

## Engine Settings

This table covers the settings in each engine, specifically what was changed from the defaults.

| Category | Godot 4 | Unreal Engine 5 |
| --: | :-- | :-- |
| Resolution: | 1920x1080 | 1920x1080 |
| Renderer: | Clustered-Forward. | Deferred. (Has a Forward Renderer, but it did not work with dynamic shadows in testing. Also took forever to compile shaders. Seems to not be in the Unreal team's primary maintenance interests.) |
| Tonemapper: | ACES. White Reference: 16. | ACES. Film Slope: 0.8. Saturation: 1.05. |
| Exposure: | Default. Auto-Exposure: Off | Exposure Compensation: 1.8 ([matches Godot's default bias](https://github.com/godotengine/godot/pull/52476/files#diff-069b8591920e7f6caadc40359c68eda1af71f53cbe001e274929093297efde5aR175)). Min Brightness: 1. Max Brightness: 1. |
| Global Illumination: | SDFGI: On. SSIL: Off. | Method: Lumen. Software Ray Tracing Mode: Detail Tracing. |
| Reflections: | Source: Sky (and implicitly SDFGI) | Method: Lumen. |
| Sky: | PanoramaSkyMaterial with .exr texture.grain | Sphere scaled to 20000 units with material using .exr texture, marked "Is Sky". SkyLight: Default. |
| Anisotropic Filtering: | Level: 16x. | Unknown. |
| Texture Streaming: | N/A. | Off. |
| Anti-Aliasing: | FXAA. | FXAA. |
| Screen-Space Ambient Occlusion: | Off. | Intensity: 0. (Does not affect scene when Lumen is enabled, anyways.) |
| Lens Flares: | N/A. | Intensity: 0. |
| Bloom/Glow: | Off. | Intensity: 0. |
| Film Grain: | N/A. | Intensity: 0. |

What I'll ask you to ignore in this comparison, as it's irrelevant:
* **Texture/Normal Map Detail**: Yes, the Unreal screenshot's textures are missing a lot of detail, especially the textures at a distance. This is likely due to a difference in anisotropic filtering implementation. While Godot looks more detailed from a screenshot comparison, it also has more aliasing noise when moving around in game.
* **Anti-Aliasing**: Both engines are using FXAA, as that's the only algorithm they have in common for these settings. I would use MSAA, however Unreal does not support it with their deferred rendering pipeline.
* **Specular Highlights**: Unreal handles direct lighting differently from Godot; it's hard for me to quantify. The top of the red building in the Unreal screenshot being brighter is due to a difference in specular handling.

## **Comparison #1**

Godot 4
![Godot Screenshot 1](./godot1.png)

Unreal 5
![Unreal Screenshot 1](./unreal1.png)

### Shadows

Godot's SDFGI appears to retain darker shadows in small detailed areas such as the under-side of these ledges. Unreal is creating shadows in these areas, but Lumen is brightening them almost completely in this scenario.

[Left: Godot 4, Right: Unreal Engine 5]

![Godot Left Unreal Right](./comparison1-shadows.png)

Lumen has a tendency to create dark blotches in areas with complex geometry such as the center this plant that's climbing the wall. You can find examples of dark blotchy spots all over the Unreal screenshots. Godot's result looks more natural in this case, as the lighting across the area is more even.

[Left: Godot 4, Right: Unreal Engine 5]

![Godot Left Unreal Right](./comparison1-occlusion-in-complex-geometry.png)

The cast shadow from this stairway wall on the ground in Unreal is notably darker than the Godot counterpart. This is due to a difference in how Unreal handles increasing the indirect light intensity vs Godot. This variable barely affects the brightness of the shadow on the ground in Unreal, while dramatically increases the brightness of the wall. Since you're throwing realism out of the window by increasing this variable, it is up in the air as to what is the "correct" response to it. But from an artistic standpoint, my goal in increasing the indirect lighting is to brighten up the shadows overall. Godot's implementation which keeps the lighting even across these shadows gives a more natural, photorealistic look.

[Left: Godot 4, Right: Unreal Engine 5]

![Godot Left Unreal Right](./comparison1-shadow-area-contrast.png)

## **Overall Impressions**

### **Godot 4 SDFGI:**

Pros:
* Seems to keep more natural/even lighting tones across the image.
* Works especially well in corners and small detailed areas.
* Does not create noise.
* In comparison to non-raytraced Lumen specifically, the reflections are more detailed and give a better illusion of real reflections.

Mids:
* While the reflections are fairly detailed for a non-raytraced system, they really need to be used in combination with PBR textures that slightly blur/hide them as they do not look good as a pure mirror.

Cons:
* Transition between cascade resolutions can be visible and distracting when the camera is moving in some lighting scenarios (such as most of the image in shadow).

### **Unreal 5 Lumen:**

Pros:
* The effect looks stable when the camera is in motion. If there is cascade resolution switching, I am not noticing it.

Mids:
* Non-raytraced Lumen reflections are not detailed, but at the same time they are temporally stable and not distracting. Their effect could be desirable or not depending on the game.

Cons:
* Many areas in shadow have noise that flickers and can be very distracting, especially noticable when the camera is not moving.
* It seems to freak out sometimes in areas with highly detailed geometry, especially in corners. Making it too dark or sometimes picking random bright color values that should not be there.

## **Comparison to a Fully Raytraced Image**

No skylight, indirect intensity/energy is 1 (default).

Raytraced
![Godot Screenshot 1](./raytraced6.png)

Godot 4
![Godot Screenshot 1](./godot6.png)

Unreal 5
![Unreal Screenshot 1](./unreal6.png)


## [THIS DOCUMENT IS A WORK IN PROGRESS]

TODO: Light bleeding comparison  
TODO: Reflection comparison  
TODO: Show issue with random colors in corners in Lumen  
TODO: Strong reflected light comparison
