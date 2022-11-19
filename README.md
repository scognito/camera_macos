# Camera MacOS

Flutter stub implementation of AVFoundation Camera for MacOS.
Feel free to fork this repository and improve it!

## Getting Started

- [Basic usage](#basic-usage)
- [Limitations and notes](#limitations-and-notes)
- [License](#license)

---

## Basic usage

### Minimum supported macOS version ###
The package supports ```macOS 10.11``` and onwards.
### How to use ###
Integrate ```CameraMacOSView``` in your widget tree.
You can choose a fit method and a ```CameraMacOSMode``` (```picture``` or ```video```).
When the camera is initialized, a ```CameraMacOSController``` object is created and you can use it to do basic functions such as taking Pictures and recording Videos.

```
final GlobalKey cameraKey = GlobalKey("cameraKey");
late CameraMacOSController macOSController;


//... build method ...

CameraMacOSView(
    key: cameraKey,
    fit: BoxFit.fill,
    cameraMode: CameraMacOSMode.picture,
    onCameraInizialized: (CameraMacOSController controller) {
        setState(() {
            this.macOSController = controller;
        });
    },
    onCameraDestroyed: () {
        print("camera is destroyed");
    },
),
```
### Take picture ###
```
CameraMacOSFile? file = await macOSController.takePicture();
if(file != null) {
    Uint8List? bytes = file.bytes;
    // do something with the file
    macOSController.destroy(); // remove camera texture
}

```
### Record Video ###

```
await macOSController.recordVideo(
    url: // get url from packages such as path_provider,
    maxVideoDuration: 30, // 30 seconds
    onVideoRecordingFinished: (CameraMacOSFile? file, CameraMacOSException? exception) {
        // called when maxVideoDuration has been reached
        // do something with the file or catch the exception
    });
);

CameraMacOSFile? file = await macOSController.stopVideoRecording();

if(file != null) {
    Uint8List? bytes = file.bytes;
    // do something with the file
    macOSController.destroy(); // remove camera texture
}

```
### Notes ###
If you change the widget ```Key``` or the ```CameraMacOsMode```, the widget will reinitialize.

### Video settings ###

Default videos settings (currently locked) are:
- ```1980x1080``` resolution
- ```ac1``` audio
- ```mp4``` format
You can set a maximum video duration (in seconds) for recording videos. A native timer will fire after time has passed.
You can also set a file location. Default is in the ```Library/Cache``` directory of the application..

### Output ###
After a video or a picture is taken, a ```CameraMacOSFile``` object is generated, containing the ```bytes``` of the content. If you specify a ```url``` for a video, it will return back also the file location.

## Limitations and notes

- The plugin is just a temporary substitutive package for the official Flutter team's ```camera``` package. It will work only on macOS.
- Focus and orientation change are currently unsupported
- Video Recording resolution is currently not supported

## License

[MIT](https://github.com/riccardo-lomazzi/webview_macos/blob/main/LICENSE)

