---
title: "유니티 셰이더 파라미터 노출하기 2부 - 어트리뷰트(속성)"
categories: [Tutorial]
tags: [셰이더, 머티리얼, 유니티]
excerpt: "유니티에서 셰이더 파라미터를 사용할 수 있도록 외부로 노출하는 방법을 알아 봅니다."
toc: true
toc_sticky: true
katex: true
license: "CC BY-NC 4.0"
---
[1부](expose-shader-parameters-unity-part1)에서 프로퍼티로 셰이더에 입력 받는 것을 진행해 보았습니다.

유니티를 어느 정도 다루다 보면 다양한 셰이더를 보게 되고 종종 기능이 더 많아 보이는 셰이더 들도 접하기 마련입니다. 이번엔 각 프로퍼티를 좀 더 편하게 쓸 수 있게 해주는 *속성*에 대해서 알아 보겠습니다.

어트리뷰트는 프로퍼티의 앞에 쓰며 한 속성마다 `[`,`]`로 묶어 사용합니다. 여러개를 써서 중복으로 사용도 가능합니다. 유효하지 않은 곳에 붙이면 아무일도 일어나지 않습니다.

## `[Gamma]`

감마 역 보정을 해줍니다. 감마 보정에 대한 글은 따로 다루도록 하겠습니다. 일반적으로 렌더러 세팅에 따라 색상이나 강도가 의도한 대로 나오지 않을 때 사용합니다.

*실수 유형*과, *벡터 유형*에 사용할 수 있습니다.

- **예:**  
```hlsl
[Gamma] _GammaColor ("감마 적용된 색", Color) = (1.0, 1.0, 1.0, 1.0)
```

## `[HDR]`

색상이나 텍스처를 정해진 범위가 아닌 더 넓은 영역을 포함한 데이터를 셰이더에게 넘길 수 있게 됩니다.

`Color`의 경우 색상 피커가 *HDR피커*로 변경되며, 텍스처의 경우 *HDR텍스처가 아니라면 경고*를 띄웁니다.

- **예:**  
```hlsl
[HDR] _HDRColor ("HDR 색", Color) = (1.0, 1.0, 1.0, 1.0)
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-hdr.webp)

## `[HideInInspector]`

해당 프로퍼티를 가려 줍니다. 이렇게 가려진 프로퍼티의 용도는 때에 따라 꽤 유용합니다.

**스크립트에서 프로퍼티 제어**  
가장 많이 사용하는 방법입니다. 사용자가 제어하지 않고 엔진에서 직접 수치를 다뤄야 하는데, 굳이 프로퍼티를 꺼내 놓을 필요는 없기 때문입니다.

**머티리얼에 값만 저장**  
스크립트에서 제어하는 것은 같지만 셰이더에 직접적인 영향을 미치지 않는 일정 값만 필요할 때가 있는데, 해당 값을 머티리얼에만 저장해두고 사용자가 제어하지 못하게 할 때 유용합니다.

**기본값 지정 후 변경 금지**  
텍스처의 경우 `[HideInInspector]`를 붙여 프로퍼티를 숨겨도 유니티에서 셰이더 파일을 선택해 보면 해당 값이 드러나 있습니다. 이 곳에 꽂아 넣으면 보이진 않아도 해당 값을 기본 값으로 정할 수 있습니다.  
단, 다른 머티리얼에서 같은 이름을 가진 프로퍼티가 존재해서 해당 값이 변경 된다면 원하는 결과가 나오지 않는데다가, 오류를 찾기도 쉽지 않게 되어 추천하는 방법은 아닙니다.

- **예:**  
```hlsl
[HideInInspector] _HiddenTexture ("안 보임", 2D) = "white" {}
```

## `[MainTexture]`

스크립트에서 `Material.mainTexture`에 접근하면 프로퍼티에서 `_MainTex`텍스처 변수를 찾게 됩니다. `_MainTex`이름을 안쓰고 셰이더를 만들다가 종종 유니티가 `_MainTex`달라고 징징거리는 경우가 있습니다. 이럴때 해당 속성을 달거나 `_MainTex`를 만들어 주면 됩니다.

- **예:**  
```hlsl
[MainTexture] _BaseMap ("Albedo", 2D) = "white" { }
```

## `[MainColor]`

`[MainTexture]`와 비슷한 경우로 스크립트에서 `Material.color`에 접근하면 프로퍼티에서 `_Color`변수를 찾게 됩니다.

- **예:**  
```hlsl
[MainColor] _Tint ("Tint", Color) = (1.0, 1.0, 1.0, 1.0)
```

## `[NoScaleOffset]`

텍스처 프로퍼티 앞에 붙이면 타일링/오프셋 설정을 보지 않을 수 있습니다.

텍스처의 타일링과 오프셋을 허용하지 않고 싶을 때, 혹은 여러 텍스처가 한개의 타일링/오프셋을 사용하고 싶을 때 나머지 텍스처에서 설정을 가리고 싶을 때 사용합니다.

- **예:**  
```hlsl
[NoScaleOffset] _Texture2D ("텍스처", 2D) = "white" {}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-noscaleoffset.webp)

