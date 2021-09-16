# 셰이더 (Shader)

### 셰이더

* 컴퓨터 그래픽스에서 물체의 위치(x, y, z), 색상, 텍스쳐, 빛 등 다양한 시각적 효과를 계산하여 최종적으로 화면에 출력할 픽셀의 위치와 색상을 계산하는 함수입니다.
* 파이프라인에서 주요 부분을 쉬운 문법으로 뽑아서 그래픽 디자이너가 사용할 수 있도록 만듭니다.
* 셰이더에 사용하는 언어는 <u>HLSL</u>, <u>GLSL</u>, <u>CG</u> 등이 존재합니다.
  * HLSL(High Level Shading Language): Direct X에서 나온 셰이더 언어
  * GLSL(OpenGL Shading Language): OpenGL에서 나온 셰이더 언어
  * CG(C for Graphics): Ndivia에서 나온 셰이더 언어
* 유니티는 CG를 사용하지만, 최근 URP (Universial Rendering Pipleline)부터는 **HLSL**을 사용하고 있습니다.

### 유니티 셰이더

* 유니티 셰이더는 3가지 타입이 있습니다.

  1. Surface Shader

     * 빛과 그림자의 영향을 받는 셰이더입니다.
     * 유니티의 라이팅 파이프라인과 상호작용합니다. 
     * 높은 추상화 레벨이 적용됩니다.
     * 짧은 코드만 적어도 많은 부분이 자동 생성됩니다.

  2. Vertex & Fragment Shader

     * 빛과 상호작용하기 어렵습니다.
     * 셰이더를 작성할 수 있는 자유도가 높지만 코드의 작성량이 많습니다.
     * 빛과 상호작용이 필요 없거나 Surface Shader만으로는 만들 수 없는 이색적인 효과를 낼 수 있습니다.
     * 일종의 Surface Shader의 상위 버전으로 보아도 무방합니다.

  3. Fixed Function Shader

     * 간단한 효과를 위한 레거시 셰이더 문법입니다.

     > 물리 기반 스탠다드 셰이더를 사용하기 전, 유니티에서는 지원한 80개 이상의 빌트인 셰이더들 중 이전 버전과의 호환성을 위해 아직 사용되는 셰이더를 레거시 셰이더라고 합니다.

* 따라서 셰이더를 작성하는 방법도 3가지가 있습니다.

  1. Surface Shader
  2. Vertex & Fragment Shader
  3. Shader Lab

### Shader Lab

* 유니티에서 셰이더를 작성할 수 있는 자체언어입니다.
* 셰이더 구조를 조직화합니다.
* 어떤 타입으로 셰이더 코드를 작성하든, Shader Lab으로 감싸지게 됩니다.
* 기본적으로 `Properties`와 `SubShader`로 이루어져 있습니다.
* 다음은 Shader Lab을 간단히 표현한 코드입니다.

```C#
Shader "Custom/MyShader" { // [1] 셰이더의 경로입니다.
    Properties {
        // [2] 여러가지 셰이더의 속성들을 기술합니다.
        // 속성의 예시로는 텍스쳐, 색, 벡터 등이 있습니다.
        // Inspector 창에서 속성의 값을 조절할 수 있는 UI를 생성합니다.
    }
   	SubShader {
        // [3] 셰이더 코드의 핵심 부분입니다.
        // Surface, Vertex, Fragment, Fixted Function 셰이더 코드를 작성합니다.
    }
    SubShader {
        // [4] SubShader를 추가할 수 있씁니다.
        // 아래에 위치할수록 하위 버전의 디바이스를 위한 셰이더입니다.
        // 따라서 고성능 디바이스에서는 가장 상위의 셰이더가 우선 적용됩니다.
    }
    Fallback "Duffuse" // 추가한 SubShader들로 지원할 수 없다면 가장 낮은 품질로 처리합니다.
}
```

* 다음은 초기 Standard Surface Shader의 SubShader 코드입니다.

