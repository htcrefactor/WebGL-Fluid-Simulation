# WebGL Fluid Simulation Tutorial

<div align="center">
    <img src="./assets/screenshot.jpg"></img>
    <h4 align="center">WebGL2 Fluid Simulation Tutorial</h4>
    <p align="center">
        <img src="http://img.shields.io/badge/-WebGL2-990000?style=flat&logo=WebGL&link=https://github.com/htcrefactor/WebGL-Fluid-Simulation"/>
        <img src="http://img.shields.io/badge/-HTML-E34F26?style=flat&logo=HTML5&link=https://github.com/htcrefactor/WebGL-Fluid-Simulation"/>
        <img src="http://img.shields.io/badge/-CSS3-1572B6?style=flat&logo=CSS3&link=https://github.com/htcrefactor/WebGL-Fluid-Simulation"/>
        <img src="http://img.shields.io/badge/-JavaScript-F7DF1E?style=flat&logo=JavaScript&link=https://github.com/htcrefactor/WebGL-Fluid-Simulation"/>
        <img src="http://img.shields.io/badge/-Netlify-00C7B7?style=flat&logo=Netlify&link=https://webgl2-fluid-simulation.netlify.app"/>
    </p>
    <p align="center">
        <a href="#try-here">Try Here</a> • 
        <a href="#prerequisites">Prerequisites</a> • 
        <a href="#what-this-tutorial-covers">What This Tutorial Covers</a> •   
        <a href="#license">License</a> • 
        <a href="#references">References</a> • 
        <a href="#acknowledgements">Acknowledgements</a>
    </p>
    WebGL은 인터넷 브라우저 환경에서 OpenGL을 플러그인 도움 없이 공식적으로 사용할 수 있도록 Khronos Group에서 제정한 웹 그래픽스 API입니다.
    2017년 2월에 OpenGL ES 3.0기반 WebGL 2.0이 공개됐습니다.
    WebGL2에 입문하고자 하시는 분들을 위해 WebGL로 할 수 있는 다양한 것 중 가장 흥미로웠던 유체 시뮬레이션에 대한 튜토리얼 입니다.
</div>

## Try Here
시뮬레이터 사용해보기:
- (모바일도 가능한 초간단 방법👍🏻) [Try on Netlify](https://webgl2-fluid-simulation.netlify.app)
- (조금 귀찮은 방법) Git Repository를 클론한 후, `index.html`를 웹 브라우저에서 열기.

## Prerequisites
- WebGL2를 지원하는 최신 브라우저를 권장해요 ([여기서 확인하기](http://get.webgl.org/))

## What The Tutorial Covers
- WebGL1과 WebGL2의 차이
- WebGL1에서 WebGL2로 코드를 올리는 방법

## WebGL1 vs WebGL2


## Moving from WebGL1 to WebGL2
### `getContext`를 호출할 때 `webgl` 대신 `webgl2` 사용
`var gl = someCanvas.getContext("webgl2");`

### WebGL1 확장의 WebGL2 표준화
WebGL1:
```javascript
var ext = gl.getExtension("OES_vertex_array_object");
if (!ext) {
  // tell user they don't have the required extension or work around it
} else {
  var someVAO = ext.createVertexArrayOES();
}
```
WebGL2:
`var someVAO = gl.createVertexArray();`

### `GLSL 300 es` 사용
사용하는 쉐이더를 GLSL 3.00 ES로 업그레이드 하면 좋다. 그러기 위해선 쉐이더 선언의 첫 줄이 `#version 300 es`면 된다. 반드시 첫 줄이여야 하기 때문에 주석이나 개행이 이루어지면 안된다.

```javascript
// BAD!!!!                +---There's a new line here!
// BAD!!!!                V
var vertexShaderSource = `
#version 300 es
..
`;

// GOOD
var vertexShaderSource = `#version 300 es
...
`;
```

#### `attribute` 대신 `in` 사용
#### `varying` 대신 `in/out` 사용
#### `gl_FragColor` 대신 원하는 변수명 사용 가능
#### `texture2D` 대신 `texture` 사용
```javascript
// WebGL1
vec4 color1 = texture2D(u_some2DTexture, ...);
vec4 color2 = textureCube(u_someCubeTexture, ...);

// WebGL2
vec4 color1 = texture(u_some2DTexture, ...);
vec4 color2 = texture(u_someCubeTexture, ...);
```


## License
MIT License
- Linking: Permissive
- Distribution: Permissive
- Modification: Permissive
- Patent Grant: Manually
- Private Use: Yes
- Sublicensing: Permissive
- TM Grant: Manually

## References
WebGL2
- https://webgl2fundamentals.org/webgl/lessons/webgl2-whats-new.html
- https://webgl2fundamentals.org/webgl/lessons/webgl1-to-webgl2.html
- https://www.khronos.org/assets/uploads/developers/library/2017-webgl-webinar/Khronos-Webinar-WebGL-20-is-here_What-you-need-to-know_Apr17.pdf
- https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Using_Extensions

Open Source Licensing
- https://en.wikipedia.org/wiki/Comparison_of_free_and_open-source_software_licences

## Acknowledgements
Many thanks to [PavelDoGreat](https://github.com/PavelDoGreat/WebGL-Fluid-Simulation) for the mesmerizing implementation of the fluid simulator.