## `[Normal]`

텍스처 설정이 노말 프리셋으로 되어 있지 않다면 경고 및 고침 메세지를 표시 합니다.

- **예:**  
```hlsl
[Normal] _Bump ("Normal Map", 2D) = "bump" {}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-normal.webp)

## `[PerRendererData]`

종종 셰이더가 받아야 할 텍스처가 정해지지 않은 상태여야 할 때가 있습니다. 사용자가 직접 텍스처를 설정하지 않게 끔 셰이더를 만들 때 사용할 수 있습니다.

좋은 예로 UI에서는 다양한 텍스처를 받게 되는데 머티리얼에 미리 정해져 있진 않지만 작동할때 유니티가 직접 텍스처를 적용하게 됩니다.

- **예:**  
```hlsl
[PerRendererData] _Texture ("Per Render Data", 2D) = "white" {}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-perrenderdata.webp)

## `[Toggle]`, `[MaterialToggle]`

선택해야 하는 상태가 둘중 하나인 경우 유용합니다. `[Toggle]`과 `[MaterialToggle]`의 기능 차이는 없습니다. 주로 기능을 켜거나 꺼서 사용하지 않을 때 성능 상 이점이 생기는 경우 주로 사용합니다. 예를 들면 무거운 스펙큘러 셰이딩이나, 투명도를 켜고 끄는 식입니다.

이 기능은 켜진 상태와 꺼진 상태 두가지의 *분기*를 만들게 됩니다. 즉, 한 셰이더에서 기능이 꺼지고 켜지는 것이 아니라, 실제로는 꺼진 상태의 셰이더와 켜진 상태의 셰이더 두가지를 만들게 됩니다.

<!-- TODO: 베리언츠 문서 생성 시 링크 -->
이런 분기에 대해서 이해하기 위해서 알아야 하는 베리언츠와 키워드의 사용방법에 대해서는 다음에 다루도록 하겠습니다.

`[Toggle]`속성이 붙은 프로퍼티는 `Float` 유형을 사용하게 되며, `0.0`이면 꺼진 상태, `1.0`이면 켜진 상태로 메테리얼에 저장됩니다. 그러므로 기본값을 무엇으로 넣는 지에 따라 최초 상태를 결정 할 수 있습니다.

변수명을 모두 대문자로 변경 후 뒤에 `_ON`을 붙인 키워드가 생성됩니다. 이 키워드를 이용해 기능을 켜고 끄는 상태를 만들게 됩니다.

- **예:**  
```hlsl
Properties
{
    [Toggle] _Toggle ("Toggle", Float) = 0.0
    [MaterialToggle] _MaterialToggle ("Material Toggle", Float) = 1.0
}
SubShader
{
    Pass
    {
        CGPROGRAM
        #pragma multi_compile __ _TOGGLE_ON
        #pragma multi_compile __ _MATERIALTOGGLE_ON
        ...
        ENDCG
    }
}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-toggle.webp)

## `[Toggle()]`, `[MaterialToggle()]`

`[Toggle]`과 거의 같지만 `(`, `)`사이에 키워드를 직접 입력하여 사용할 수 있습니다.

- **예:**  
```hlsl
Properties
{
    [Toggle(IS_CUSTOM)] _Toggle ("Custom", Float) = 0.0
    [MaterialToggle(USE_FEATURE)] _MaterialToggle ("Use Feature", Float) = 1.0
}
SubShader
{
    Pass
    {
        CGPROGRAM
        #pragma multi_compile __ IS_CUSTOM
        #pragma multi_compile __ USE_FEATURE
        ...
        ENDCG
    }
}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-toggle-keyword.webp)

## `[ToggleOff]`

`[Toggle]`과 반대로 작동합니다. 토클에 체크하면 셰이더에선 해당 기능을 꺼버리는 식입니다. 그리고 키워드 또한 변수명 뒤에 `_OFF`로 붙게 됩니다.

- **예:**  
```hlsl
Properties
{
    [ToggleOff] _Feature ("Not Use Feature", Float) = 0.0
}
SubShader
{
    Pass
    {
        CGPROGRAM
        #pragma multi_compile __ _Feature_OFF
        ...
        ENDCG
    }
}
```

## `[ToggleOff()]`

`[Toggle()]`과 반대로 작동합니다. 머티리얼에서 체크하면 `(`, `)`사이의 키워드를 사용하지 않게 됩니다.

- **예:**  
```hlsl
Properties
{
    [ToggleOff(DISABLE_FEATURE)] _Feature ("Disable Feature", Float) = 0.0
}
SubShader
{
    Pass
    {
        CGPROGRAM
        #pragma multi_compile __ DISABLE_FEATURE
        ...
        ENDCG
    }
}
```

## `[KeywordEnum()]`

서로 중복되지 않을 기능을 드랍다운 목록에서 한가지를 정해 사용할 수 있습니다. 예를 들어 가벼운 셰이딩/무거운 셰이딩/끄기 와 같이 여러가지 선택지 중 한가지를 결정해야 하거나, R/G/B/A 채널중 하나를 선택해야 하는 경우에 사용합니다.