```C#
SubShader
{
    Tags { "RenderType"="Opaque" }
    LOD 200 // Level of Detail
            // 쉐이더의 지원 레벨을 조정한다.
        	// LOD를 기준으로 적용할 SubShader를 선택
            // 예) SubShader {LOD 200}, SubShader {LOD 100}이 있을 경우
            // maximumLOD를 지정하지 않을 경우
            // 디바이스가 지원하는 최대 레벨을 기준으로 SubShader가 적용된다.
            // (최대가 1000이라고 가정하면 SubShader {LOD 200} 구문 사용)
            // shader.maximumLOD = 100 처럼 셋팅할 경우 SubShader {LOD 100} 구문이 수행된다.
            // material.shader.maximumLOD = 100 처럼 쓸 수 있다.
            // Shader.globalMaximumLOD = 100 처럼 사용 시 모든 쉐이더를 세팅할 수 있다.
            // 유니티에 내장된 쉐이더들의 LOD는 아래와 같이 셋팅되어 있다.
            // VertexLit kind of shaders = 100
            // Decal, Reflective VertexLit = 150
            // Diffuse = 200
            // Diffuse Detail, Reflective Bumped Unlit, Reflective Bumped VertexLit = 250
            // Bumped, Specular = 300
            // Bumped Specular = 400
            // Parallax = 500
            // Parallax Specular = 600

    CGPROGRAM // 지금부터 CG코드를 시작

    // #pragma는 스니핏(snippet)으로 일종의 설정을 의미
    #pragma surface surf Standard fullforwardshadows // Surface 셰이더에서 어떤 기능을 사용하겠다고 지정
    #pragma target 3.0 // 셰이더 3.0 버전으로 컴파일

    sampler2D _MainTex; // 프로퍼티와 연동할 변수, 변수명이 같아야 ㅎ
	
    // 엔진으로부터 받아야 할 데이터들
    struct Input
    {
        float2 uv_MainTex;
    };

    half _Glossiness;  // half = float의 절반 크기 (float = 32bit, half = 16bit)
    half _Metallic;   
    fixed4 _Color;     // fixed4 = 11bit * 4

    UNITY_INSTANCING_BUFFER_START(Props)
    UNITY_INSTANCING_BUFFER_END(Props)
        
    void surf (Input IN, inout SurfaceOutputStandard o) // inout => C++의 참조자와 같은 역할
        												// Surface...Standard는 유니티에 내장된 구조체
        												/*  struct SurfaceOutputStandard
                                                            {
                                                                fixed3 Albedo;
                                                                fixed3 Normal;
                                                                fixed3 Emission;
                                                                half Metallic;
                                                                half Smoothness;
                                                                half Occlusion;
                                                                half Alpha;
                                                            } 
                                                           */
        												// 함수 내에서는 세미콜론(;)을 사용해야 함
    {
        fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
        o.Albedo = c.rgb;
        o.Metallic = _Metallic;
        o.Smoothness = _Glossiness;
        o.Alpha = c.a;
    }
    ENDCG // CG코드 종료
}
```
### Example - Surface Shader

1. 유니티 창에서 Assets > Create > Shader > Standard Surface Shader를 눌러서 셰이더 파일을 생성합니다.

2. 파일의 이름을 "ColorShader" 로 수정합니다.

   * 이 때, 실제 셰이더의 이름도 "Custom/ColorShader" 로 변경됩니다.

3. 직접 코드를 작성하기 위해 `Properties`와 `SubShader`의 내용을 전부 제거합니다.

4. `Properties`에 속성을 추가합니다.

```c#
   Properties
   {
   	_MyColor ("Green Color", Color) = (0, 1, 0, 1)
   }
```

   * 프로퍼티는 ***변수명 (프로퍼티 표시명, 프로퍼티 타입) = 초기값***  형식으로 작성할 수 있습니다.
   * 만약 프로퍼티 타입이 int여도 실제 inspector 창에선 실수를 입력할 수 있습니다.

> 프로퍼티의 타입은 다음과 같이 입력할 수 있습니다.
>
> 1. Float - 실수형
> 2. Range - Float와 같지만, 범위를 지정 가능
> 3. Int - 정수형
> 4. Color - Float 형이 4개가 결합된, RGBA 채널 값을 가질 수 있음
> 5. Vector - Float 형이 4개가 결합된, XYZW 좌표 값을 가질 수 있음
> 6. 2D - 2D 텍스쳐
> 7. Rect - 2D에 속하면서 2의 배수가 아닌 텍스쳐
> 8. 3D - 3D 텍스쳐

5. `SubShader`를 추가합니다.

   ```c#
   SubShader
   {
       Pass
       {
           Material
           {
               Diffuse[_MyColor]
           }	
           Lighting On
       }
   }

   ```

   * `Pass`는 물체를 그릴 때 GPU레 넘겨줄 작업을 기술하는 곳입니다.
   * `Pass`는 두 번 이상 기술할 수 있으며 이를 멀티 패스라 합니다.
     * 멀티 패스는 하나의 물체를 여러 번에 걸쳐서 그리게 됩니다.
   * `Material`은 물체의 재질을 결정합니다.
   * `Diffuse`는 물체의 고유 색상을 의미합니다. 
   * 빛을 추가하지 않으면 하얀색만 보이므로 `Lighting On`으로 빛을 추가합니다.

6. Materials 폴더에 Material 파일을 생성합니다. 이름을 ColorMaterial로 수정합니다.

7. ColorMaterial의 Insepctor 창에서 Shader > Custom > ColorShader 를 통해 작성한 셰이더를 적용시킬 수 있습니다.

8. ColorMaterial의 Inpector 창에서 Green Color라는 프로퍼티를 변경할 수 있습니다.



