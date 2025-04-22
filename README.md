# DomeProjection: Enhanced Dome Projection System for Unreal Engine

DomeProjection is an integrated suite of actors and assets designed to facilitate dome projection within Unreal Engine 5 (UE5), complemented by an optional split-screen user interface for enhanced user interaction. Originally developed as part of the DomeVR project, this system enables seamless rendering of UE5 graphics for immersive dome projection experiences in the form of a simple Unreal plugin.

The system enables to render gameplay in Unreal for display on a dome screen, utilizing a projector and hemispherical mirror setup to achieve a visually coherent projection surface.

![Dome Projection Illustration](https://user-images.githubusercontent.com/89643686/156999909-2f752051-adb0-42ed-b5a6-34ff104d7764.png)


In the original design of the dome projection system, a streaming level technique was employed to establish the rendering pipeline for the dome projection. However, this architecture was revised for this plugin and is now seamlessly integrated in a Character blueprint. This enables a much easier usage without any complicated additions in existing Unreal projects. Activation of the dome projection feature is straightforward: developers need only to assign the _DomeBaseCharacter_ blueprint—or any class derived from it—as the player's pawn. This blueprint encapsulates the entirety of the dome projection logic, including interactions with designated render targets and materials, ensuring a blueprint-only solution that is compatible with UE5.3 and later.

The implementation strategy for the dome projection, including the creation of essential meshes for accurately distorting projection images, leverages a methodology akin to that devised by Paul Bourke for Unity applications. This approach is detailed on Bourke's website ([UnityiDome](http://paulbourke.net/dome/UnityiDome/)), providing a foundational concept for adapting similar techniques within Unreal Engine.

Central to this method is the use of a five-camera setup aimed at producing a fisheye projection capable of encompassing a wide field of view (FOV), extending up to 240 degrees. This technique is thoroughly explained in Bourke's documentation on creating fisheye projections within Unity ([Fisheye Projection](http://paulbourke.net/dome/unity3d/)), offering insights into achieving broad visual coverage in dome projection setups.

Adjusting the fisheye projection to fit specific dome setups requires a calibration process, wherein the default mesh used for image distortion is replaced with a specifically calibrated mesh. This tailored mesh can be crafted using Bourke's Meshmapper tool ([Meshmapper Application](http://paulbourke.net/dome/meshmapper/)), a resource designed to facilitate the creation of meshes that conform precisely to the unique contours and characteristics of individual projection environments. This calibration ensures that the fisheye distortion aligns perfectly with the dome's geometry, optimizing the visual experience.

## Requirements

-   Unreal Engine 5.3 or later

## Implementation Guide

To utilize DomeProjection, clone the plugin into your project's Plugin folder. Activate the _DomeBaseGameMode_, enable split-screen by setting `Activate Use Splitscreen` to true and select vertical orientation in the Project Settings. Input configurations leverage the Enhanced Input system introduced in Unreal Engine 5.0.

## User Interface (UI) Features

The DomeProjection package enhances user interaction by incorporating a user interface (UI) on one side of a split-screen setup, providing direct control over projection settings. This UI feature can be enabled or disabled by adjusting the `bLoadWithoutUI` variable in the _DomeBaseCharacter_ blueprint to true, allowing users to choose between a UI-driven or pure projection experience based on their needs.

The UI is developed within a widget named _DomeBaseUI_. For customization, users can replace _DomeBaseUI_ with another widget by changing the `Menu` variable in _DomeBaseCharacter_, enabling the use of a different UI layout or functionality that better suits their project's requirements. By default, the UI is positioned on the left side of the split-screen, but this can be altered by toggling the `bSwitchSplitscreens` variable in _DomeBaseCharacter_ to true, providing flexibility in how the UI and projection content are displayed.

The package comes with a predefined UI widget that displays the different stages of rendering and offers interactive elements to adjust camera and graphics settings, facilitating a user-friendly interface for managing projection parameters.

![UI Widget Overview](https://user-images.githubusercontent.com/89643686/156571940-05d045d4-a903-4964-9d5a-ccbd12cd22a4.png)

Moreover, the UI includes functionality for showing a calibration image in various orientations, aiding in the alignment of the projection with the dome surface. An essential feature is the 'Warp Mesh Settings' dropdown menu, which scans the _Content/DomeProjection/Meshes/MirrorDistortion_ directory for meshes tagged with "warp." This enables users to easily switch between different calibrated meshes, facilitating the calibration process for optimal dome projection. This approach to UI design emphasizes practicality and ease of use, aiming to streamline the setup and calibration of dome projections.

## Rendering Techniques

The DomeProjection toolkit offers two distinct rendering approaches: Static Rendering and Dynamic Rendering, each catering to different project requirements and setups.

**Static Rendering** is designed around the use of pre-configured assets for both the render targets and materials. This method is inherently constrained by the resolutions predefined within the render target assets, limiting scalability and flexibility. Activation is straightforward: users set the `bStaticRendering` variable within the _DomeBaseCharacter_ to true or engage the Static Rendering mode via the corresponding button in the user interface. This mode is ideal for projects where a consistent, unchanging resolution is sufficient.

Conversely, **Dynamic Rendering** introduces the capability to generate render targets and materials in real-time, affording users the flexibility to adjust the resolution of these render targets during runtime. This method is enabled by setting the `bStaticRendering` variable to false or selecting the Dynamic Rendering option in the UI. A key advantage here is the ability to experiment with various resolutions directly in the runtime environment, optimizing performance and visual quality to meet specific needs.

# Standard Settings

Beyond the rendering methods, DomeProjection allows for extensive customization through various settings within the _DomeBaseCharacter_.

## Graphics Settings

The Graphics Settings section provides a suite of variables for adjusting the visual output, including:

-   **Window Mode (`WindowMode`)**: Defines the window behavior (e.g., fullscreen, windowed).
-   **Screen Resolution (`ScreenResolution`)**: Sets the display resolution.
-   **V-Sync (`VSync`)**: Enables or disables vertical sync to prevent screen tearing.
-   **Frame-rate Limit (`FramerateLimit`)**: Caps the maximum frames per second (FPS) to reduce GPU strain.

For projects utilizing Dynamic Rendering, default values for render target resolutions can be specified (`FisheyeResolution`, `CaptureResolution`), ensuring a balance between performance and visual fidelity. Additional options include toggling the UI visibility (`bLoadWithoutUI`) and switching the UI display side on the split-screen (`bSwitchSplitscreens`), providing further control over the user experience and presentation.

## Input Modifiers

Input modifiers within the DomeProjection toolkit provide granular control over the character's movement dynamics:

-   **BaseTurnRate**: This variable adjusts the rotation speed of the character around its yaw axis, influencing how quickly the character turns in response to player input.
-   **Fraction Variables**: Suffixes in variable names ending with `_Fraction` indicate their role in scaling input sensitivity across different axes. These variables offer the flexibility to dynamically adjust input responsiveness during gameplay, rather than being fixed settings within the Project Settings. This feature is particularly useful for fine-tuning character control to match player preferences or gameplay requirements.
-   **SkipInputValue**: To address the issue of controllers that do not rest at a zero value when idle, the `SkipInputValue` setting is used. Input values falling below this threshold are ignored, mitigating unintended character movement or actions due to controller drift or inaccuracies.

## Player Settings

Player settings define the physical parameters of the character within the virtual environment:

-   **PlayerHeight**: Specifies the character's height, impacting how the character interacts with the environment and the perspective of the player camera.
-   **PlayerRadius**: Determines the character's circumference, affecting collision detection and spatial navigation.
-   **CameraHeight**: Sets the vertical position of the camera relative to the character, essentially representing the height at which the character's eyes are located. This setting is constrained by the `PlayerHeight`, ensuring the camera's position remains realistic.
-   **CameraRotation**: Controls the camera's pitch rotation, allowing for adjustments to the viewing angle upwards or downwards.

## Global Settings

To address the potential loss of customized settings upon level change or character reinitialization, DomeProjection introduces a mechanism for preserving these settings globally. Implementing the `IDomeGameInstance` interface within a custom GameInstance class allows for the storage of variable values across the game session. When a GameInstance implementing this interface is linked to the `GameInstance` variable of _DomeBaseCharacter_, it retrieves settings through the interface functions of the specified GameInstance, rather than relying solely on its internal variables. This system ensures continuity and consistency of user-defined settings throughout the gameplay experience, enhancing the overall game dynamic and player satisfaction.

## Known Limitations

- Occlusion culling can lead to flickering issues at walls and should ideally be turned off.
- In total 7 rendering steps lead to a high demand on GPU resources.
- Higher input lag due to several consecutive rendering steps (Fisheye->Dome->Screen).