---
title: "유니티 셰이더 파라미터 노출하기 1부"
categories: [Tutorial]
tags: [셰이더, 머티리얼, 유니티]
excerpt: "유니티에서 셰이더 파라미터를 사용할 수 있도록 외부로 노출하는 방법을 알아 봅니다."
toc: true
toc_sticky: true
use_zoom: true
license: "CC BY-NC 4.0"
---
한 프로그램이 셰이더의 종류가 너무 많다는 것은 최적화 측면에서 그다지 좋은 상황이 아닙니다. 게임처럼 실시간으로 작동하는 분야에서는 더 큰 문제가 될 수도 있지만 다양한 효과를 써야 멋진 아트를 보여주기 좋을 것입니다.

그래서 엔진들은 하나의 셰이더를 다양하게 써먹을 방법을 제시합니다. 엔진의 종류는 다양하지만, *기본적인 테크닉은 대부분 비슷*합니다.

'다른 엔진들은 다 되는데 내가 쓰는 엔진은 안돼?' 라는 이야기가 나오면 엔진 회사 입장에서도 좋을 게 없겠죠. 그래서 안정적이고 다들 좋다고 하는 것은 얼추 구현해 놓기 마련입니다. 다만 엔진들 각자의 개발 철학이 달라 접근법이 다르다고 보는 것이 더 맞지 않을까 합니다.

<!-- TODO: 베리언츠 문서 생성 시 링크 -->
**<i class="fa-solid fa-circle-info"></i> 정보**<br/>꼭 한 셰이더를 다양하게 써야 최적화가 되는 것은 아닙니다. 최적화란 것이 쓰는 상황에 따라 늘 달라지기 쉽상입니다. 한 셰이더를 다양하게 쓰기 위해 `if`문이나 베리언츠로 덩치가 마구 마구 커지기 시작하면 그 또한 감당 안 되는 상황이 올 수 있습니다. 셰이더가 너무 무거워져서 프레임이 안 나오거나, 베리언츠 개수가 너무 많아져서 프로그램이 감당하지 못하고 터져 버리기도 합니다.:dizzy_face:
{: .notice--info}

## 셰이더와 머티리얼 만들기

셰이더를 어떻게 돌아 가게 하는지도 중요하지만, 개발을 혼자 하는 것이 아니라면 본인이 만든 셰이더를 *다른 사람들이 잘 쓸 수 있게* 하는 것도 중요합니다. 유니티는 그런 면에서 꽤 많은 파라미터를 제공해 주고 있습니다. 유니티에서는 이 파라미터에서 설정한 값들을 머티리얼에 저장하고 각 항목을 프로퍼티(`Properties`)라로 부르고 있습니다. 시작은 간단히 셰이더를 만들어 보겠습니다.

유니티의 프로젝트 윈도에서 원하는 위치로 이동 후 오른 클릭 해서 **Create → Shader → Unlit Shader** 로 셰이더를 만듭니다.

![](/assets/images/tutorial/expose-shader-parameters-unity-new-shader.webp){: .zoom}

적당한 셰이더 파일 이름을 정해 주고, 만든 셰이더 파일을 오른 클릭 해서 **Create → Material** 로 해당 셰이더 파일을 사용하는 머티리얼을 만들어 보겠습니다.

![](/assets/images/tutorial/expose-shader-parameters-unity-new-material.webp){: .zoom .align-center}

**<i class="fa-solid fa-circle-info"></i> 정보**<br/>이렇게 머티리얼과 셰이더를 꼭 함께 만들 필요는 없습니다. 이름도 얼마든지 마음껏 만들어도 괜찮습니다. 셰이더와 머티리얼 각자 필요한 위치에 만들어도 무관합니다. 머티리얼이 셰이더와 연결되어 있지 않다면 머티리얼을 선택후 인스펙터에서 다음과 같이 셰이더를 선택하면 됩니다.<br/><br/>위 예제대로라면 **Unlit → NewUnlitShader**로 찾을 수 있습니다.<br/><br/>
![](/assets/images/tutorial/expose-shader-parameters-unity-shader-material.webp){: .align-center}
{: .notice--info}

## 셰이더 프로퍼티

만들어진 머티리얼을 선택해 보면 드디어 머티리얼 프로퍼티를 볼 수 있습니다.

