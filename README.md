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

 3. Add targets to the **Vidometer**:

***a: add targets as a JSON object to the targets attribute of Vidometer:***

```jsx
...
<vidometry-vidometer id="vidometry-vidometer" targets='[
   {
      "id":"https://bettar.life/navigation/",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":0
   },
   {
      "id":"1",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":10
   },
   {
      "id":"2",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":2.2
   }
]'></vidometry-vidometer>
...
```

Targets should be as an array of Target objects in JSON format:

```jsx
[
	Target,
	Target,
	...
	Target
]
```

Target object format:

```jsx
{
      "id":"https://bettar.life/navigation/",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":0
   }
```

id - QR code data *(in text format)*;

size - the size of printed QR code in meters *(in number format)*;

x - X coordinate in meters *(in number format)*;

y - rotation in degrees (clockwise) around vertical relative to the upper left corner of the QR code *(in number format)*;

z - Z coordinate in meters *(in number format)*;

***b. add targets programmatically:***

Add **onload** event listener to the body element, inside this listener create an empty array and add each target like a new element of this array:

```jsx
...
function init() {
  let targets = [];
  targets.push({ id: 'https://bettar.life/navigation/', size: 0.178, x: -0.11, z: 0, y: 0 });
  targets.push({ id: '1', size: 0.178, x: -0.11, z: 10, y: 0 });
  targets.push({ id: '2', size: 0.178, x: -0.11, z: 2.2, y: 0 });

  const vidometer = document.getElementById('vidometry-vidometer');
  vidometer.targets = targets;
}
...
<body onload="init();">
...
```

id - QR code data *(in text format)*;

size - the size of printed QR code in meters *(in number format)*;

x - X coordinate in meters *(in number format)*;

y - rotation in degrees (clockwise) around vertical relative to the upper left corner of the QR code *(in number format)*;

z - Z coordinate in meters *(in number format)*;

The f**ull source of code:**

```jsx
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Demo Vidometer Scene</title>
  <script src="https://bettar.life/vidometry-dev/vidometer.0.0.16.js"></script>
  <script src="https://bettar.life/vidometry-dev/vidometry-scene.0.0.15.js"></script>
</head>

<body >

  <div style=" position: fixed; left: 0; top: 0; width: 100vw; height: 100vh;" scrolling="no">

    <vidometry-scene id="vidometry-scene">

      <!-- add lightning -->
      <vidometry-ambient-light color="#ffeeb1" intensity="2"></vidometry-ambient-light>
      <vidometry-point-light color="#ffffff" intensity="3" position="10 10 10"></vidometry-point-light>

      <vidometry-gltf src="./assets/navigation.glb"></vidometry-gltf>

      <vidometry-vidometer id="vidometry-vidometer" targets='[
   {
      "id":"https://bettar.life/navigation/",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":0
   },
   {
      "id":"1",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":10
   },
   {
      "id":"2",
      "size":0.178,
      "x":-0.11,
      "y":0,
      "z":2.2
   }
]'></vidometry-vidometer>

    </vidometry-scene>

  </div>

</body>

</html>
```

## Events

To add events to the scene you just need to add **onload** event listener to the body element. Inside the **init** function add **onLoaded** and **onStarted** event listeners to the scene element:

```jsx
...
function onSceneLoaded() {
	// Add your code here
}

function onSceneStarted() {
	// Add your code here
}

function init() {
  const scene = document.getElementById('vidometry-scene');
  scene.onLoaded = onSceneLoaded;
  scene.onStarted = onSceneStarted;
}

...
<body onload="init();">
...
```

**onLoaded** - thrown when the Vidometer is initialized and all resources are loaded - Vidometer is ready to work.

**onStarted** - thrown when the QR code is detected the first time. The scene gets visible.

**QRSize:**

![https://bettar.life/navigation/assets/qr-size.png](https://bettar.life/navigation/assets/qr-size.png)

**QR coordinates:**

![https://bettar.life/navigation/assets/coord.png](https://bettar.life/navigation/assets/coord.png)

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