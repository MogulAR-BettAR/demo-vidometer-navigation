# vidometer-navigation

This example project shows how to integrate **vidometer** into your web navigation application.

You can check [live example](https://bettar.life/navigation/).

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
<script src="https://bettar.life/vidometry/vidometry-scene.0.0.15.js"></script>
...
```

1. Add the following HTML elements:

```jsx
...
<vidometry-scene id="vidometry-scene">

    <!-- add lightning -->
    <vidometry-ambient-light color="#ffeeb1" intensity="2"></vidometry-ambient-light>
    <vidometry-point-light color="#ffffff" intensity="3" position="10 10 10"></vidometry-point-light>

    <vidometry-gltf src="./assets/navigation.glb"></vidometry-gltf>

    <vidometry-vidometer id="vidometry-vidometer"></vidometry-vidometer>

</vidometry-scene>
...
```

1. **vidometry-scene** - main scene element; 
2. **vidometry-ambient-light** - ambient light;
3. **vidometry-point-light** - point light;
4. **vidometry-gltf** - gltf/glb element;
    1. **src** - the source of the 3d model;
5. **vidometry-vidometer** - vidometer element;

1. Add **onload** event listener to the body element. Inside the **init** function add **onLoaded** and **onStarted** event listener to the scene element:

```jsx
...
function init() {
  const scene = document.getElementById('vidometry-scene');
  scene.onLoaded = onSceneLoaded;
  scene.onStarted = onSceneStarted;
}

...
<body onload="init();">
...
```

1. Inside the onSceneLoaded function add all QR targets:

```jsx
function onSceneLoaded() {
  const vidometer = document.getElementById('vidometry-vidometer');
  vidometer.addTarget('https://bettar.life/navigation/', 0.178, -0.11, 0, 0);
  vidometer.addTarget('1', 0.178, -0.11, 0, 10);
  vidometer.addTarget('2', 0.178, -0.11, 0, 2.2);
}
```

addTarget(QRtext: string, QRSize: number, x: number, y:number, z:number) :

1. QRText - text of QR code data;
2. QRSize - the size of printed QR code in meters;
3. x - X coordinate in meters;
4. y - Y coordinate in meters, height above the ground (it is recommended to set 0);
5. z - Z coordinate in meters;

**QRSize:**

![https://bettar.life/navigation/assets/qr-size.png](https://bettar.life/navigation/assets/qr-size.png)

**QR coordinates:**

![https://bettar.life/navigation/assets/coord.png](https://bettar.life/navigation/assets/coord.png)

**Full source of code:**

```jsx
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Demo Vidometer Scene</title>
  <script src="https://bettar.life/vidometry/vidometer.0.0.16.js"></script>
  <script src="https://bettar.life/vidometry/vidometry-scene.0.0.15.js"></script>
  <script>

    function onSceneLoaded() {
      const vidometer = document.getElementById('vidometry-vidometer');
      vidometer.addTarget('https://bettar.life/navigation/', 0.178, -0.11, 0, 0);
      vidometer.addTarget('1', 0.178, -0.11, 0, 10);
      vidometer.addTarget('2', 0.178, -0.11, 0, 2.2);

      const preloader = document.getElementById('preloader');
      preloader.style.display = 'none';

      const hint = document.getElementById('qr-hint');
      hint.style.display = 'block';
    }

    function onSceneStarted() {
      const hint = document.getElementById('qr-hint');
      hint.style.display = 'none';
    }

    function init() {
      const scene = document.getElementById('vidometry-scene');
      scene.onLoaded = onSceneLoaded;
      scene.onStarted = onSceneStarted;
    }

  </script>
</head>

<body onload="init();">

  <div style=" position: fixed; left: 0; top: 0; width: 100vw; height: 100vh;" scrolling="no">

    <vidometry-scene id="vidometry-scene">

      <!-- add lightning -->
      <vidometry-ambient-light color="#ffeeb1" intensity="2"></vidometry-ambient-light>
      <vidometry-point-light color="#ffffff" intensity="3" position="10 10 10"></vidometry-point-light>

      <vidometry-gltf src="./assets/navigation.glb"></vidometry-gltf>

      <vidometry-vidometer id="vidometry-vidometer"></vidometry-vidometer>

    </vidometry-scene>

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

# Demo

You can test the [demo](https://bettar.life/navigation/) by URL:  [https://bettar.life/navigation/](https://bettar.life/navigation/)

### Print markers on A4 paper format

1. Print [***marker_url***](https://bettar.life/navigation/marker_url.pdf) [https://bettar.life/navigation/marker_url.pdf](https://bettar.life/navigation/marker_url.pdf)
2. Print [***marker_1***](https://bettar.life/navigation/marker_1.pdf) [https://bettar.life/navigation/marker_1.pdf](https://bettar.life/navigation/marker_1.pdf)
3. (optional) Print [***marker_2***](https://bettar.life/navigation/marker_2.pdf) [https://bettar.life/navigation/marker_2.pdf](https://bettar.life/navigation/marker_1.pdf)

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

*Optionally:* If you moving according to **marker_url** you can refine your position according to **marker_2** 

# Files

1. 3d model blender’s file: [https://bettar.life/navigation/assets/navigation.blend](https://bettar.life/navigation/assets/navigation.blend)
2. 3d mdel *.glb file: [https://bettar.life/navigation/navigation.glb](https://bettar.life/navigation/navigation.glb)