# Camera macOS

Implementation of ```AVKit``` camera for ```macOS```.
Does basic things.
Feel free to fork this repository and improve it!

## Getting Started

- [Basic usage](#basic-usage)
- [Limitations and notes](#limitations-and-notes)
- [License](#license)

---

## Basic usage


### How to use ###
Integrate ```CameraMacOSView``` in your widget tree.
You can choose a ```BoxFit``` method and a ```CameraMacOSMode``` (```picture``` or ```video```).
When the camera is initialized, a ```CameraMacOSController``` object is created and can be used to do basic things such as taking pictures and recording videos.

```
final GlobalKey cameraKey = GlobalKey("cameraKey");
late CameraMacOSController macOSController;

//... build method ...

CameraMacOSView(
    key: cameraKey,
    fit: BoxFit.fill,
    cameraMode: CameraMacOSMode.photo,
    onCameraInizialized: (CameraMacOSController controller) {
        setState(() {
            this.macOSController = controller;
        });
    },
),
```

It works with external cameras too: specify an optional ```deviceId``` for the camera and an optional ```audioDeviceId``` for the microphone.
Both IDs are related to the ```uniqueID``` property of ```AVCaptureDevice```, and can be obtained with the ```listDevices``` method.
Audio recording can be enabled or disabled with the ```enableAudio``` flag both in the initialization phase or within the ```recordVideo``` method (default ```true```).

```
String? deviceId;
String? audioDeviceId;

// List devices

List<CameraMacOSDevice> videoDevices = await CameraMacOS.listDevices({ deviceType: CameraMacOSMode.video });
List<CameraMacOSDevice> audioDevices = await CameraMacOS.listDevices({ deviceType: CameraMacOSMode.audio });

// Set devices
deviceId = videoDevices.first.deviceId
audioDeviceId = audioDevices.first.deviceId

//... build method ...

CameraMacOSView(
    deviceId: deviceId, // optional camera parameter, defaults to the Mac primary camera
    audioDeviceId: audioDeviceId, // optional microphone parameter, defaults to the Mac primary microphone
    enableAudio: true,
    cameraMode: CameraMacOSMode.video,
    onCameraInizialized: (CameraMacOSController controller) {
        // ...
    },
),
```

A ```CameraMacOSDevice``` object contains the following properties (mapped to the original ```AVCaptureDevice``` class):
- ```deviceId```
- ```localizedName```
- ```manufacturer```
- ```deviceType``` (video or audio)

### Take a picture ###
```
CameraMacOSFile? file = await macOSController.takePicture();
if(file != null) {
    Uint8List? bytes = file.bytes;
    // do something with the file...
}

```
### Record a video ###

```
await macOSController.recordVideo(
    url: // get url from packages such as path_provider,
    maxVideoDuration: 30, // duration in seconds,
    enableAudio: null, // optional, overrides initialization parameter
    onVideoRecordingFinished: (CameraMacOSFile? file, CameraMacOSException? exception) {
        // called when maxVideoDuration has been reached
        // do something with the file or catch the exception
    });
);

CameraMacOSFile? file = await macOSController.stopVideoRecording();

if(file != null) {
    Uint8List? bytes = file.bytes;
    // do something with the file...
}

```

### Widget refreshing ###
- If you change the widget ```Key```, ```deviceId```  or the ```CameraMacOsMode```, the widget will reinitialize.

### Video settings ###

You can enable or disable audio recording with the ```enableAudio``` flag.

Default videos settings (currently locked) are:
- max resolution available to the selected camera
- default microphone format (```ac1```)
- default video format (```mp4```)

You can set a maximum video duration (in seconds) for recording videos with ```maxVideoDuration```.
A native timer will fire after time has passed.

You can also set a file location. Default is in the ```Library/Cache``` directory of the application.

### Output ###
After a video or a picture is taken, a ```CameraMacOSFile``` object is generated, containing the ```bytes``` of the content. If you specify a ```url``` for a video, it will return back also the file location.

## Limitations and notes

- The package supports ```macOS 10.11``` and onwards.
- The plugin is just a temporary substitutive package for the official Flutter team's ```camera``` package. It will work only on ```macOS```.
- Focus and orientation change are currently unsupported
- Video Recording resolution change is currently not supported

## License

[MIT](https://github.com/riccardo-lomazzi/webview_macos/blob/main/LICENSE)

