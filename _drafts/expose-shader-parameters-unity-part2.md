---
title: "유니티 셰이더 파라미터 노출하기 2부"
categories: [Blog]
tags: [셰이더, 머티리얼, 유니티]
excerpt: "유니티에서 셰이더 파라미터를 사용할 수 있도록 외부로 노출하는 방법을 알아 봅니다."
# header:
#   overlay_image: /assets/images/blog/what-is-shader.webp
#   overlay_filter: 0.7
toc: true
toc_sticky: true
katex: true
license: "CC BY-NC 4.0"
---
[1부](expose-shader-parameters-unity-part1)에서 프로퍼티로 셰이더에 입력 받는 것을 진행해 보았어도 성에 차지 않는 분도 계실 것입니다.

유니티를 어느 정도 다루다 보면 다양한 셰이더를 보게 되고 종종 기능이 더 많아 보이는 셰이더 들도 접하기 마련입니다. 이번엔 각 프로퍼티를 좀 더 편하게 쓸 수 있게 해주는 *속성*에 대해서 알아 보겠습니다.

## 어트리뷰트(속성)

프로퍼티의 앞에 쓰며 한 속성마다 `[`,`]`로 묶어 사용합니다. 여러개를 써서 중복으로 사용도 가능합니다. 유효하지 않은 곳에 붙이면 아무일도 일어나지 않습니다.

#### `[Gamma]`

감마 역 보정을 해줍니다. 감마 보정에 대한 글은 따로 다루도록 하겠습니다. 일반적으로 렌더러 세팅에 따라 색상이나 강도가 의도한 대로 나오지 않을 때 사용합니다.

*실수 유형*과, *벡터 유형*에 사용할 수 있습니다.

{% capture notice-gamma %}
**<i class="fa-solid fa-right-to-bracket"></i> 예제**

```hlsl
[Gamma] _GammaColor ("감마 적용된 색", Color) = (1.0, 1.0, 1.0, 1.0)
```
{% endcapture %}

<div class="notice">{{ notice-gamma | markdownify }}</div>

#### `[HDR]`

색상이나 텍스처를 정해진 범위가 아닌 더 넓은 영역을 포함한 데이터를 셰이더에게 넘길 수 있게 됩니다.

`Color`의 경우 색상 피커가 *HDR피커*로 변경되며, 텍스처의 경우 *HDR텍스처가 아니라면 경고*를 띄웁니다.

{% capture notice-hdr %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
[HDR] _HDRColor ("HDR 색", Color) = (1.0, 1.0, 1.0, 1.0)
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/blog/expose-shader-parameters-unity-hdr.webp)
{% endcapture %}

<div class="notice">{{ notice-hdr | markdownify }}</div>

#### `[HideInInspector]`

해당 프로퍼티를 가려 줍니다. 이렇게 가려진 프로퍼티의 용도는 때에 따라 꽤 유용합니다.

- **스크립트에서 프로퍼티 제어**  
  가장 많이 사용하는 방법입니다. 사용자가 제어하지 않고 엔진에서 직접 수치를 다뤄야 하는데, 굳이 프로퍼티를 꺼내 놓을 필요는 없기 때문입니다.
- **머티리얼에 값만 저장**  
  스크립트에서 제어하는 것은 같지만 셰이더에 직접적인 영향을 미치지 않는 일정 값만 필요할 때가 있는데, 해당 값을 머티리얼에만 저장해두고 사용자가 제어하지 못하게 할 때 유용합니다.
- **기본값 지정 후 변경 금지**  
  텍스처의 경우 `[HideInInspector]`를 붙여 프로퍼티를 숨겨도 유니티에서 셰이더 파일을 선택해 보면 해당 값이 드러나 있습니다. 이 곳에 꽂아 넣으면 보이진 않아도 해당 값을 기본 값으로 정할 수 있습니다.  
  단, 다른 머티리얼에서 같은 이름을 가진 프로퍼티가 존재해서 해당 값이 변경 된다면 원하는 결과가 나오지 않는데다가, 오류를 찾기도 쉽지 않게 되어 추천하는 방법은 아닙니다.