![](/assets/images/tutorial/expose-shader-parameters-unity-material-properties.webp){: .align-center}

만들어진 셰이더 파일을 더블클릭해서 셰이더 파일을 수정해 보겠습니다.

**<i class="fa-solid fa-circle-info"></i> 정보**<br/>일반적으로 셰이더 파일을 수정하려고 더블클릭하면 대부분 VisualStudio가 실행될 텐데 좋은 에디터입니다만, 다른 에디터를 사용해도 전혀 무관합니다. `.shader`파일은 단순 텍스트 파일이며, 텍스트만 수정할 수 있다면 메모장으로 작업하셔도 좀 불편하긴 해도 작성자체는 가능합니다. 편한 에디터를 사용하세요!
{: .notice--info}

```hlsl
Shader "Unlit/NewUnlitShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader
    {
    ... 생략
    }
}
```

**<i class="fa-solid fa-circle-info"></i> 정보**<br/>유니티의 `.shader`파일에서 실제 셰이더 부분은 일반적으로 `CGPROGRAM`에서 부터 `ENDCG` 사이의 내용입니다. 혹은 사용하는 언어나 목적에 따라 `HLSLPROGRAM`...`ENDHLSL`이거나 `GLSLPROGRAM`...`ENDGLSL`일 수도 있습니다.<br/><br/>이 이야기는 저 영역외부의 내용들은 *셰이더가 아니라는 뜻*입니다. 저 영역의 바깥쪽은 [ShaderLab](https://docs.unity3d.com/kr/current/Manual/SL-Reference.html)이라고 부르는 유니티에서 셰이더를 관리하기 위한 스크립트입니다.
{: .notice--info}

### 셰이더 프로퍼티 문법

제일 첫 줄에 보이는 `Shader "이름" { ... }`은 유니티에서 셰이더라면 무조건 있어야 하는 부분입니다. `{`에서 `}`까지가 `"이름"`이 붙은 `Shader`라는 뜻으로 보면 되겠습니다.

이름은 C# 스크립트의 클래스 이름과 다르게 파일명과 통일시킬 필요가 없습니다. 큰따옴표`"`로 잘 묶어 주기만 한다면 한글도 잘 작동합니다.(잘 작동하긴 하지만 프로그래머들은 싫어할 수도 있습니다.:sweat_smile:)

그 아래에 있는 `Properties { ... }` 구간이 유니티에서 파라미터를 노출하는 부분입니다. 프로퍼티 구간 외에는 이번 튜토리얼에서 다루지 않을 테니 여기에선 따로 표시하지 않겠습니다.

이제 남은 부분만을 가지고 실제 보이는 머티리얼에 어떻게 적용되는지 보면 다음과 같습니다.

![](/assets/images/tutorial/expose-shader-parameters-unity-properties01.webp){: .zoom}

위 이미지를 보시면 각 부분이 유니티에서 어떻게 표현되고 있는지 아실 수 있을 것입니다. 좀 더 자세히 들여다보겠습니다.

![](/assets/images/tutorial/expose-shader-parameters-unity-properties02.svg){: .zoom}

괄호나 `=`, `,`같은 기호들은 자리를 나눠주는 역할을 합니다. 각 부분의 역할은 다음과 같습니다.
- **변수 이름**: 이 이름을 통해 셰이더 안에서 사용할 이름입니다. 그러므로 다른 변수들과 이름이 *중복되어서는 안 됩니다*.
- **프로퍼티 이름**: 유니티에 드러나는 이름입니다. 꽤 길게 써도 잘 나오지만, 일반적으로 읽기 편하게 이 파라미터가 무엇인지만 간결하게 씁니다.  
이름 중간에`/`로 구분 지으면 폴더와 같이 그룹 지어 정리할 수도 있습니다.
- **유형**: 이 프로퍼티가 어떤 유형인지 여기에 적은 대로 작동하게 됩니다.
- **기본값**: 최초 머티리얼이 만들어졌을 때 사용하는 값입니다. ***유형**에 따라 작성 방법이 다릅니다*.

**<i class="fa-solid fa-circle-info"></i> 정보**<br/>텍스트 에디터에서 셰이더를 수정하고 저장한 다음 유니티에 오면 자동으로 적용이 됩니다.<br/><br/>시험 삼아 **프로퍼티 이름**을 한번 수정해 보시면 바로바로 유니티에 반영되는 것을 보실 수 있습니다.<br/><br/>혹시 안된다면 어딘가 문법이 틀린 부분이 있을 수 있습니다. 대소문자 구분도 하니 주의 깊게 살펴봐야 합니다. 문제가 해결되지 않는다면 인터넷을 찾아보세요.<br/><br/>그래도 해결되지 않으면 여기 댓글을 한번 남겨 보시면 저의 미천한 실력으로나마 혹시 어쩌면 도움을 드릴 수도 있습니다.:wink:
{: .notice--info}

## 셰이더 프로퍼티 유형

유니티에서 셰이더가 받을 수 있는 파라미터는 크게 **숫자**, **벡터**, **텍스처** 세 가지입니다.

### 숫자

숫자란 말 그대로 숫자 하나를 의미합니다. 숫자 파라미터를 통해서 셰이더에 숫자 하나를 보낼 수 있습니다. 주로 속도, 밝기, 투명도, 세기 등 한 가지를 조절하면 되는 용도의 파라미터로 사용하게 됩니다. 이렇게 숫자가 하나만 있는 것을 **스칼라**(혹은 스케일러)라고 부릅니다.

각자의 용도에 따라 여러 가지 방법으로 설정할 수 있습니다.

#### `Integer`

[정수](https://ko.wikipedia.org/wiki/정수)를 입력할 수 있습니다. 유니티에서 숫자를 입력해 보면 소수점표현이 되지 않는 것을 볼 수 있습니다. 유니티에서 가장 최근에 생긴 프로퍼티 유형인데, 이전에 사용하던 `Int`유형은 이제 사용하지 않기를 권고하고 있습니다.

{% capture notice-integer %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Integer ("정수", Integer) = 1
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-integer.webp)
{% endcapture %}

<div class="notice">{{ notice-integer | markdownify }}</div>

#### `Float`

[실수](https://ko.wikipedia.org/wiki/실수)를 입력할 수 있습니다.

{% capture notice-float %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Float ("실수", Float) = 1.0
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-float.webp)
{% endcapture %}

<div class="notice">{{ notice-float | markdownify }}</div>

#### `Range`

실제 셰이더에서 받는 유형은 `Float`과 동일합니다. 하지만 최소, 최대 값을 정해서 범위를 벗어나지 않게 할 수 있습니다.

{% capture notice-range %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Range ("실수 범위", Range(-1.0, 1.0)) = 0.0
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-range.webp)
{% endcapture %}

<div class="notice">{{ notice-range | markdownify }}</div>

### 벡터

수학적으로 보면 복잡하지만 실상 컴퓨터 입장에서는 스칼라 네 개 묶음으로 봐도 무방합니다. 각자의 용도가 있는 숫자 네 개를 묶는 것이 아닌, 한가지 용도로 쓰인 네 개의 숫자를 표현할 때 사용합니다. 예를 들면, 3차원상의 xyz를 표현하기 위한 위치, 각도라던가, rgba를 가진 색상이 좋은 예입니다.

#### `Vector`

숫자 네 개를 직접 유저가 입력할 수 있습니다. 셰이더에게 숫자 네 개를 묶어 전달하게 됩니다.

{% capture notice-vector %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Vector ("벡터", Vector) = (1.0, 1.0, 1.0, 1.0)
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-vector.webp)
{% endcapture %}

<div class="notice">{{ notice-vector | markdownify }}</div>

#### `Color`

색도 따지고 보면 R,G,B,A의 네가지 숫자의 모음입니다. 좀 더 직관 적으로 컬러피커를 열어 색상을 선택할 수 있게 해줍니다.

{% capture notice-color %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Color ("색", Color) = (1.0, 1.0, 1.0, 1.0)
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-color.webp)
{% endcapture %}

<div class="notice">{{ notice-color | markdownify }}</div>

### 텍스처

다른 프로그램 언어에는 없는 셰이더 언어만의 유형입니다. 텍스처를 셰이더가 받아서 처리할 수 있게 됩니다.

다른 속성과 다르게 텍스처는 기본값의 뒤에 `{}`이 붙어 있는데 구버전의 잔재입니다. 이젠 사용하지 않지만, 굳이 문법에서는 그대로 두고 있습니다.

기본값에 색을 큰따옴표`"`안에 적는데, 이름에 해당하는 색을 가진 매우 작은 텍스처를 유니티가 내장해서 들고 있다가 주게 됩니다. 기본값은 다음과 같습니다.

| 기본 값 | 설명 |
|---|---|
| `"white" {}` | 흰색의 작은 텍스처를 기본값으로 사용합니다. |
| `"black" {}` | 검은색의 작은 텍스처를 기본값으로 사용합니다. | 
| `"gray" {}` | 중간 회색의 작은 텍스처를 기본값으로 사용합니다. |
| `"red" {}` | 빨간색의 작은 텍스처를 기본값으로 사용합니다. |
| `"bump" {}` | 기본 노말 맵으로 사용할 수 있는 작은 텍스처를 기본값으로 사용합니다. |

#### `2D`

일반적인 이미지를 받아 올 수 있습니다. 대부분의 경우 텍스처라고 하면 이것이라고 보면 됩니다.

{% capture notice-2d %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Texture2D ("2D 텍스처", 2D) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-2d-texture.webp)
{% endcapture %}

<div class="notice">{{ notice-2d | markdownify }}</div>

#### `3D`

가로와 세로로 픽셀이 있는 2D 이미지를 쌓아 놓았다고 생각하면 맞는 개념입니다. 용량이 해상도의 증가에 따라 기하급수적으로 늘어 나는 특징이 있어 일반적으로 볼류메트릭과 관련된 효과를 쓰는 경우가 아니라면 자주 보기 힘든 유형입니다. 언젠가 다룰 기회가 있다면 다뤄 보도록 하겠습니다.

{% capture notice-3d %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Texture3D ("3D 텍스처", 3D) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-3d-texture.webp)
{% endcapture %}

<div class="notice">{{ notice-3d | markdownify }}</div>

#### `Cube`

2D 이미지 여섯 장을 저장하고 있다가 정육면체의 각 면에 펼쳐 놓고 쓰는 방식으로 보면 됩니다. 2D 이미지 여섯 장을 한 번에 처리하게 되며, 360도 모든 곳을 표현하기에 좋은 형식입니다. 반사될 것이나, 360도 모든 곳을 표현해야 하는 스카이 박스 같은 경우 적절합니다.

{% capture notice-cube %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_TextureCube ("Cube 텍스처", Cube) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-cube-texture.webp)
{% endcapture %}

<div class="notice">{{ notice-cube | markdownify }}</div>

#### `2DArray`

3D 텍스처와 비슷해 보이지만 용도나 개념이 약간 다릅니다. 같은 크기면서 설정이 같은 여러 이미지를 1장 이상 묶어 두고 사용한다는 개념입니다. 지형 같은 것을 만들 때 여긴 흙밭, 여긴 풀밭 하는 식으로 다양한 재질을 표현할 때 묶음에서 하나씩 골라 표현하는 방법처럼 한곳에 다양한 표현을 할 때 주로 사용합니다.

{% capture notice-2darray %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_Texture2DArray ("2DArray 텍스처", 2DArray) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-2darray-texture.webp)
{% endcapture %}

<div class="notice">{{ notice-2darray | markdownify }}</div>

#### `CubeArray`

같은 크기면서 설정이 같은 큐브맵들을 1개 이상을 묶어 적절할 때 불러와 사용할 수 있도록 만들어 둔 것입니다. 장소에 따라 다른 큐브맵을 묶어서 셰이더에게 넘기고 해당 장소에 등록한 큐브맵을 불러오는 식으로 활용하기도 합니다.

{% capture notice-cubearray %}
**<i class="fa-solid fa-right-to-bracket"></i> 입력**

```hlsl
_TextureCubeArray ("Cube Array 텍스처", CubeArray) = "white" {}
```

**<i class="fa-solid fa-right-from-bracket"></i> 출력**

![](/assets/images/tutorial/expose-shader-parameters-unity-cubearray-texture.webp)
{% endcapture %}

<div class="notice">{{ notice-cubearray | markdownify }}</div>

## 참조

- [ShaderLab: 머티리얼 프로퍼티](https://docs.unity3d.com/kr/current/Manual/SL-Properties.html)
