# three.js Study

## renderer
화면에 그림을 그리는 가장 기본적인 객체다.

```
const renderer = new THREE.WebGLRenderer(); // renderer 선언
...
document.body.appendChild(renderer.domElement); // HTML DOM 에 renderer 를 추가
...
renderer.render(scene, camera); // renderer 에 scene 과 camera 를 추가하여 그리기
```

## window.requestAnimationFrame(함수)
화면주사율 주파수에 맞춰 함수를 호출한다.
함수 내부에 새로운 그림을 그리고 마지막으로 `renderer.render(scene, camera)` 를 호출하면 화면 refresh 이전에 새로운 그림이 그려진다.
