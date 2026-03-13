# Make Transparent GIFs
Examples of the Transparent GIFs I made using the below workflow are here: 

**Meazure Shortcuts** https://github.com/cthing/meazure-linux/issues/3

### Workflow for Transparent GIF
- OBS: Record clean image assets MOV ProRes 4444 with Alpha
- GIMP: Clean image assets. Make hard masks.
- Davinci Resolve: Animate and Render GIF with Alpha

### GIF does Transparency, but with Limits
- GIF does Binary Transparency, not Alpha Transparency
- GIF **keeps** pixels with 100% Opacity
- GIF **blacks out** pixels with 1-99% Opacity
- GIF **keeps transparency** for pixels with 0% Opacity

This affects:
- opacity
- drop-shadow
- anti-aliasing
- feather
- soft-edge
- gradient etc.

### Alpha Limits Downside: Artifacts in GIF Transparency
Be careful with your image 'edge' where Opacity meets Transparency. Artifacts can be minimized in ways such as:
- Remove anti-aliasing on edges. Eg I used GIMP to delete anti-alias/drop-shadow pixels of the mouse cursor.
- Mask using pure black and white only. No grey. Eg Davinci Resolve masks don't work because they have soft-edges. I used GIMP to make masks with zero feather.
- Resizing images or video: Use settings for the hardest/sharpest edges.
- Use OBS Filters like Chroma/Colour/Luma Keys with Smoothness/Opacity sliders to create hard/sharp masks

> [!NOTE]
> Scaling/translating image assets during workflow may cause artifacts in the final GIF. Try to go 1\:1\:1 from OBS to GIMP to Davinci Resolve. This affects: image scaling, input scaling, output scaling, zoom, animation, canvas size etc


## OBS Setup for MOV with Alpha
### Backup OBS
> [!CAUTION]
> NOTE WELL: I did NOT backup OBS before this project. I wasn't on my main streaming PC and had nothing to lose. In your case, act accordingly.

**OBS Knowledge Base: Profiles** https://obsproject.com/kb/profiles

**How to move OBS between PCs (including your FILES!)** https://www.youtube.com/watch?v=y8FM_5tzgH0

### General Settings
Make dedicated GIF Profile/Scene. Don't mess up your standard profiles or scenes
> OBS Menu > Profile > New
> OBS Scenes > Add

### OBS Settings > Video
|				|					|
|:------------------------------|:--------------------------------------|
| Base/Output Resolution	| Set resolution to final GIF size	|
| FPS 				| Set FPS to final GIF frame rate	|

### OBS Settings > Advanced
|		|		|
|:--------------|:--------------|
| Colour Format | BGRA (8-bit)	| 
| Colour Space 	| sRGB   	| 
| Colour Range	| Limited	|

### OBS Settings > Output > Output Mode > Advanced > Recording
|			|				|
|:----------------------|:------------------------------|
| Type 			| Custom Output (FFmpeg)	|
| Container Format	| mov				|
| Video Encoder 	| prores - Apple ProRes		|

## GIMP: Clean Image Assets. Make Hard Masks
Skip the GIMP. Get to rendering in Davinci Resolve asap. If your GIF is perfect, you're laughing. If not work back from there. 

In terms of GIF and GIMP, clean means Alpha Channel pixels are 0% or 100% as in above GIF limitations. Find and delete artifact causing pixels in the alpha channel with 'By Color Select'
>Tools > Selection Tools > By Color Select

For masks keep hard edges between black and white. A mask with any grey will cause artifacts in the GIF. Eg in Rectangle Select turn off 'Antialiasing' and turn off 'Feather edges'

## Davinci Resolve Setup: Animate and Render GIF with Alpha
Make New Project, you know the drill

### File > Project Settings > Master Settings
|			|				|										|
|:----------------------|:------------------------------|:------------------------------------------------------------------------------|
| Timeline Format	| Timeline Resolution		| Set Resolution to final GIF size. *Smaller than 256x256: see note below*[^1]	|

### File > Project Settings > Image Scaling
|			|				|				|
|:----------------------|:------------------------------|:------------------------------|
| Image Scaling		| Resize filter			| Bilinear			|
| Image Scaling		| Deinterlace quality		| High				|
| Input Scaling		| Mismatched resolution files	| Center crop with no resizing	|
| Output Scaling	| Match timeline settings	| 				|
| Output Scaling	| Mismatched resolution files	| Center crop with no resizing	|

### Workspace > Switch to Page > Edit > Inspector > Retime and Scaling
Like Project Settings but clip by clip. If image looks wrong test here for optimal. Implement optimal to all clips in:
> File > Project Settings > Image Scaling

### Workspace > Switch to Page > Deliver > Video
|		|			|
|:--------------|:----------------------|
| Format	| GIF			|
| Codec		| Animated GIF		|
| Resolution	| Timeline Resolution	|
| Export Alpha	| True			|

### To make a Render Preset
> Workspace > Switch to Page > Deliver > Render Settings > Three Dot Menu > Create Additional Video Output

## Pros and Cons
### GIF
- Tiny file size
- Infinite loop
- No audio
- Every frame eats CPU and GPU
- Binary Transparency. 0% or 100%. Difficult to control artifacts

### VP9
- Medium file size
- GIF-like artifacts

### MOV ProRes 4444
- Large file size
- Alpha channel 0-100% = All the alpha effects and Zero Artifacts!

## Notes
- Figure out the workflow before getting into the details. Get test renders out of Resolve asap. Every render will improve
- This project gave me a lot of clues about Transparent Video and helped me understand GIF artifacts. I feel a lot more confident to quickly make MOV/GIF overlays in the future. I successfully made transparent MOVs and GIFs in the past, but it was very confusing. I didn't realize the Alpha limits of GIF or the pros and cons of MOV v VP9 v GIF.
- **Recording Transparent Video with VP9** https://obsproject.com/forum/threads/recording-transparent-video-with-vp9.183938/
- Other OBS/Davinci Resolve settings may be relevant. Please let me know if you have improvements, comments or hiccups.
- This document is to remember all the above for next time.

[^1]: Workaround for Davinci Resolve minimum Timeline Resolution of 256x256.  
OBS Settings > Video > Base (Canvas) Resolution > 256x256  
OBS Settings > Video > Output (Scaled) Resolution > 256x256  
OBS Add Image Source of "Final GIF size safe area.png"  
Davinci Resolve > File > Project Settings... > Master Settings > Timeline Resolution > Custom > 256x256  
Davinci Resolve > Workspace > Switch to Page > Deliver > Video > Resolution > Timeline Resolution  
OBS and Davinci Resolve guide or safe-area that represents your final GIF size eg OBS red rectange PNG, Davinci Resolve Viewer Guides  
GIMP File > New... > Final GIF size  
GIMP File > Open as Layers... > 256x256 GIF from Davinci Resolve  
GIMP Image > Crop to Content  
GIMP File > Export... > Export Image Dialog > Name: > "*.gif" > Export  
GIMP Export Image as GIF Dialog > As Animation: True > Delay between frames where unspecified: "1000/framerate" > Export  
