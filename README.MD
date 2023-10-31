## WEB AR Streamer API

### Native interface

WEB AR Streamer application provides all interactions via events and js property of window object :

`window.mobileNativeInterface`

### Finish loading

To hide preloading screen of native application you have to call native interface function

`window.mobileNativeInterface.finishLoading()`

### Camera event

Camera position updates from native part via event 'onTrackingPoseUpdated'
Event detail contains position and rotation of camera 
 
* translation - Pose of the center of the camera, in world coordinates.
* quaternion - Quaternion rotation of the camera, in world coordinates.

```json
{
  "quaternion": {
    "w": 0.973961,
    "x": -0.1578228,
    "y": 0.15740584,
    "z": 0.04141751
  },
  "translation": [
    -0.007826636,
    0.012311755,
    0.005161943
  ]
}

```

#### Reference code :

```typescript
interface Pose {
    quaternion: {
        x: number;
        y: number;
        z: number;
        w: number;
    };
    translation: [number, number, number];
}

let slamVals: Pose;
const slamTranslate = new THREE.Vector3();
const slamQuat = new THREE.Quaternion();

window.addEventListener("onTrackingPoseUpdated", (ev: any) => {

    slamVals = ev.detail;

    const quat = slamVals.quaternion;
    slamTranslate.set(...slamVals.translation);
    slamQuat.set(quat.x, quat.y, quat.z, quat.w);

    camera.position.copy(slamTranslate);

    camera.quaternion.copy(slamQuat);

    camera.updateMatrixWorld();
    camera.updateMatrix();
});
```

### Add tracking images

window.mobileNativeInterface.addTrackingImageUrl(imageName: String, imageUrl: String, imageWidth: String) - Adds a new image by download URL.

Arguments:
* imageName - Name of the downloading image. You can determine what picture is tracking when invoking getTrackingImageInfo() by the name provided in this function
* imageUrl - Url of the downloading image.
* imageWidth - Physical width of the image in meters AS JS STRING. Example - "0.7"

```
window.mobileNativeInterface.addTrackingImageUrl('1', 'https://example.com/1.png', "0.7")
```

### Delete tracking image
window.mobileNativeInterface.deleteTrackingImage(imageName: String) - Deletes tracking image by name.

Arguments:
* imageName - Name of the downloading image. You can determine what picture is tracking when invoking getTrackingImageInfo() by the name provided in this function

### Delete all tracking images
window.mobileNativeInterface.clearImageTrackingList() - Clears list of tracking images.

### Image event

Image position/rotation updates from native part via event 'onTrackingImagesUpdated'
Event detail contains array of position and rotation of tracking images :

* centerPose - Pose of the center of the augmented image, in world coordinates.
* name - Name of this augmented image.
* sizeXmeters - Estimated width, in metres, of the corresponding physical image, as measured along the local X-axis of the coordinate space centered on the image..
* sizeZmeters - Estimated height, in metres, of the corresponding physical image, as measured along the local Z-axis of the coordinate space centered on the image.
* trackingMethod - FULL_TRACKING, LAST_KNOWN_POSE, NOT_TRACKING.
* trackingState - PAUSED, STOP, TRACKING.

```json
[
  {
    "centerPose": {
      "quaternion": {
        "w": -0.0057567656,
        "x": 0.83262336,
        "y": 0.0017986894,
        "z": 0.5538066
      },
      "translation": [
        0.057569526,
        -0.34433982,
        0.097405285
      ]
    },
    "name": "sun",
    "sizeXmeters": 0.18083161,
    "sizeZmeters": 0.16455677,
    "trackingMethod": "FULL_TRACKING",
    "trackingState": "TRACKING"
  }
]

```
