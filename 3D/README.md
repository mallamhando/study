# three.js Study

## renderer
화면에 그림을 그리는 가장 기본적인 객체다.

### 선언
```
const renderer = new THREE.WebGLRenderer();
```
### HTML DOM 에 renderer 를 추가
```
document.body.appendChild(renderer.domElement);
```
### 재생
```
renderer.render(scene, camera);
```

## window.requestAnimationFrame(함수)
화면주사율 주파수에 맞춰 함수를 호출한다.
함수 내부에 새로운 그림을 그리고 마지막으로 `renderer.render(scene, camera)` 를 호출하면 화면 refresh 이전에 새로운 그림이 그려진다.

## scene
### 선언
```
scene = new THREE.Scene();
```
### 3D 객체 추가
모든 3D 객체는 scene 에 추가된 뒤에 renderer 를 통해 그려진다.
```
scene.add(group);
scene.add(light);
```
### 재생
```
renderer.render(scene, camera);
```

## camera
3D 세계에서 어느 위치에서 어느 방향으로 바라볼지를 결정한다.
카메라를 움직이는 것만으로도 애니메이션 효과가 발생한다.

## 
