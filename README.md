# Hello WebGL 2.0: Using WebGL 2.0 to Implement Fluid Simulation

<div align="center">
    <img src="./assets/screenshot.jpg"></img>
    <h4 align="center">Hello WebGL 2.0: Using WebGL 2.0 to Implement Fluid Simulation</h4>
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
    WebGL은 인터넷 브라우저 환경에서 OpenGL을 플러그인 도움 없이 공식적으로 사용할 수 있도록 Khronos Group에서 제정한 웹 그래픽스 API에요.
    2017년 2월에 OpenGL ES 3.0기반 WebGL 2.0이 공개됐어요.
    WebGL2에 입문하고자 하시는 분들을 위해 WebGL로 할 수 있는 다양한 것 중 가장 흥미로웠던 유체 시뮬레이션에 대한 튜토리얼이에요.
</div>

## Try Here
시뮬레이터 사용해보기:
- (모바일도 가능한 초간단 방법 👍🏻 ) [Try on Netlify](https://webgl2-fluid-simulation.netlify.app)
- (조금 귀찮은 방법) Git Repository를 클론한 후, `index.html`를 웹 브라우저에서 열기. (콘솔에 CORS 관련 에러가 나타나도 대부분 작동해요!)

## Prerequisites
- WebGL 2.0을 지원하는 환경 ([여기서 확인하기](http://get.webgl.org/))

## What The Tutorial Covers
- WebGL1과 WebGL2의 차이
- Fluid Simulation에서 사용된 WebGL 2.0 코드 (예제 코드에 포함)
- Khronos Group의 WebGL 2.0 Specification에 대한 추적

## WebGL1과 WebGL2의 차이
### 1. `getContext`를 호출할 때 `webgl` 대신 `webgl2` 사용
```javascript
// WebGL1
var gl = canvas.getContext('webgl');

// WebGL2 @ script.js:121
var gl = canvas.getContext('webgl2');
```

### 2. 부동 소수점 프레임버퍼 추가
WebGL1에선 부동 소수점 텍스처를 지원하는지 확인하기 위해 `OES_texture_float`를 활성화 한 후 부동 소수점 텍스처를 생성하고 프레임버퍼에 추가해 `gl.checkFramebufferStatus`를 호출해 `gl.FRAMEBUFFER_COMPLETE`를 반환하는지 확인해야 했어요.

WebGL2에서 `gl.checkFramebufferStatus`로 부터 `gl.FRAMEBUFFER_COMPLETE`를 반환받기 위해선 `EXT_color_buffer_float`를 허용해주세요. [공식 문서 보러 가기](https://www.khronos.org/registry/webgl/specs/latest/2.0/#4.1.5)

`HALF_FLOAT` 프레임버퍼에도 동일하게 적용해주세요!

```javascript
function getWebGLContext(canvas) {
    
    ...

    // WebGL2
    if (isWebGL2) {
        gl.getExtension('EXT_color_buffer_float');
        supportLinearFiltering = gl.getExtension('OES_texture_float_linear');
    } 
    
    // WebGL1
    else {
        halfFloat = gl.getExtension('OES_texture_half_float');
        supportLinearFiltering = gl.getExtension('OES_texture_half_float_linear');
    }

    ...

}
```

### 3. WebGL1 확장의 WebGL2 표준화
아래 확장들이 표준에 편입됐어요.
- Depth Textures (`WEBGL_depth_texture`)
- Floating Point Textures (`OES_texture_float/OES_texture_float_linear`)
- Half Floating Point Textures (`OES_texture_half_float/- OES_texture_half_float_linear`)
- Vertex Array Objects (`OES_vertex_array_object`)
- Standard Derivatives (`OES_standard_derivatives`)
- Instanced Drawing (`ANGLE_instanced_arrays`)
- UNSIGNED_INT indices (`OES_element_index_uint`)
- Setting gl_FragDepth (`EXT_frag_depth`)
- Blend Equation MIN/MAX (`EXT_blend_minmax`)
- Direct texture LOD access (`EXT_shader_texture_lod`)
- Multiple Draw Buffers (`WEBGL_draw_buffers`)
- Texture access in vertex shaders

자주 사용되던 `OES_vertex_array_object`를 예시로 확장 없이 사용하는 예시를 들어볼게요.
```javascript
// WebGL1
var ext = gl.getExtension("OES_vertex_array_object");
if (!ext) {
  // tell user they don't have the required extension or work around it
} else {
  var someVAO = ext.createVertexArrayOES();
}

// WebGL2
var someVAO = gl.createVertexArray();
```

### 4. `GLSL 300 es` 사용
사용하는 쉐이더를 GLSL 3.00 ES로 업그레이드 하면 좋다. 그러기 위해선 쉐이더 선언의 첫 줄이 `#version 300 es`야 해요. 반드시 첫 줄이여야 하기 때문에 주석이나 개행이 이루어지면 안돼요.

```javascript
// BAD                    +---There's a new line here!
// BAD                    V
var vertexShaderSource = `
#version 300 es
...
`;

// GOOD
var vertexShaderSource = `#version 300 es
...
`;
```

#### 4-1. `attribute` 대신 `in` 사용
```javascript
// WebGL1
attribute vec4 a_position;
attribute vec2 a_texcoord;
attribute vec3 a_normal;

// WebGL2 
in vec4 a_position;
in vec2 a_texcoord;
in vec3 a_normal;
```

#### 4-2. `varying` 대신 `in/out` 사용
```javascript
// WebGL1
varying vec2 v_texcoord;
varying vec3 v_normal;

// WebGL2 
// Vertex Shader
out vec2 v_texcoord;
out vec3 v_normal;

// Framgent Shader
in vec2 v_texcoord;
in vec3 v_normal;
```

#### 4-3. `gl_FragColor` 대신 원하는 변수명 사용 가능
```javascript
// WebGL1
gl_FragColor = vec4(1, 0, 0, 1);  // red

// WebGL2
out vec4 myOutputColor;
 
void main() {
   myOutputColor = vec4(1, 0, 0, 1);  // red
}
```

#### 4-4. `texture2D` 대신 `texture` 사용
```javascript
// WebGL1
vec4 color1 = texture2D(u_some2DTexture, ...);
vec4 color2 = textureCube(u_someCubeTexture, ...);

// WebGL2
vec4 color1 = texture(u_some2DTexture, ...);
vec4 color2 = texture(u_someCubeTexture, ...);
```

더 자세한 내용이 궁금하다면? [공식 문서 보러 가기](https://khronos.org/registry/webgl/specs/latest/2.0/#4.3)

### 5. Non-Power-of-Two 텍스처의 사용 허용
WebGL 1.0 API와 다르게, WebGL 2.0에서는 non-power-of-two 텍스처를 사용할 수 있어요. 다양한 mipmapping 기능과 wrapping mode를 사용할 수 있어요. [공식 문서 보러 가기](https://www.khronos.org/registry/webgl/specs/latest/2.0/#4.1.3)

### 6. 새로 추가된 텍스처 포맷
<details>
<summary>무려 52가지나! 궁금하면 클릭해서 열어보기</summary>
  
- `RGBA32I`
- `RGBA32UI`
- `RGBA16I`
- `RGBA16UI`
- `RGBA8`
- `RGBA8I`
- `RGBA8UI`
- `SRGB8_ALPHA8`
- `RGB10_A2`
- `RGB10_A2UI`
- `RGBA4`
- `RGB5_A1`
- `RGB8`
- `RGB565`
- `RG32I`
- `RG32UI`
- `RG16I`
- `RG16UI`
- `RG8`
- `RG8I`
- `RG8UI`
- `R32I`
- `R32UI`
- `R16I`
- `R16UI`
- `R8`
- `R8I`
- `R8UI`
- `RGBA32F`
- `RGBA16F`
- `RGBA8_SNORM`
- `RGB32F`
- `RGB32I`
- `RGB32UI`
- `RGB16F`
- `RGB16I`
- `RGB16UI`
- `RGB8_SNORM`
- `RGB8I`
- `RGB8UI`
- `SRGB8`
- `R11F_G11F_B10F`
- `RGB9_E5`
- `RG32F`
- `RG16F`
- `RG8_SNORM`
- `R32F`
- `R16F`
- `R8_SNORM`
- `DEPTH_COMPONENT32F`
- `DEPTH_COMPONENT24`
- `DEPTH_COMPONENT16`
</details>

## License
### MIT License
- Linking: Permissive
- Distribution: Permissive
- Modification: Permissive
- Patent Grant: Manually
- Private Use: Yes
- Sublicensing: Permissive
- TM Grant: Manually

## References
### WebGL 2.0
- https://webgl2fundamentals.org/
- https://www.khronos.org/registry/webgl/specs/latest/2.0/
- https://www.khronos.org/assets/uploads/developers/library/2017-webgl-webinar/Khronos-Webinar-WebGL-20-is-here_What-you-need-to-know_Apr17.pdf
- https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Using_Extensions

### Open Source Licensing
- https://en.wikipedia.org/wiki/Comparison_of_free_and_open-source_software_licences

## Acknowledgements
Many thanks to [PavelDoGreat](https://github.com/PavelDoGreat/WebGL-Fluid-Simulation) for the mesmerizing fluid simulation implementation.