{% capture notice-hide %}
**<i class="fa-solid fa-right-to-bracket"></i> 예제**

```hlsl
[HideInInspector] _HiddenTexture ("안 보임", 2D) = "white" {}
```
{% endcapture %}

<div class="notice">{{ notice-hide | markdownify }}</div>

#### `[MainTexture]`

스크립트에서 `Material.mainTexture`에 접근하면 프로퍼티에서 `_MainTex`텍스처 변수를 찾게 됩니다. `_MainTex`이름을 안쓰고 셰이더를 만들다가 종종 유니티가 `_MainTex`달라고 징징거리는 경우가 있습니다. 이럴때 해당 속성을 달거나 `_MainTex`를 만들어 주면 됩니다.

{% capture notice-maintexture %}
**<i class="fa-solid fa-right-to-bracket"></i> 예제**

```hlsl
[MainTexture] _BaseMap ("Albedo", 2D) = "white" { }
```
{% endcapture %}

<div class="notice">{{ notice-maintexture | markdownify }}</div>

#### `[MainColor]`

`[MainTexture]`와 비슷한 경우로 스크립트에서 `Material.color`에 접근하면 프로퍼티에서 `_Color`변수를 찾게 됩니다.

{% capture notice-maincolor %}
**<i class="fa-solid fa-right-to-bracket"></i> 예제**

```hlsl
[MainColor] _Tint ("Tint", Color) = (1.0, 1.0, 1.0, 1.0)
```
{% endcapture %}

<div class="notice">{{ notice-maincolor | markdownify }}</div>

#### `[NoScaleOffset]`

텍스처 프로퍼티 앞에 붙이면 타일링/오프셋 설정을 보지 않을 수 있습니다.

텍스처의 타일링과 오프셋을 허용하지 않고 싶을 때, 혹은 여러 텍스처가 한개의 타일링/오프셋을 사용하고 싶을 때 나머지 텍스처에서 설정을 가리고 싶을 때 사용합니다.

{% capture notice-noscaleoffset %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
[NoScaleOffset] _Texture2D ("텍스처", 2D) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/blog/expose-shader-parameters-unity-noscaleoffset.webp)
{% endcapture %}

<div class="notice">{{ notice-noscaleoffset | markdownify }}</div>

#### `[Normal]`

텍스처 설정이 노말 프리셋으로 되어 있지 않다면 경고 및 고침 메세지를 표시 합니다.

{% capture notice-normal %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
[Normal] _Bump ("Normal Map", 2D) = "bump" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/blog/expose-shader-parameters-unity-normal.webp)
{% endcapture %}

<div class="notice">{{ notice-normal | markdownify }}</div>

#### `[PerRendererData]`

종종 셰이더가 받아야 할 텍스처가 정해지지 않은 상태여야 할 때가 있습니다. 사용자가 직접 텍스처를 설정하지 않게 끔 셰이더를 만들 때 사용할 수 있습니다.

좋은 예로 UI에서는 다양한 여러장의 텍스처를 받게 되는데 머티리얼에 정해져 있진 않지만 작동할때 유니티가 직접 텍스처를 꽂아 사용하게 됩니다.

{% capture notice-perrenderdata %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
[PerRendererData] _Texture ("Per Render Data", 2D) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/blog/expose-shader-parameters-unity-perrenderdata.webp)
{% endcapture %}

<div class="notice">{{ notice-perrenderdata | markdownify }}</div>

#### `[Toggle]`, `[MaterialToggle]`, `[Toggle()]`, `[ToggleOff()]`

