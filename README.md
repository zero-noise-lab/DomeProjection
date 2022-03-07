# DomeProjection
Collection of actors and assets that enable a dome projection with an optional user interface on a split-screen in Unreal Engine 4 (UE4)

The method for the dome projection in UE4 was originally developed as a part of a larger project called DomeVR. The provided content allows to render any graphics created in UE4 to be displayed as a dome projection. 

<img src="https://user-images.githubusercontent.com/89643686/156999909-2f752051-adb0-42ed-b5a6-34ff104d7764.png" width=50% height=50%>

The rendered image is distorted such that it can be displayed on a dome screen via a projector that is projecting on a hemispherical mirror.

<img src="https://user-images.githubusercontent.com/89643686/156337394-db0663fe-2118-4e0e-a954-24ddfe37ab63.jpg" width=50% height=50%>

While the dome projection was originally implemented using a streamed level for the rendering pipeline, here it is completely integrated in a Character blueprint. This allows for much easier use in arbitrary UE4 projects. All it takes to activate the dome projection is to use the _DomeBaseCharacter_ (or any derived character class) as the players pawn. The dome projection is completely implemented inside the blueprint code of the _DomeBaseCharacter_ and the referenced render targets and materials. Therefore, it is content only and should at least be usable in all UE4 versions >=4.24. 

The method used for implementing the dome projection and necessary meshes for distorting the images for the projection is based on a similar method introduced by Paul Bourke for Unity: http://paulbourke.net/dome/UnityiDome/

Here the five camera method for generating a fisheye projection with a wider FOV (up to 240 degree) was used: http://paulbourke.net/dome/unity3d/

The final distortion of the fisheye projection is specific to the setup and needs to be calibrated by exchanging the used mesh with a calibrated mesh that can be generated using Paul Bourke's Meshmapper application: http://paulbourke.net/dome/meshmapper/

# How to use

1. Copy the content to the root of your projects content folder. 
2. Open your project. 
3. Use the DomeBaseCharacter (or any derived Character class) as Default Pawn in your GameMode.
4. Run your game

Following input actions already exist in the DomeBaseCharacter and can be set for basic movement capabilites in the DefaultInput.ini as follows

```+AxisMappings=(AxisName="MoveForward",Scale=1.000000,Key=W)
+AxisMappings=(AxisName="MoveForward",Scale=-1.000000,Key=S)
+AxisMappings=(AxisName="MoveRight",Scale=-1.000000,Key=A)
+AxisMappings=(AxisName="MoveRight",Scale=1.000000,Key=D)
+AxisMappings=(AxisName="Turn",Scale=1.000000,Key=Right)
+AxisMappings=(AxisName="Turn",Scale=-1.000000,Key=Left)
```
# User Interface

In addition to the dome projection, this package also offers the possibility to display a user interface (UI) on one screen of a split-screen. To make use of the UI capabilities following steps need to be taken: 
1. Set bLoadWithoutUI Variable in the _DomeBaseCharacter_ to false. 
2. Set the provided _DomeBasePlayerController_ (or any derived class) as the Player Controller Class in the Game Mode
3. Activate Use Splitscreen and set it to vertical in the Project Settings. 
4. Set a class reference to a specific UI widget in the Menu variable of _DomeBaseCharacter_.

Standardly, there is already a UI widget provided, which shows the different rendering steps and allows one to change the camera and graphics settings. 
![2022-03-03_14h14_28](https://user-images.githubusercontent.com/89643686/156571940-05d045d4-a903-4964-9d5a-ccbd12cd22a4.png)

Furthermore, a calibration image can be displayed in different orientations and calibrated meshes for the dome projection can be conveniently exchanged by a drop-down menu under Warp Mesh Settings. The drop-down menu scans all meshes in _Content/DomeProjection/Meshes/MirrorDistortion_ that contain the substring "warp". 

# Rendering Methods

There are two different but similar rendering methods provided. One method called Static Rendering uses premade assets for the render targets and materials and is thus limited to the fixed resolution set in the render target assets. It is activated by setting _bStaticRendering_ in _DomeBaseCharacter_ to true or pressing the Static Rendering button in the provided UI. The other rendering method uses dynamically created render targets and materials, which allows one to set the resolution of the render targets at run-time. It can analogously be activated by setting _bStaticRendering_ to false or pressing the Dynamic Rendering button in the UI where also different resolutions can be tested at run-time. 

# Standard Settings

In addition to the already mentioned settings, other settings for the graphics, inputs and players can be set via different variables in the _DomeBaseCharacter_.

## Graphics Settings

Different settings for the displayed graphics can be defined by the variables in the Graphics Settings category. Here, the used window mode (WindowMode), screen resolution (_ScreenResolution_), V-sync (_VSync_) and frame-rate limit (_FramerateLimit_) can be set. Besides that, standard values for the resolution of the render targets of the already explained dynamic rendering method can be set (_FisheyeResolution_, _CaptureResolution_), and there are options for loading the simulation with or without the UI (_bLoadWithoutUI_) and for switching the side of the screen the UI is displayed on (_bSwitchSplitscreens_).

## Input Settings

There are also settings to modify the impact of the preconfigured input events for controlling the character. The _BaseTurnRate_ sets the speed of rotating the character around its yaw-axis. The variables ending in _*Fraction_ control the scale of the respective axes which enables one to change such settings at run-time and not only statically in the input settings within the Project Settings. The _SkipInputValue_ variable is used in situations in which the used controller is not always perfectly at 0 when idle. If selected, the input values that are smaller than the set value are disregarded.

## Player Settings

The player settings define the dimensions of the player. The _PlayerHeight_ defines the height of the character and the _PlayerRadius_ defines the circumference of the character. The _CameraHeight_ defines basically the height of the eyes of the character but is naturally limited by the _PlayerHeight_. The _CameraRotation_ determines the rotation of the camera on the pitch-axis. 

# Global settings

All of the aforementioned settings can easily be changed by the variables in the _DomeBaseCharacter_. However, changes during run-time are lost when the level is changed and the character is reinitialized. This might be undesirable in some situations. To tackle this problem, there is also an interface provided which can be implemented by a custom GameInstance class. Thus variable values can be saved there globally during run-time and are preserved between level changes. If a Game Instance that implements the _IDomeGameInstance_ interface is set in the GameInstance variable of the DomeBaseCharacter, it pulls the respective settings via the interface functions of the set Game Instance instead of its own variables. 

# Drawbacks

- Occlusion culling can lead to flickering issues at walls and should ideally be turned off.
- In total 7 rendering steps lead to a high demand on GPU resources.
- Higher input lag due to several consecutive rendering steps (Fisheye->Dome->Screen).
