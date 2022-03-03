# DomeProjection
Providing a Character and helper blueprints that enable a dome projection with an optional user interface on a split-screen in Unreal Engine 4

The provided assets were originally developed as a part of a larger UE4 project called DomeVR. They enable to render graphics created in UE4 to be displayed as a dome projection.

<img src="https://user-images.githubusercontent.com/89643686/156333961-b0e31d52-c2f8-4093-9369-ee438dae598e.png" width=50% height=50%>

Such a projection is suitable to display the rendered image in a dome via a projector that is projecting on a hemispherical mirror. Thus it creates an immersive experience without wearing any kind of XR glasses.  

<img src="https://user-images.githubusercontent.com/89643686/156337394-db0663fe-2118-4e0e-a954-24ddfe37ab63.jpg" width=50% height=50%>

While the dome projection was implemented differently by using a streamed level for the rendering pipeline, it is here integrated in a Character blueprint completely. This allows for much easier use of the dome projection in arbitrary UE4 projects. All it takes to activate the dome projection is to use the DomeBaseCharacter (or any derived character class) as the players pawn. The dome projection is completely implemented inside the blueprint code of the DomeBaseCharacter and the referenced render targets and materials. Therefore it is content only and should be usable at least in all UE4 versions >=4.24. 

The used method for implementing the dome projection and necessary meshes for distorting the images for the projection is based on similar method introduced by Paul Bourke for Unity: http://paulbourke.net/dome/UnityiDome/

For the fisheye projection the five camera method for generating a fisheye projection with a wider FOV (up to 240 degree) was used (http://paulbourke.net/dome/unity3d/)

The final distortion of the fisheye projection is specific to the setup and needs to be calibrated by exchanging the used mesh with a calibrated mesh that can be generated using Paul Bourkes Meshmapper application: http://paulbourke.net/dome/meshmapper/

# How to use

1. Copy the plugin in the Plugins folder at the root of your UE4 project. 
2. Open your project and activate the plugin. 
3. Use the DomeBaseCharacter (or any derived Character class) as Default Pawn in your GameMode.
4. Run your game

Following input actions already exist in the DomeBaseCharacter and can be set for basic movement capabilites in the DefaultInput.ini as follows

+AxisMappings=(AxisName="MoveForward",Scale=1.000000,Key=W)

+AxisMappings=(AxisName="MoveForward",Scale=-1.000000,Key=S)

+AxisMappings=(AxisName="MoveRight",Scale=-1.000000,Key=A)

+AxisMappings=(AxisName="MoveRight",Scale=1.000000,Key=D)

+AxisMappings=(AxisName="Turn",Scale=1.000000,Key=Right)

+AxisMappings=(AxisName="Turn",Scale=-1.000000,Key=Left)

# User Interface

In addition to the dome projection, this package also offers the possibility to display a user interface (UI) on one screen of a split-screen. To make use of the UI capabilities, the bLoadWithoutUI Variable in the DomeBaseCharacter needs to be set to false. In addition to that, the provided DomeBasePlayerController (or any derived class) needs to be set as the Player Controller Class in the Game Mode as well as Use Splitscreen needs to be activated and set to vertical in the Project Settings. 
To load a specific UI widget the respective class reference can be set by the Menu variable in the DomeBaseCharacter.

There is already a UI widget provided, which shows the different rendering steps and allows to change the camera and graphics settings. 
![2022-03-03_14h14_28](https://user-images.githubusercontent.com/89643686/156571940-05d045d4-a903-4964-9d5a-ccbd12cd22a4.png)

In addition a calibration picture can be displayed in different orientations and calibrated meshes for the dome projection can be conveniently exchanged by a drop-down menu. The drop-down menu scans all Meshes in Content/DomeProjection/Meshes/MirrorDistortion that contain the substring "warp". 

# Rendering Methods

There are two different but similar rendering methods provided. One method called Static Rendering uses premade assets for the render targets and materials and is thus limited to the fixed resolution set in the render target assets. It is activated by setting bStaticRendering in DomeBaseCharacter to true or pressing the Static Rendering button in the provided UI. The other rendering method uses dynamically created render targets and materials, which enables to set the resolution of the render targets at run-time. It can analogously be activated by setting bStaticRendering to false or pressing the Dynamic Rendering button in the UI where also different resolutions can be tested at run-time. 

# Standard Settings

In addition to the already mentioned settings, other settings about the graphic, input and player can also be set via different variables in the DomeBaseCharacter.

# Graphics Settings

Different settings about the displayed graphics can be made by the variables in the Graphics Settings category. Here the used window mode, screen resolution, V-sync and frame-rate limit can be set. 

# Input Settings

# Player Settings

# Global settings

# Drawbacks

- Occlusion culling can lead to flickering issues at walls and should ideally be turned off.
- Higher input lag due to several consecutive rendering steps.
