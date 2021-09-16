# Example - 불 셰이더

* 불은 불규칙하게 아래서 위로 일렁이는 형태입니다.
* 따라서 불의 색을 가진 텍스쳐, 0와 1이 불규칙하게 섞인 텍스쳐, 시간 변수를 이용해서 표현이 가능합니다.

```c#
Tags { "RenderType"="Transparent" } // 알파값 허용 1
LOD 200

CGPROGRAM

#pragma surface surf Standard alph:blend // 알파값 허용 2
#pragma target 3.0
    
sample2D _MainTex1; // 불의 색을 가진 텍스쳐
sample2D _MainTex2; // 0과 1이 불규칙하게 섞인 텍스쳐

struct Input {
    float4 uv_MainTex1;
    float4 uv_MainTex2;
}

void surf(Input IN, SurfaceOutputStandard o) {
    float4 MainTex2 = tex2D(_MainTex2, float2(IN.uv_MainTex2.u, IN.UV_MainTex.V + _Time.y));
    float4 MainTex1 = tex2D(_MainTex1, IN.uv_MainTex1 + MainTex2.r*0.2); 	// 0.2를 곱하지 않으면
    																		// 일렁임이 지나치게 됨
    o.Emission = MainTex1.rgb;
    o.Alpha = MainTex1.a * MainTex2.a;
}
```

