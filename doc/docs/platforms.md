Supported Platforms
=========================

This page documents the status of various platforms and renderers supported by Godot.

## Table of Contents

**Operating Systems**
* [Windows](#windows)
* [Linux](#linux)
* [macOS](#macos)
* [Android](#android)
* [IOS](#ios)
* [Steam Deck](#steam-deck)
* [HTML / WebGL](#webgl)

**Renderers**
* [Forward+ / Vulkan](#vulkan)
* [Forward+ / Direct3D 12](#d3d12)
* [Forward+ / Metal](#metal)
* [Mobile / Vulkan](#mobile)
* [Compatibility / OpenGLES 3](#compatibility)

## Windows

Fully supported. See [renderers](#supported-renderers).

## Linux

Fully supported. See [renderers](#supported-renderers).

## macOS

Godot and Terrain3D work fine on macOS, however Apple security is overly aggressive when using our release binaries.

Users have reported errors like this:

`"libterrain.macos.debug" cannot be opened because the developer cannot be verified. macOS cannot verify that this app is free from malware.`

Running the following commands within the downloaded and unzipped directory appears to resolve the issue.

```
$ xattr -dr com.apple.quarantine addons/terrain_3d/bin/libterrain.macos.debug.framework/libterrain.macos.debug
$ xattr -dr com.apple.quarantine addons/terrain_3d/bin/libterrain.macos.release.framework/libterrain.macos.release
```

You can also [read comments and workarounds](https://github.com/TokisanGames/Terrain3D/issues/227)
from other users. 

If bypassing Apple security is not working, or if approaching a release date, macOS users should [build from source](building_from_source.md) so you can sign the binaries with your own developer account.


## Android

As of Terrain3D 0.9.1 and Godot 4.2, Android is reported to work. 

* Use textures that Godot imports (converts) such as PNG or TGA, not DDS.
* Enable `Project Settings/Rendering/Textures/VRAM Compression/Import ETC2 ASTC`.

The release builds include binaries for arm32 and arm64.

There is a [texture artifact](https://github.com/TokisanGames/Terrain3D/issues/137) affecting some systems using the demo DDS textures. This may be alleviated by using PNGs as noted above, but isn't confirmed.

Further reading:
* [Issue 197](https://github.com/TokisanGames/Terrain3D/issues/197)


## IOS

As of Terrain3D 0.9.1 and Godot 4.2, iOS is reported to work with the following setup:

* Use textures that Godot imports (converts) such as PNG or TGA, not DDS.
* Enable `Project Settings/Rendering/Textures/VRAM Compression/Import ETC2 ASTC`.
* Set `Project Settings/Application/Config/Icon` to a valid file (eg `res://icon.png` or svg).
* The Terrain3D release includes iOS builds, however they aren't signed and may not work.
* If needed, build the iOS library and make sure the binaries are placed where identified in `terrain.gdextension`:
```
     scons platform=ios target=template_debug
     scons platform=ios target=template_release
```

* Select `Project/Export`, Add the iOS export preset and configure with `App Store Team ID` and `Bundle Identifier`, then export.

```{image} images/ios_export.png
:target: ../_images/ios_export.png
```

Once it has been exported, you can open it in XCode, run locally, or on your device.

Further reading:
* [Issue 218](https://github.com/TokisanGames/Terrain3D/issues/218)
* [PR 219](https://github.com/TokisanGames/Terrain3D/pull/219)
* [PR 295](https://github.com/TokisanGames/Terrain3D/pull/295)


## Steam Deck

As of Terrain3D v0.9.1 and Godot 4.2, the first generation Steam Deck is reported working, running the demo at 200+ fps.

The user got it working with the following:
* Use SteamOS 3.5.7
* Install `glibc` and `linux-api-headers` in addition to the standard Godot dependencies
* [Build from source](building_from_source.md)

Further reading:
* [Issue 220](https://github.com/TokisanGames/Terrain3D/issues/220#issuecomment-1837552459)


## WebGL

The Terrain3D library can be exported to WebGL, however the terrain will not currently render. It requires the [Compatibility Renderer (read more)](#compatibility), which is now supported, and but there are some additional shader elements that need to be sorted out before it will work. 

Further reading:
* [Issue 502](https://github.com/TokisanGames/Terrain3D/issues/502)


Supported Renderers
====================

* [Forward+ / Vulkan](#vulkan)
* [Forward+ / Direct3D 12](#d3d12)
* [Forward+ / Metal](#metal)
* [Forwad Mobile](#mobile)
* [Compatibility / OpenGLES 3](#compatibility)

## Vulkan

The Forward+ Vulkan renderer is fully supported.

## D3D12

The Forward+ Direct3D 12 renderer was recently merged into Godot 4.3 and is not yet supported by Terrain3D. Early testing shows artifacts.

## Metal

Support for Apple's Metal for iOS and macOS was recently merged into Godot 4.4-dev1. It is not yet supported by Terrain3D. No testing has been done.

## Mobile

The Forward Vulkan Mobile renderer is fully supported in Terrain3D 0.9.3. There is a precision artifact in 0.9.2.

## Compatibility

The OpenGLES 3.0 Compatibility renderer is supported from Terrain3D 0.9.3 with Godot 4.3. There is partial support for Godot 4.2.

* If using a custom override shader, we add a special COMPATIBILITY_DEFINES block to your shader that will allow certain features to work properly (eg the fma() function). We remove this block from your shader if you switch back to Mobile or Forward. It is normal to receive a shader dump in your console during this transition, but it should not repeat every start, once saved.

* `IS_COMPATIBILITY` is defined in this block should you wish to check against it with your own custom preprocessor statements.

* If enabling compatibility mode on the command line, we cannot detect that currently. You can tell Terrain3D with a special parameter:

    `Godot_v4.3-stable_win64_console.exe --rendering-driver opengl3 -e project.godot --terrain3d-renderer=compatibility`

**Godot 4.2**

In addition to the above notes, OpenGLES is incomplete in 4.2. It does work with the following caveats:

* There are some startup warnings about depth textures and glow that you can ignore.
* `VRAM Compressed` is not supported. In the `Import` panel, you must set your texture files to either `VRAM Uncompressed` or `Lossless` and reimport.
* The command line option `--rendering-method gl_compatibility` breaks the terrain in 4.2. Don't use it. The above command works for 4.2 or 4.3.
 
Further reading:

* [Issue 217](https://github.com/TokisanGames/Terrain3D/issues/217)
* [PR 500](https://github.com/TokisanGames/Terrain3D/pull/500)