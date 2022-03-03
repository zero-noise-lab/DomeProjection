# DomeProjection
Adding a Character blueprint that enables a dome projection in Unreal Engine 4

This plugin was originally developed as a part of a larger UE4 project called DomeVR. It enables to render graphics created in UE4 to be displayed as a dome projection.

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

# Drawbacks

- Occlusion culling should be turned off, since it can lead to flickering issues
- 