선택해야 하는 상태가 둘중 하나인 경우 유용합니다. 주로 기능을 켜거나 꺼서 사용하지 않을 때 성능 상 이점이 생기는 경우 주로 사용합니다. 예를 들면 무거운 스펙큘러 셰이딩이나, 투명도를 켜고 끄는 식입니다.

<!-- TODO: 베리언츠 문서 생성 시 링크 -->
이 속성들을 잘 사용하기 위해서 알아야 하는 베리언츠와 키워드의 사용방법에 대해서는 다음에 다루도록 하겠습니다.

**<i class="fa-solid fa-circle-info"></i> 정보**<br/>현재 설명 중인 `[Toggle]`속성들과 함께, 바로 다음에 설명 할 [`[KeywordEnum()]`](#keywordenum)은 셰이더 베리언츠를 생성합니다. 주로 한 기능을 선택해서 사용할지 말지를 결정하게 되는데 모든 경우의 수 만큼 셰이더를 생성해 놓아야 합니다.<br/><br/>한개의 기능만 끄고 켠다면 기능 부분만 다르게 만들고 나머지는 같은 셰이더를 만들어서 사용하는 것도 방법이지만, 한 셰이더에서 기능을 켜고 끄는 것이 아티스트에게는 훨씬 편해서 사용하게 됩니다. 너무 남용하는 것도 좋지 않지만 너무 경직된 구조는 개발을 즐겁지 못하게 만들 수도 있습니다!
{: .notice--info}

#### `[KeywordEnum()]`

서로 중복되지 않을 기능을 드랍다운 목록에서 한가지를 정해 사용할 수 있습니다. 예를 들어 가벼운 셰이딩/무거운 셰이딩/끄기 와 같이 셋 중 한가지를 결정해야 하거나, R/G/B/A 채널중 하나를 선택해야 하는 경우에 사용합니다.

`()`안에 목록을 넣으면 `변수명_목록명`의 키워드로 작동 시킬 수 있게 됩니다. 각 목록명 순으로 *0 부터 번호를 매겨 머티리얼에 `Float`으로 저장*됩니다.

예를 들어 `[KeywordEnum(A,B,C)]`를 속성으로 주고 머티리얼에서 `A`를 선택하면`0.0`으로, `C`를 선택하면 `2.0`으로 저장하는 식입니다.

#### `[Enum()]`

[`[KeywordEnum()]`](#keywordenum)과 비슷하지만, 각 목록별로 저장되는 숫자를 이용하게 됩니다.

`()`안에 `목록,숫자`를 쌍으로 입력하면 해당 숫자를 입력받아 사용할 수 있습니다. 예를 들어 `(One,1,Six,6)`라고 입력했을 경우 머티리얼에서 `One`을 선택하면 `1`을, `Six`를 선택하면 `6`을 받을 수 있습니다.

하지만 일반적으로 숫자를 목록으로 놓고 하나만 써야 할 일이 없진 않겠지만 그다지 많지 않습니다. 이 속성의 강점은 유니티 엔진의 내부 열거형을 불러와 사용 할 수 있다는 점입니다.

<!-- TODO: 엔진 열거형 예제 -->

#### `[PowerSlider()]`

`()`안에 숫자를 넣어 입력받은 수를 몇승 할 것인지 결정합니다. 예를 들어 `[PowerSlider(4.0)]`속성을 붙인 변수명이 $$x$$라는 프로퍼티라면 셰이더에서 받는 값은 $$x^4$$가 됩니다.

#### `[IntRange]`

범위 슬라이더의 값을 정수로 표현해 줍니다.

#### `[Header()]`

제목을 따로 달 수 있게 해줍니다.

#### `[Space]`, `[Space()]`

프로퍼티와 프로퍼티 사이에 여백을 줄 수 있습니다. 수치를 입력하면 수치 만큼 여백을 줍니다.

## 더 많은 방법

모든 프로퍼티, 모든 속성을 다 적게 되어 매우 긴 글이 되어 버렸습니다.

## 참조
