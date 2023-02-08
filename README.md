# vidometer-navigation

This example project shows how to integrate a **vidometer** together with ThreeJS.

You can check [live example](https://bettar.life/vidometer-navigation/).

### Installation

Execute the following command in order to install dependencies:

```jsx
npm i
```

or

```jsx
yarn install
```

## Integration

1. Add the following JS script in the **head** section:

```jsx
...
<script src="https://bettar.life/vidometry/vidometer.0.0.16.js"></script>
...
```

1. Add the following HTML elements:

```jsx
...
<div style=" position: fixed; left: 0; top: 0; width: 100vw; height: 100vh;" scrolling="no">
  <vidometry-vidometer id="vidometer"></vidometry-vidometer>
  <canvas id="canvas-video" style="position: absolute;"></canvas>
  <canvas id="canvas-gl" style="position: absolute; left: 0; top: 0; width: 100vw; height: 100vh;" width="100vw"
    height="100vh"></canvas>
</div>
...
```

1. **vidometry-vidometer** - vidometer element; 
2. **canvas***#canvas-video* - canvas which **vidometer** uses to render video frame;
3. **canvas***#canvas-gl* - canvas which **three.js** uses to render 3d scenes;

1. Add **onload** event listener to the body element and initialize Vidometer and Three.js:

 

```jsx
...
function init() {
	// INITIALIZE THREE.JS HERE
	...

	// INITIALIZE VIDOMETER
	const canvas = document.getElementById('canvas-video');
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

	const vidometer = document.getElementById('vidometer');
  // ADD EVENT LISTENERS
  // triggered when the Vidometer is ready to work
  vidometer.onReady = onVidometerReady;
  // triggered when the Vidometer detects the QR code for the first time
  vidometer.onStarted = onVidometerStarted;
  // triggered in each frame
  vidometer.onProcess = onVidometerProcess;
  // triggered whenever a QR code is detected
  vidometer.onTargetDetected = onVidometerTargetDetected;

  // INITIALIZE VIDOMETER
  vidometer.initialize(window.innerWidth, window.innerHeight, fov, canvas);
}
...
<body onload="init();">
...
```

1. Add targets to **Vidometer**:

```jsx
...
targets.push({ id: 'https://192.168.0.111:8080/', size: 0.178, x: 0, z: 0, y: 0 });
targets.push({ id: '1', size: 0.178, x: 0, z: 1, y: 90 });

vidometer.targets = targets;
...
```

## Event Listeners

**onReady()** - triggered when the Vidometer is ready to work;

**onStarted()** -  triggered when the Vidometer detects the QR code for the first time;

**onProcess(T, To, focal)** -  triggered in each frame;

*T* - source array of the 4x4 transformation of camera matrix;

*To* - source array of the 4x4 transformation of object matrix;

*focal* - camera focal length;

**onTargetDetected(targetId)** - triggered whenever a QR code is detected;

*targetId* - number of detected target;

## Initialization

**initialize(sceneWidth, sceneHeight, fov, canvas)** - Vidometer initialization method;

*sceneWidth* - width of scene in pixels;

*sceneHeight* - height of scene in pixels;

*fov* - field of view of camera, we recommend using 65 value to initialize the vidometer and three.js scene;

*canvas* - a reference to the canvas where Vidometer will render video;

## Target

Target is a JSON object of the following format:

```jsx
{ id: 'id', size: 0.178, x: 0, z: 0, y: 0 }
```

**id** - hardcoded text of QR code*(in text format) (you can use this [link](https://goqr.me/) to generate QR code)*;

**size** - the size of printed QR code in meters *(in number format)*;

**x** - X coordinate in meters *(in number format)*;

**y** - rotation in degrees (clockwise) around vertical relative to the center of the QR code *(in number format)*;

**z** - Z coordinate in meters *(in number format)*;

The target coordinate corresponds to the coordinate in the real world and fits the 3d scene coordinate.

The f**ull source of code:**

```jsx
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Demo Vidometer Navigation</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.js"
    integrity="sha512-NLtnLBS9Q2w7GKK9rKxdtgL7rA7CAS85uC/0xd9im4J/yOL4F9ZVlv634NAM7run8hz3wI2GabaA6vv8vJtHiQ=="
    crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  <script src="https://bettar.life/vidometry-dev/vidometer.0.0.16.js"></script>
  <script>

    class Scene3D {
      constructor(width, height, fov, canvas) {
        this.fov = fov;
        this.width = width;
        this.height = height;

        // 1. renderer
        this.renderer = new THREE.WebGLRenderer({
          canvas,
          antialias: true,
          alpha: true,
        });
        this.renderer.setSize(width, height);

        // 2. scene
        this.scene = new THREE.Scene();

        // 3. camera
        const aspect = width / height;
        this.camera = new THREE.PerspectiveCamera(fov, aspect, 0.01, 10000);
        this.camera.filmGauge = Math.max(width, height);
        this.camera.updateProjectionMatrix();
        this.scene.add(this.camera);

        // 4. container
        this.container = new THREE.Object3D();
        this.container.frustumCulled = false;
        this.container.matrixAutoUpdate = false;
        this.scene.add(this.container);
      }

      updateFocal(focal) {
        if (this.focal !== focal) {
          this.focal = focal;
          this.camera.setFocalLength(focal);
          this.camera.updateProjectionMatrix();
        }
      }

      show() {
        this.container.visible = true;
      }

      hide() {
        this.container.visible = false;
      }

      addLight(light) {
        this.scene.add(light);
      }

      add(object) {
        this.container.add(object);
      }

      updateObjectMatrix(roto) {
        const matrix = new THREE.Matrix4();
        matrix.set(
          roto[0], roto[1], roto[2], roto[3],
          roto[4], roto[5], roto[6], roto[7],
          roto[8], roto[9], roto[10], roto[11],
          roto[12], roto[13], roto[14], roto[15],
        );

        matrix.decompose(this.container.position, this.container.quaternion, this.container.scale);
        this.container.updateMatrix();
      }

      render(roto) {
        const matrix = new THREE.Matrix4();
        matrix.set(
          roto[0], roto[1], roto[2], roto[3],
          roto[4], roto[5], roto[6], roto[7],
          roto[8], roto[9], roto[10], roto[11],
          roto[12], roto[13], roto[14], roto[15],
        );

        matrix.decompose(this.camera.position, this.camera.quaternion, this.camera.scale);
        this.renderer.render(this.scene, this.camera);
      }

    };

    var scene, vidometer;
    let targets = [];

    function onVidometerReady() {
      const preloader = document.getElementById('preloader');
      preloader.style.display = 'none';

      const hint = document.getElementById('qr-hint');
      hint.style.display = 'block';
    }

    function onVidometerStarted() {
      const preloader = document.getElementById('preloader');
      preloader.style.display = 'none';

      const hint = document.getElementById('qr-hint');
      hint.style.display = 'none';
    }

    const onVidometerProcess = (T, To, focal) => {
      scene.updateFocal(focal);
      scene.updateObjectMatrix(To);
      scene.render(T);
    }

    function onVidometerTargetDetected(targetId) {
      const target = targets[targetId].id;
      console.log("detected target: " + target);
    }

    function init() {
      const fov = 65;
      initVidometer(fov);
      initScene(fov);
    }

    function initVidometer(fov) {
      const canvas = document.getElementById('canvas-video');
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;

      const vidometer = document.getElementById('vidometer');
      // ADD EVENT LISTENERS
      // triggered when the Vidometer is ready to work
      vidometer.onReady = onVidometerReady;
      // triggered when the Vidometer detects for the QR code the first time
      vidometer.onStarted = onVidometerStarted;
      // triggered in each frame
      vidometer.onProcess = onVidometerProcess;
      // triggered whenever a QR code is detected
      vidometer.onTargetDetected = onVidometerTargetDetected;

      // INITIALIZE VIDOMETER
      vidometer.initialize(window.innerWidth, window.innerHeight, fov, canvas);

      // ADD QR CODE TARGETS
      // id - hardcoded text into the QR;
      // size - size of printed QR code (width and height should be same);
      // x - x position, in meters;
      // z - z position, in meters;
      // y - rotation relative to the vertical in degrees, clockwise;
      // print this target: https://bettar.life/navigation/marker_url.pdf
      targets.push({ id: 'https://bettar.life/navigation/', size: 0.178, x: 0, z: 0, y: 0 });
      // print this target: https://bettar.life/navigation/1.pdf
      targets.push({ id: '1', size: 0.178, x: 0, z: 1, y: 90 });

      vidometer.targets = targets;
    }

    function initScene(fov) {
      // MAIN SCENE
      const canvas = document.getElementById('canvas-gl');
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      scene = new Scene3D(window.innerWidth, window.innerHeight, fov, canvas);

      const boxSize = 0.178;

      // ADD CUBE OBJECTS
      const cube1 = new THREE.Mesh(new THREE.BoxGeometry(boxSize, boxSize, boxSize), new THREE.MeshNormalMaterial());
      // to centralize cube move it by half of width and half of height
      cube1.position.setX(0);
      cube1.position.setZ(0);

      cube1.position.setY(boxSize / 2);

      cube1.frustumCulled = false;
      scene.add(cube1);

      const cube2 = new THREE.Mesh(new THREE.BoxGeometry(boxSize, boxSize, boxSize), new THREE.MeshNormalMaterial());
      // to centralize cube move it by half of width and half of height
      // + move it to the position of second marker x = 0m, z = 1m
      cube2.position.setX(0.0);
      cube2.position.setZ(1.0);

      cube2.position.setY(boxSize / 2);

      cube2.frustumCulled = false;
      scene.add(cube2);
    }

  </script>
</head>

<body onload="init();">

  <div style=" position: fixed; left: 0; top: 0; width: 100vw; height: 100vh;" scrolling="no">
    <vidometry-vidometer id="vidometer"></vidometry-vidometer>
    <canvas id="canvas-video" style="position: absolute;"></canvas>
    <canvas id="canvas-gl" style="position: absolute; left: 0; top: 0; width: 100vw; height: 100vh;" width="100vw"
      height="100vh"></canvas>
  </div>

  <div id="qr-hint" style="
    display: none;
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0px;
    bottom: 0px;
    z-index: 2;
    pointer-events: none;">

    <div style="
      display: block;
      position: absolute;
      left: 2vw;
      top: 28vh;
      color: #fff;
      font-size: 2vh;">Scan QR to start</div>

    <div style="
      display: block;
      position: absolute;
      left: 2vw;
      top: 32vh;
      width: 20vw;
      height: 20vw;
      background-image: url('./assets/qr.png');
      background-repeat: no-repeat;
      background-size: contain;"></div>
  </div>

  <div id="preloader" style="position: absolute; right: 2vh; top: 2vh; font-size: 2vh;">
    Loading...
  </div>

</body>

</html>
```

**QRSize:**

![https://bettar.life/navigation/assets/qr-size.png](https://bettar.life/navigation/assets/qr-size.png)

**Marker direction:**

![https://bettar.life/navigation/assets/marker.png](https://bettar.life/navigation/assets/marker.png)

**Coordinate system:**

![https://bettar.life/navigation/assets/coord.png](https://bettar.life/navigation/assets/coord.png)

**QR coordinates:**

![https://bettar.life/navigation/assets/coord-markers.png](https://bettar.life/navigation/assets/coord-markers.png)

**marker1** - locates at the position x = 0, z =0 (relative to the center of the QR marker). Marker rotation is 0 (y value in Target config is 0), so the Z coordinate is the same as the direction of the marker.  When the marker would be detected the camera position will calculate relative to this position.

**marker2** - locates at the position x = 0, z = 2.2m. Marker is rotated to -90 (y value in Target config is -90) degrees around the vertical, so Z coordinate would be rotated to 90 degrees (clockwise) relative to the QR marker direction. When the marker would be detected the camera position will calculate relative to this position.

# Demo

You can test the [demo](https://bettar.life/navigation/) by URL:  [https://bettar.life/navigation/](https://bettar.life/navigation/)

### Print markers on A4 paper format

1. Print ***[marker_url](https://bettar.life/navigation/assets/marker_url.pdf)*** [https://bettar.life/navigation/assets/marker_url.pdf](https://bettar.life/navigation/marker_url.pdf)
2. Print ***[marker_1](https://bettar.life/navigation/assets/marker_1.pdf)*** [https://bettar.life/navigation/assets/marker_1.pdf](https://bettar.life/navigation/marker_1.pdf)
3. (optional) Print ***[marker_2](https://bettar.life/navigation/assets/marker_2.pdf)*** [https://bettar.life/navigation/assets/marker_2.pdf](https://bettar.life/navigation/marker_1.pdf)

### Place marker on the floor

Place markers **marker_url** and **marker_1** randomly.

*Optionally:* place marker **marker_2** at a distance of 2.2m from the marker **marker_url** (refinement tracking).

![https://bettar.life/navigation/assets/design.png](https://bettar.life/navigation/assets/design.png)

### Scan and open application

Scan **marker_url** and open the application.

![https://bettar.life/navigation/assets/scan_qr.jpg](https://bettar.life/navigation/assets/scan_qr.jpg)

### Scan the marker in the app

Scan **marker_url** or **marker_1**

![https://bettar.life/navigation/assets/scan_qr_app.jpg](https://bettar.life/navigation/assets/scan_qr_app.jpg)

### Move according to the navigation

*Optionally:* If you are moving according to **marker_url** you can refine your position according to **marker_2** 

# Files

1. 3d model blender’s file: [https://bettar.life/navigation/assets/navigation.blend](https://bettar.life/navigation/assets/navigation.blend)
2. 3d mdel *.glb file: [https://bettar.life/navigation/navigation.glb](https://bettar.life/navigation/navigation.glb)