`()`안에 목록을 넣으면 `변수명_목록명`의 키워드로 작동 시킬 수 있게 됩니다. 각 목록명 순으로 *0 부터 번호를 매겨 머티리얼에 `Float`으로 저장*됩니다.

예를 들어 `[KeywordEnum(A,B,C)]`를 속성으로 주고 머티리얼에서 `A`를 선택하면`0.0`으로, `C`를 선택하면 `2.0`으로 저장하는 식입니다.

키워드는 변수명 뒤에 속성에 넣은 값을 모두 대문자로 변경해서 생성하게 됩니다.

- **예:**  
```hlsl
Properties
{
    [KeywordEnum(None, Light, Heavy)] _Feature ("Select Feature", Float) = 0.0
}
SubShader
{
    Pass
    {
        CGPROGRAM
        #pragma multi_compile _FEATURE_NONE _FEATURE_LIGHT _FEATURE_HEAVY
        ...
        ENDCG
    }
}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-keyword-enum.webp)

## `[Enum()]`

[`[KeywordEnum()]`](#keywordenum)과 비슷하지만, 각 목록별로 저장되는 숫자를 이용하게 됩니다.

`()`안에 `목록,숫자`를 쌍으로 입력하면 해당 숫자를 입력받아 사용할 수 있습니다. 예를 들어 `(One,1,Six,6)`라고 입력했을 경우 머티리얼에서 `One`을 선택하면 `1`을, `Six`를 선택하면 `6`을 받을 수 있습니다.

이렇게 선택한 숫자는 사실 셰이더 보다는 패스단계에서 셰이더를 설정하기 위해 사용하는 경우가 더 많습니다. 즉, 셰이더에서 일반적으로 숫자를 목록으로 놓고 하나만 써야 할 일이 없진 않겠으나 그다지 많지 않습니다.

그리고 이 속성의 강점은 유니티 엔진의 내부 열거형을 불러와 사용 할 수 있다는 점입니다.

- **예:**  
```hlsl
Properties
{
    [Enum(Native,1,Half,2,Quater,4)] _Resolution ("Select Resolution", Float) = 1
    // 일반적으로 셰이더에서 사용할만한 유니티 내장 열거형들
    [Enum(UnityEngine.Rendering.BlendOp)] _BlendOp ("BlendOp", Float) = 2
    [Enum(UnityEngine.Rendering.BlendMode)] _SrcBlend ("Blend mode Source", Float) = 0
    [Enum(UnityEngine.Rendering.BlendMode)] _DstBlend ("Blend mode Destination", Float) = 1
    [Enum(UnityEngine.Rendering.CullMode)] _Cull ("Cull mode", Float) = 2
}
SubShader
{
    BlendOp [_BlendOp]
    Blend [_SrcBlend] [_DstBlend]
    Cull [_Cull]
}
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-enum.webp)

## `[PowerSlider()]`

`Range()`를 사용하면 나오는 슬라이더가 비선형으로 작동해야 할 때 사용합니다. 예를 들면 감마 보정같이 보이는 것과 내부 계산이 다른 경우를 들 수 있습니다.

유형이 `Range()`인 경우에만 작동하는 속성입니다.

- **예:**  
```hlsl
[PowerSlider(2.2)] _Power ("Power", Range(0.001,1)) = 0.5
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-powerslider.webp)

## `[IntRange]`

범위 슬라이더의 값을 정수로 표현해 줍니다.

- **예:**  
```hlsl
[IntRange] _IntRange ("Count", Range(0,100)) = 50
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-intrange.webp)

## `[Header()]`

한 줄을 할당해 제목을 달 수 있습니다. 제목의 위쪽에 약간의 여백도 들어가 단락을 나눌 때 좋습니다.

`(`, `)`안에 내용을 쓰면 적용됩니다. 단 **`"`는 사용하지 않습니다**. 아쉽게도 한글은 지원을 하지 않습니다.

- **예:**  
```
[Header(Title)]
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-header.webp)

## `[Space]`, `[Space()]`

프로퍼티와 프로퍼티 사이에 여백을 줄 수 있습니다.

`Space`만 단독으로 사용하면 약간만 여백을 주고, 수치를 입력하면 수치 만큼 여백을 줍니다.

- **예:**  
```
[Toggle(IS_CUSTOM)] _Toggle ("Custom", Float) = 0.0
[Space(50)]
[MaterialToggle(USE_FEATURE)] _MaterialToggle ("Use Feature", Float) = 1.0
```
- **결과:**  
![](/assets/images/tutorial/expose-shader-parameters-unity-space.webp)

## 더 많은 방법

모든 프로퍼티, 모든 속성을 다 적게 되어 매우 긴 글이 되어 버렸습니다. 셰이더 프로퍼티를 이 이상으로 변화를 주려면 메테리얼에서 작동하는 스크립트를 작성해야 합니다. 만약 3부를 쓰게 된다면 스크립트와 함께 다뤄 보도록 하겠습니다.

## 참조

[MaterialPropertyDrawer](https://docs.unity3d.com/ScriptReference/MaterialPropertyDrawer.html)
