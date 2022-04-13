# react-native-video

> :warning: **Version 6 Alpha**: The following documentation may refer to features only available through the v6.0.0 alpha releases, [please see version 5.2.x](https://github.com/react-native-video/react-native-video/blob/v5.2.0/README.md) for the current documentation!

## A `<Video>` component for react-native.
Version 6.x recommends react-native >= 0.68.2. 
<br>For older versions of react-native, [please use version 5.x](https://github.com/react-native-video/react-native-video/tree/v5.2.0).

### Version 6.0.0 breaking changes

Version 6.0.0 is introducing dozens of breaking changes, mostly through updated dependecies and significant refactoring. While the API remains compatible, the significant internal changes require full testing with your app to ensure all functionality remains operational. Please view the [Changelog](CHANGELOG.md) for specific breaking changes.

### Installing Version 6.0.0 Alphas
Whilst we finalise version 6.0.0 you can install the latest alpha from npm
Using npm:

```shell
npm install --save react-native-video
```

or using yarn:

```shell
yarn add react-native-video
```

Then follow the instructions for your platform to link react-native-video into your project:

### iOS installation
<details>
  <summary>iOS details</summary>

#### Standard Method

**React Native 0.60 and above**

Run `npx pod-install`. Linking is not required in React Native 0.60 and above.

**React Native 0.59 and below**

Run `react-native link react-native-video` to link the react-native-video library.

#### Using CocoaPods (required to enable caching)

Setup your Podfile like it is described in the [react-native documentation](https://facebook.github.io/react-native/docs/integration-with-existing-apps#configuring-cocoapods-dependencies). 

Depending on your requirements you have to choose between the two possible subpodspecs:

Video only:

```diff
  pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'
+  `pod 'react-native-video', :path => '../node_modules/react-native-video/react-native-video.podspec'`
end
```

Video with caching ([more info](docs/caching.md)):

```diff
  pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'
+  `pod 'react-native-video/VideoCaching', :path => '../node_modules/react-native-video/react-native-video.podspec'`
end
```

</details>

### tvOS installation
  <details>
  <summary>tvOS details</summary>

`react-native link react-native-video` doesn’t work properly with the tvOS target so we need to add the library manually.

First select your project in Xcode.

<img src="./docs/tvOS-step-1.jpg" width="40%">

After that, select the tvOS target of your application and select « General » tab

<img src="./docs/tvOS-step-2.jpg" width="40%">

Scroll to « Linked Frameworks and Libraries » and tap on the + button

<img src="./docs/tvOS-step-3.jpg" width="40%">

Select RCTVideo-tvOS

<img src="./docs/tvOS-step-4.jpg" width="40%">
</details>

### Android installation
<details>
  <summary>Android details</summary>
 
Linking is not required in React Native 0.60 and above.
If your project is using React Native < 0.60, run `react-native link react-native-video` to link the react-native-video library.

Or if you have trouble, make the following additions to the given files manually:

#### **android/settings.gradle**

The newer ExoPlayer library will work for most people.

```gradle
include ':react-native-video'
project(':react-native-video').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-video/android-exoplayer')
```

If you need to use the old Android MediaPlayer based player, use the following instead:

```gradle
include ':react-native-video'
project(':react-native-video').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-video/android')
```

#### **android/app/build.gradle**

From version >= 5.0.0, you have to apply these changes:

```diff
dependencies {
   ...
    compile project(':react-native-video')
+   implementation "androidx.appcompat:appcompat:1.0.0"
-   implementation "com.android.support:appcompat-v7:${rootProject.ext.supportLibVersion}"

}
```

#### **android/gradle.properties**

Migrating to AndroidX (needs version >= 5.0.0):

```gradle.properties
android.useAndroidX=true
android.enableJetifier=true
```

#### **MainApplication.java**

On top, where imports are:

```java
import com.brentvatne.react.ReactVideoPackage;
```

Add the `ReactVideoPackage` class to your list of exported packages.

```java
@Override
protected List<ReactPackage> getPackages() {
    return Arrays.asList(
            new MainReactPackage(),
            new ReactVideoPackage()
    );
}
```
</details>

### Windows installation
<details>
  <summary>Windows RNW C++/WinRT details</summary>

#### Autolinking

**React Native Windows 0.63 and above**

Autolinking should automatically add react-native-video to your app.

#### Manual Linking

**React Native Windows 0.62**

Make the following additions to the given files manually:

##### **windows\myapp.sln**

Add the _ReactNativeVideoCPP_ project to your solution (eg. `windows\myapp.sln`):

1. Open your solution in Visual Studio 2019
2. Right-click Solution icon in Solution Explorer > Add > Existing Project...
3. Select `node_modules\react-native-video\windows\ReactNativeVideoCPP\ReactNativeVideoCPP.vcxproj`

##### **windows\myapp\myapp.vcxproj**

Add a reference to _ReactNativeVideoCPP_ to your main application project (eg. `windows\myapp\myapp.vcxproj`):

1. Open your solution in Visual Studio 2019
2. Right-click main application project > Add > Reference...
3. Check _ReactNativeVideoCPP_ from Solution Projects

##### **pch.h**

Add `#include "winrt/ReactNativeVideoCPP.h"`.

##### **app.cpp**

Add `PackageProviders().Append(winrt::ReactNativeVideoCPP::ReactPackageProvider());` before `InitializeComponent();`.

**React Native Windows 0.61 and below**

Follow the manual linking instuctions for React Native Windows 0.62 above, but substitute _ReactNativeVideoCPP61_ for _ReactNativeVideoCPP_.

</details>

### react-native-dom installation
<details>
  <summary>react-native-dom details</summary>

Make the following additions to the given files manually:

#### **dom/bootstrap.js**

Import RCTVideoManager and add it to the list of nativeModules:

```javascript
import { RNDomInstance } from "react-native-dom";
import { name as appName } from "../app.json";
import RCTVideoManager from 'react-native-video/dom/RCTVideoManager'; // Add this

// Path to RN Bundle Entrypoint ================================================
const rnBundlePath = "./entry.bundle?platform=dom&dev=true";

// React Native DOM Runtime Options =============================================
const ReactNativeDomOptions = {
  enableHotReload: false,
  nativeModules: [RCTVideoManager] // Add this
};
```
</details>

## Usage

```javascript
// Load the module

import Video from 'react-native-video';

// Within your render function, assuming you have a file called
// "background.mp4" in your project. You can include multiple videos
// on a single screen if you like.

<Video source={{uri: "background"}}   // Can be a URL or a local file.
       ref={(ref) => {
         this.player = ref
       }}                                      // Store reference
       onBuffer={this.onBuffer}                // Callback when remote video is buffering
       onError={this.videoError}               // Callback when video cannot be loaded
       style={styles.backgroundVideo} />

// Later on in your styles..
var styles = StyleSheet.create({
  backgroundVideo: {
    position: 'absolute',
    top: 0,
    left: 0,
    bottom: 0,
    right: 0,
  },
});
```

### Configurable props
* [allowsExternalPlayback](#allowsexternalplayback)
* [audioOnly](#audioonly)
* [automaticallyWaitsToMinimizeStalling](#automaticallyWaitsToMinimizeStalling)
* [bufferConfig](#bufferconfig)
* [cache](#cache)
* [controls](#controls)
* [currentPlaybackTime](#currentPlaybackTime)
* [disableFocus](#disableFocus)
* [filter](#filter)
* [filterEnabled](#filterEnabled)
* [fullscreen](#fullscreen)
* [fullscreenAutorotate](#fullscreenautorotate)
* [fullscreenOrientation](#fullscreenorientation)
* [headers](#headers)
* [hideShutterView](#hideshutterview)
* [id](#id)
* [ignoreSilentSwitch](#ignoresilentswitch)
* [maxBitRate](#maxbitrate)
* [maxCacheFileSize](#maxcachefilesize)
* [maxCacheSize](#maxcachesize)
* [minLoadRetryCount](#minLoadRetryCount)
* [mixWithOthers](#mixWithOthers)
* [muted](#muted)
* [paused](#paused)
* [pictureInPicture](#pictureinpicture)
* [playInBackground](#playinbackground)
* [playWhenInactive](#playwheninactive)
* [poster](#poster)
* [posterResizeMode](#posterresizemode)
* [preferredForwardBufferDuration](#preferredForwardBufferDuration)
* [preventsDisplaySleepDuringVideoPlayback](#preventsDisplaySleepDuringVideoPlayback)
* [progressUpdateInterval](#progressupdateinterval)
* [rate](#rate)
* [repeat](#repeat)
* [reportBandwidth](#reportbandwidth)
* [resizeMode](#resizemode)
* [selectedAudioTrack](#selectedaudiotrack)
* [selectedTextTrack](#selectedtexttrack)
* [selectedVideoTrack](#selectedvideotrack)
* [source](#source)
* [stereoPan](#stereopan)
* [textTracks](#texttracks)
* [trackId](#trackId)
* [useTextureView](#usetextureview)
* [volume](#volume)

### Event props
* [onAudioBecomingNoisy](#onaudiobecomingnoisy)
* [onBandwidthUpdate](#onbandwidthupdate)
* [onEnd](#onend)
* [onExternalPlaybackChange](#onexternalplaybackchange)
* [onFullscreenPlayerWillPresent](#onfullscreenplayerwillpresent)
* [onFullscreenPlayerDidPresent](#onfullscreenplayerdidpresent)
* [onFullscreenPlayerWillDismiss](#onfullscreenplayerwilldismiss)
* [onFullscreenPlayerDidDismiss](#onfullscreenplayerdiddismiss)
* [onLoad](#onload)
* [onLoadStart](#onloadstart)
* [onReadyForDisplay](#onreadyfordisplay)
* [onPictureInPictureStatusChanged](#onpictureinpicturestatuschanged)
* [onPlaybackRateChange](#onplaybackratechange)
* [onProgress](#onprogress)
* [onSeek](#onseek)
* [onRestoreUserInterfaceForPictureInPictureStop](#onrestoreuserinterfaceforpictureinpicturestop)
* [onTimedMetadata](#ontimedmetadata)

### Methods
* [dismissFullscreenPlayer](#dismissfullscreenplayer)
* [presentFullscreenPlayer](#presentfullscreenplayer)
* [save](#save)
* [restoreUserInterfaceForPictureInPictureStop](#restoreuserinterfaceforpictureinpicturestop)
* [seek](#seek)

### Configurable props

#### allowsExternalPlayback
Indicates whether the player allows switching to external playback mode such as AirPlay or HDMI.
* **true (default)** - allow switching to external playback mode
* **false** -  Don't allow switching to external playback mode

Platforms: iOS

#### audioOnly
Indicates whether the player should only play the audio track and instead of displaying the video track, show the poster instead.
* **false (default)** - Display the video as normal
* **true** - Show the poster and play the audio

For this to work, the poster prop must be set.

Platforms: all

#### automaticallyWaitsToMinimizeStalling
A Boolean value that indicates whether the player should automatically delay playback in order to minimize stalling. For clients linked against iOS 10.0 and later
* **false** - Immediately starts playback
* **true (default)** - Delays playback in order to minimize stalling

Platforms: iOS

#### bufferConfig
Adjust the buffer settings. This prop takes an object with one or more of the properties listed below.

Property | Type | Description
--- | --- | ---
minBufferMs | number | The default minimum duration of media that the player will attempt to ensure is buffered at all times, in milliseconds.
maxBufferMs | number | The default maximum duration of media that the player will attempt to buffer, in milliseconds.
bufferForPlaybackMs | number | The default duration of media that must be buffered for playback to start or resume following a user action such as a seek, in milliseconds.
bufferForPlaybackAfterRebufferMs | number | The default duration of media that must be buffered for playback to resume after a rebuffer, in milliseconds. A rebuffer is defined to be caused by buffer depletion rather than a user action.

This prop should only be set when you are setting the source, changing it after the media is loaded will cause it to be reloaded.

Example with default values:
```
bufferConfig={{
  minBufferMs: 15000,
  maxBufferMs: 50000,
  bufferForPlaybackMs: 2500,
  bufferForPlaybackAfterRebufferMs: 5000
}}
```

Platforms: Android ExoPlayer

#### cache
To enable or disable caching for the particular video src. Caching is done in a folder name `video` in the internal cache.
* **false (default)** - Disable caching
* **true** - Enable caching

Platforms: Android ExoPlayer

#### currentPlaybackTime
When playing an HLS live stream with a `EXT-X-PROGRAM-DATE-TIME` tag configured, then this property will contain the epoch value in msec.

Platforms: Android ExoPlayer, iOS

#### controls
Determines whether to show player controls.
* **false (default)** - Don't show player controls
* **true** - Show player controls

Note on iOS, controls are always shown when in fullscreen mode.

For Android MediaPlayer, you will need to build your own controls or use a package like [react-native-video-controls](https://github.com/itsnubix/react-native-video-controls) or [react-native-video-player](https://github.com/cornedor/react-native-video-player).

Note on Android ExoPlayer, native controls are available by default. If needed, you can also add your controls or use a package like [react-native-video-controls].

Platforms: Android ExoPlayer, iOS, react-native-dom

#### disableFocus
Determines whether video audio should override background music/audio in Android devices.
* **false (default)** - Override background audio/music
* **true** - Let background audio/music from other apps play

Platforms: Android Exoplayer

### DRM
To setup DRM please follow [this guide](./DRM.md)

Platforms: Android Exoplayer, iOS

#### filter
Add video filter
* **FilterType.NONE (default)** - No Filter
* **FilterType.INVERT** - CIColorInvert
* **FilterType.MONOCHROME** - CIColorMonochrome
* **FilterType.POSTERIZE** - CIColorPosterize
* **FilterType.FALSE** - CIFalseColor
* **FilterType.MAXIMUMCOMPONENT** - CIMaximumComponent
* **FilterType.MINIMUMCOMPONENT** - CIMinimumComponent
* **FilterType.CHROME** - CIPhotoEffectChrome
* **FilterType.FADE** - CIPhotoEffectFade
* **FilterType.INSTANT** - CIPhotoEffectInstant
* **FilterType.MONO** - CIPhotoEffectMono
* **FilterType.NOIR** - CIPhotoEffectNoir
* **FilterType.PROCESS** - CIPhotoEffectProcess
* **FilterType.TONAL** - CIPhotoEffectTonal
* **FilterType.TRANSFER** - CIPhotoEffectTransfer
* **FilterType.SEPIA** - CISepiaTone

For more details on these filters refer to the [iOS docs](https://developer.apple.com/library/archive/documentation/GraphicsImaging/Reference/CoreImageFilterReference/index.html#//apple_ref/doc/uid/TP30000136-SW55).

Notes: 
1. Using a filter can impact CPU usage. A workaround is to save the video with the filter and then load the saved video.
2. Video filter is currently not supported on HLS playlists.
3. `filterEnabled` must be set to `true`

Platforms: iOS

#### filterEnabled
Enable video filter. 

* **false (default)** - Don't enable filter
* **true** - Enable filter

Platforms: iOS

#### fullscreen
Controls whether the player enters fullscreen on play.
* **false (default)** - Don't display the video in fullscreen
* **true** - Display the video in fullscreen

Platforms: iOS

#### fullscreenAutorotate
If a preferred [fullscreenOrientation](#fullscreenorientation) is set, causes the video to rotate to that orientation but permits rotation of the screen to orientation held by user. Defaults to TRUE.

Platforms: iOS

#### fullscreenOrientation

* **all (default)** - 
* **landscape**
* **portrait**

Platforms: iOS

#### headers
Pass headers to the HTTP client. Can be used for authorization. Headers must be a part of the source object.

Example:
```
source={{
  uri: "https://www.example.com/video.mp4",
  headers: {
    Authorization: 'bearer some-token-value',
    'X-Custom-Header': 'some value'
  }
}}
```

Platforms: Android ExoPlayer

#### hideShutterView
Controls whether the ExoPlayer shutter view (black screen while loading) is enabled.

* **false (default)** - Show shutter view 
* **true** - Hide shutter view

Platforms: Android ExoPlayer

#### id
Set the DOM id element so you can use document.getElementById on web platforms. Accepts string values.

Example:
```
id="video"
```

Platforms: react-native-dom

#### ignoreSilentSwitch
Controls the iOS silent switch behavior
* **"inherit" (default)** - Use the default AVPlayer behavior
* **"ignore"** - Play audio even if the silent switch is set
* **"obey"** - Don't play audio if the silent switch is set

Platforms: iOS

#### maxBitRate
Sets the desired limit, in bits per second, of network bandwidth consumption when multiple video streams are available for a playlist.

Default: 0. Don't limit the maxBitRate.

Example:
```
maxBitRate={2000000} // 2 megabits
```

Platforms: Android ExoPlayer, iOS

#### maxCacheFileSize
Sets the maximum size for each file to be cached. If a file or larger size comes, it would not be cached.

Default: 10MB

Example:
```
maxCacheFileSize={20} // 20MB cache size for each file
```

Platforms: Android ExoPlayer

#### maxCacheSize
Sets the maximum cache size for videos. It follows LRU cache eviction and removes the Least Recently Used
video from cache after the size if filled.

Default: 100MB

Example:
```
maxCacheSize={300} // 300MB total cache size
```

Platforms: Android ExoPlayer

#### minLoadRetryCount
Sets the minimum number of times to retry loading data before failing and reporting an error to the application. Useful to recover from transient internet failures.

Default: 3. Retry 3 times.

Example:
```
minLoadRetryCount={5} // retry 5 times
```

Platforms: Android ExoPlayer

#### mixWithOthers
Controls how Audio mix with other apps.
* **"inherit" (default)** - Use the default AVPlayer behavior
* **"mix"** - Audio from this video mixes with audio from other apps.
* **"duck"** - Reduces the volume of other apps while audio from this video plays.

Platforms: iOS

#### muted
Controls whether the audio is muted
* **false (default)** - Don't mute audio
* **true** - Mute audio

Platforms: all

#### paused
Controls whether the media is paused
* **false (default)** - Don't pause the media
* **true** - Pause the media

Platforms: all

#### pictureInPicture
Determine whether the media should played as picture in picture.
* **false (default)** - Don't not play as picture in picture
* **true** - Play the media as picture in picture

Platforms: iOS

#### playInBackground
Determine whether the media should continue playing while the app is in the background. This allows customers to continue listening to the audio.
* **false (default)** - Don't continue playing the media
* **true** - Continue playing the media

To use this feature on iOS, you must:
* [Enable Background Audio](https://developer.apple.com/library/archive/documentation/Audio/Conceptual/AudioSessionProgrammingGuide/AudioSessionBasics/AudioSessionBasics.html#//apple_ref/doc/uid/TP40007875-CH3-SW3) in your Xcode project
* Set the ignoreSilentSwitch prop to "ignore"

Platforms: Android ExoPlayer, Android MediaPlayer, iOS

#### playWhenInactive
Determine whether the media should continue playing when notifications or the Control Center are in front of the video.
* **false (default)** - Don't continue playing the media
* **true** - Continue playing the media

Platforms: iOS

#### poster
An image to display while the video is loading
<br>Value: string with a URL for the poster, e.g. "https://baconmockup.com/300/200/"

Platforms: all

#### posterResizeMode
Determines how to resize the poster image when the frame doesn't match the raw video dimensions.
* **"contain" (default)** - Scale the image uniformly (maintain the image's aspect ratio) so that both dimensions (width and height) of the image will be equal to or less than the corresponding dimension of the view (minus padding).
* **"center"** - Center the image in the view along both dimensions. If the image is larger than the view, scale it down uniformly so that it is contained in the view.
* **"cover"** - Scale the image uniformly (maintain the image's aspect ratio) so that both dimensions (width and height) of the image will be equal to or larger than the corresponding dimension of the view (minus padding).
* **"none"** - Don't apply resize
* **"repeat"** - Repeat the image to cover the frame of the view. The image will keep its size and aspect ratio. (iOS only)
* **"stretch"** - Scale width and height independently, This may change the aspect ratio of the src.

Platforms: all

#### preferredForwardBufferDuration
The duration the player should buffer media from the network ahead of the playhead to guard against playback disruption. Sets the [preferredForwardBufferDuration](https://developer.apple.com/documentation/avfoundation/avplayeritem/1643630-preferredforwardbufferduration) instance property on AVPlayerItem.

Default: 0

Platforms: iOS

#### preventsDisplaySleepDuringVideoPlayback
Controls whether or not the display should be allowed to sleep while playing the video. Default is not to allow display to sleep.

Default: true

Platforms: iOS, Android

#### progressUpdateInterval
Delay in milliseconds between onProgress events in milliseconds.

Default: 250.0

Platforms: all

### rate
Speed at which the media should play. 
* **0.0** - Pauses the video
* **1.0** - Play at normal speed
* **Other values** - Slow down or speed up playback

Platforms: all

Note: For Android MediaPlayer, rate is only supported on Android 6.0 and higher devices.

#### repeat
Determine whether to repeat the video when the end is reached
* **false (default)** - Don't repeat the video
* **true** - Repeat the video

Platforms: all

#### reportBandwidth
Determine whether to generate onBandwidthUpdate events. This is needed due to the high frequency of these events on ExoPlayer.

* **false (default)** - Don't generate onBandwidthUpdate events
* **true** - Generate onBandwidthUpdate events

Platforms: Android ExoPlayer

#### resizeMode
Determines how to resize the video when the frame doesn't match the raw video dimensions.
* **"none" (default)** - Don't apply resize
* **"contain"** - Scale the video uniformly (maintain the video's aspect ratio) so that both dimensions (width and height) of the video will be equal to or less than the corresponding dimension of the view (minus padding).
* **"cover"** - Scale the video uniformly (maintain the video's aspect ratio) so that both dimensions (width and height) of the image will be equal to or larger than the corresponding dimension of the view (minus padding).
* **"stretch"** - Scale width and height independently, This may change the aspect ratio of the src.

Platforms: Android ExoPlayer, Android MediaPlayer, iOS, Windows UWP

#### selectedAudioTrack
Configure which audio track, if any, is played.

```
selectedAudioTrack={{
  type: Type,
  value: Value
}}
```

Example:
```
selectedAudioTrack={{
  type: "title",
  value: "Dubbing"
}}
```

Type | Value | Description
--- | --- | ---
"system" (default) | N/A | Play the audio track that matches the system language. If none match, play the first track.
"disabled" | N/A | Turn off audio
"title" | string | Play the audio track with the title specified as the Value, e.g. "French"
"language" | string | Play the audio track with the language specified as the Value, e.g. "fr"
"index" | number | Play the audio track with the index specified as the value, e.g. 0

If a track matching the specified Type (and Value if appropriate) is unavailable, the first audio track will be played. If multiple tracks match the criteria, the first match will be used.

Platforms: Android ExoPlayer, iOS

#### selectedTextTrack
Configure which text track (caption or subtitle), if any, is shown.

```
selectedTextTrack={{
  type: Type,
  value: Value
}}
```

Example:
```
selectedTextTrack={{
  type: "title",
  value: "English Subtitles"
}}
```

Type | Value | Description
--- | --- | ---
"system" (default) | N/A | Display captions only if the system preference for captions is enabled
"disabled" | N/A | Don't display a text track
"title" | string | Display the text track with the title specified as the Value, e.g. "French 1"
"language" | string | Display the text track with the language specified as the Value, e.g. "fr"
"index" | number | Display the text track with the index specified as the value, e.g. 0

Both iOS & Android (only 4.4 and higher) offer Settings to enable Captions for hearing impaired people. If "system" is selected and the Captions Setting is enabled, iOS/Android will look for a caption that matches that customer's language and display it. 

If a track matching the specified Type (and Value if appropriate) is unavailable, no text track will be displayed. If multiple tracks match the criteria, the first match will be used.

Platforms: Android ExoPlayer, iOS

#### selectedVideoTrack
Configure which video track should be played. By default, the player uses Adaptive Bitrate Streaming to automatically select the stream it thinks will perform best based on available bandwidth.

```
npm install --save react-native-video@alpha
```
using yarn:
```
yarn add react-native-video@alpha
```

## Useful resources
- [Documentation](API.md)
- [Changelog](CHANGELOG.md)
- [Contribution guide](CONTRIBUTING.md)
- [Usefull Side Project](./docs/PROJECTS.md)
- [Advanced debugging](./docs/DEBUGGING.md)

**react-native-video** was originally created by [Brent Vatne](https://github.com/brentvatne)
