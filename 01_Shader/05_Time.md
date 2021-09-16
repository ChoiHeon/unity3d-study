# Time

* 자동으로 인클루드되는 **UnityShaderVariables.cginc** 에 선언된 빌트인 셰이더 변수입니다.

  * 해당 파일에는 시간 외에도 다양한 변수들이 선언되어 있습니다.

* 시간 변수는 총 4개가 선언되어 있습니다.

  | Name            | Type   | Value                                          |
  | --------------- | ------ | ---------------------------------------------- |
  | _Time           | float4 | time since level load, (t/20, t, t\*2, t\*3)   |
  | _SinTime        | float4 | sine of time, (t/8, t/4, t/2, t)               |
  | _CosTime        | float4 | cosine of time, (t/8, t/4, t/2, t)             |
  | unity_DeltaTime | float4 | delta time, (dt, 1/dt, smooth_dt, 1/smooth_dt) |

  * 각각 `x, y, z, w`로 접근할 수 있습니다.

* 이를 이용해서 애니메이션처럼 동작하도록 만들 수 있습니다.

  ```c#
  // 생략
  {
      float4 MainTex = tex2D(_MainTex, float2(IN.uv_MainTex.u + _Time.y, IN.uv_MainTex.v);
  }
  ```

