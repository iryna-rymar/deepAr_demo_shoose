# deepar

DeepAR SDK for Web is an augmented reality SDK that allows users to integrate advanced, Snapchat-like face lenses in the browser environment. It supports face masks, effects, multiple face tracking, natural image tracking.
The SDK requires an internet connection.

## Documentation

Visit the official DeepAR docs for Web SDK here: https://docs.deepar.ai/category/deepar-sdk-for-web  
See the official example here: https://github.com/DeepARSDK/quickstart-web-js-npm

## Prerequisites

> ⚠️ Note that these steps are only needed when deploying to production (non-localhost) domain.

In order to use the DeepAR Web SDK you need to set up a license key for your web app on [developer.deepar.ai](https://developer.deepar.ai).
1. Create an account: https://developer.deepar.ai/signup.
2. Create a project: https://developer.deepar.ai/projects.
3. Add a web app to the project. Note that you need to specify the domain name which you plan to use for hosting the app.

## Installation

Using `npm`:

```shell
$ npm install deepar
```

Using `yarn`:

```shell
$ yarn add deepar
```

## Bundler setup

We recommend using a bundler to correctly include assets like models, effects and WebAssembly files.

For example, if using Webpack, add this to your `webpack.config.js`:

```javascript
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.(wasm)|(bin)|(obj)$/i,
        include: [
          path.resolve(__dirname, 'node_modules/deepar/'),
        ],
        type: 'asset/resource',
      },
      {
        include: [
          path.resolve(__dirname, 'effects/'),
        ],
        type: 'asset/resource',
      },
    ],
  },
  // ...
```

## Canvas

DeepAR requires a `canvas` element for the preview of camera, masks, filters and effects. You can add it directly in the HTML.

```html
<!DOCTYPE HTML>
<html>
<head>
  <title>DeepAR</title>
</head>
<body>
  <canvas width="1280" height="720" id="deepar-canvas"></canvas>
</body>
</html>
```

Or you can create it in Javascript.
```javascript
let canvas = document.createElement("canvas");
```

> **Note:** Be sure to set `width` and `height` properties of the `canvas`!

## Initialize DeepAR

Import DeepAR module and DeepAR WebAssembly file.  
If you wish to use an effect that uses face tracking, import the face tracking model and the effect.

```javascript
import { DeepAR } from 'deepar';
import deeparWasmPath from 'deepar/wasm/deepar.wasm';

import faceTrackingModelPath from 'deepar/models/face/models-68-extreme.bin';
import someEffect from './path/to/effect_file';
```

Then initialize DeepAR.

```javascript
const deepAR = new DeepAR({
  licenseKey: 'your_license_key_here',  
  canvas: document.getElementById('deepar-canvas'),
  deeparWasmPath,
  callbacks: {
    onInitialize: function() {
      deepAR.startVideo(true);
      deepAR.switchEffect(0, 'mask', someEffect);
    },
  },
});

// Download the face tracking model. This is requred in order to track face.
deepAR.downloadFaceTrackingModel(faceTrackingModelPath);
```

## Callbacks

DeepAR will call specified callbacks on certain events. List of all callbacks can be found in API reference.

> **IMPORTANT** You always need to provide `onInitialize` callback since most of the DeepAR methods will not work
> until SDK has fully initialized.

You can provide callbacks in the constructor of the `DeepAR` class in the `callbacks` parameter.
```javascript
const deepAR = new DeepAR({
  callbacks: {
    onInitialize: function() {
      // This is where you start camera preview and start loading effects
    },
    onScreenshotTaken: function(imageUrl) {
      // Show and/or save the screenshot
    },
    onFaceTracked: function(faceData) {
      // Inspect the face tracking features
    }
  },
  // other parameters ...
});
```
Add or change callbacks via `DeepAR.callbacks` property.

```javascript
deepAR.callbacks.onScreenshotTaken = (url) => {
    // download or show the image from url
}
```

To remove certain callback:
```javascript
deepAR.callbacks.onScreenshotTaken = undefined;
```

## Switch effects

All masks, filters, background removal, etc. are represented by effect files in DeepAR. You can load them to preview the effect.
You can download a free filter pack here: https://docs.deepar.ai/deep-ar-studio/free-filter-pack.

Load an effect using the `switchEffect` method:
```javascript
import alienEffect from './effects/alien';

// ...

deepAR.switchEffect(0, 'slot', alienEffect);
```

Load different effects on different persons' faces:
```javascript
import alienEffect from './effects/alien';
import lionEffect from './effects/lion';

// ...

deepAR.switchEffect(0, 'slot', alienEffect);
deepAR.switchEffect(1, 'slot', lionEffect);
```

Load a background removal effect:
```javascript
import segmentationEffect from './effects/background_segmentation';

// ...

deepAR.switchEffect(0, 'slot', segmentationEffect);
```

## Background removal or blur

To use background segmentation DeepAR needs to initialize the segmentation model.

```javascript
import segmentationModelPath from 'deepar/models/segmentation/segmentation-160x160-opt.bin';

// ...

const deepAR = new DeepAR({
  segmentationConfig: {
    modelPath: segmentationModelPath,
  },
  // other params ...
});
```

## Shoe try-on

To use shoe try-on feature DeepAR needs to initialize foot tracking. All the `footTrackingConfig` parameters are required.

```javascript
import poseEstimationWasmPath from 'deepar/wasm/libxzimgPoseEstimation.wasm';
import footDetectorPath from 'deepar/models/foot/foot-detection-96x96x6.bin';
import footTrackerPath from 'deepar/models/foot/foot-tracker-96x96x18-test.bin'; // or foot-tracker-96x96x13-test.bin
import footObjPath from 'deepar/models/foot/foot-model.obj';

// ...

const deepAR = new DeepAR({
  footTrackingConfig: {
    poseEstimationWasmPath,
    detectorPath: footDetectorPath,
    trackerPath: footTrackerPath,
    objPath: footObjPath,
  },
  // other params ...
});
```

## License

Please see: https://developer.deepar.ai/customer-agreement
