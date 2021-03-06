---
title: ASP.NET Core Blazor 고급 시나리오
author: guardrex
description: 수동 RenderTreeBuilder 논리를 앱에 통합 하는 방법을 비롯 하 여 Blazor의 고급 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453183"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core Blazor 고급 시나리오

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="manual-rendertreebuilder-logic"></a>수동 RenderTreeBuilder 논리

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 구성 요소를 코드에서 C# 수동으로 빌드하는 등 구성 요소 및 요소를 조작 하는 메서드를 제공 합니다.

> [!NOTE]
> 구성 요소를 만드는 데 `RenderTreeBuilder`를 사용 하는 것은 고급 시나리오입니다. 형식이 잘못 된 구성 요소 (예: 닫히지 않은 태그 태그)는 정의 되지 않은 동작을 발생 시킬 수 있습니다.

다른 구성 요소에 수동으로 빌드할 수 있는 다음 `PetDetails` 구성 요소를 고려 합니다.

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

다음 예제에서 `CreateComponent` 메서드의 루프는 세 개의 `PetDetails` 구성 요소를 생성 합니다. `RenderTreeBuilder` 메서드를 호출 하 여 구성 요소를 만드는 경우 (`OpenComponent` 및 `AddAttribute`) 시퀀스 번호는 소스 코드 줄 번호입니다. Blazor 차이점 알고리즘은 고유 호출 호출이 아닌 개별 코드 줄에 해당 하는 시퀀스 번호를 사용 합니다. `RenderTreeBuilder` 메서드를 사용 하 여 구성 요소를 만드는 경우 시퀀스 번호에 대 한 인수를 하드 코딩 합니다. **계산 또는 카운터를 사용 하 여 시퀀스 번호를 생성 하면 성능이 저하 될 수 있습니다.** 자세한 내용은 [시퀀스 번호를 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) . 섹션을 참조 하세요.

`BuiltContent` 구성 요소:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> `Microsoft.AspNetCore.Components.RenderTree` 형식을 사용 하면 렌더링 작업의 *결과* 를 처리할 수 있습니다. Blazor framework 구현의 내부 세부 정보입니다. 이러한 형식은 *불안정* 한 것으로 간주 되며 이후 릴리스에서 변경 될 수 있습니다.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>시퀀스 번호는 코드 줄 번호와 관련 되 고 실행 순서와는 관련이 없습니다.

Razor 구성 요소 파일 (*razor*)은 항상 컴파일됩니다. 컴파일 단계는 런타임에 응용 프로그램 성능을 향상 시키는 정보를 삽입 하는 데 사용 될 수 있기 때문에 컴파일은 코드를 해석 하는 경우에 비해 잠재적 이점이 있습니다.

이러한 향상 된 기능을 포함 하는 주요 예로 *시퀀스 번호가*있습니다. 시퀀스 번호는 코드의 고유 하 고 정렬 된 줄에서 제공 되는 출력을 런타임에 표시 합니다. 런타임은이 정보를 사용 하 여 선형 시간에 효율적인 트리 차이을 생성 합니다 .이는 일반적으로 일반적인 트리 diff 알고리즘에 대해 가능한 것 보다 훨씬 빠릅니다.

다음 Razor 구성 요소 (*razor*) 파일을 살펴봅니다.

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

위의 코드는 다음과 같은 항목으로 컴파일됩니다.

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

코드가 처음 실행 될 때 `someFlag` `true`경우 작성기는 다음을 수신 합니다.

| 시퀀스 | Type      | data   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 처음  |
| 1        | 텍스트 노드 | 초 |

`someFlag` `false`되 고 태그가 다시 렌더링 된다고 가정 합니다. 이번에는 작성기가 다음을 받습니다.

| 시퀀스 | Type       | data   |
| :------: | ---------- | :----: |
| 1        | 텍스트 노드  | 초 |

런타임에서 diff를 수행 하는 경우 시퀀스 `0`의 항목이 제거 된 것으로 확인 되므로 다음과 같은 trivial *편집 스크립트*를 생성 합니다.

* 첫 번째 텍스트 노드를 제거 합니다.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>프로그래밍 방식으로 시퀀스 번호를 생성 하는 문제

대신 다음과 같은 렌더링 트리 작성기 논리를 작성 했다고 가정해 보겠습니다.

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

이제 첫 번째 출력은 다음과 같습니다.

| 시퀀스 | Type      | data   |
| :------: | --------- | :----: |
| 0        | 텍스트 노드 | 처음  |
| 1        | 텍스트 노드 | 초 |

이 결과는 이전 사례와 동일 하므로 부정적인 문제가 없습니다. `someFlag`은 두 번째 렌더링에서 `false` 되며 출력은 다음과 같습니다.

| 시퀀스 | Type      | data   |
| :------: | --------- | ------ |
| 0        | 텍스트 노드 | 초 |

이번에는 diff 알고리즘은 *두 가지* 변경이 발생 했음을 확인 하 고 알고리즘은 다음과 같은 편집 스크립트를 생성 합니다.

* 첫 번째 텍스트 노드의 값을 `Second`변경 합니다.
* 두 번째 텍스트 노드를 제거 합니다.

시퀀스 번호를 생성 하면 `if/else` 분기와 루프가 원래 코드에 표시 된 위치에 대 한 모든 유용한 정보가 손실 됩니다. 이로 인해 이전 처럼 비교가 **두 번** 수행 됩니다.

이는 간단한 예제입니다. 복잡 하 고 많이 중첩 된 구조와 특히 루프를 사용 하는 현실적인 사례에서 성능 비용은 일반적으로 높아집니다. 삽입 또는 제거 된 루프 블록 또는 분기를 즉시 식별 하는 대신, diff 알고리즘은 렌더링 트리를 깊이 있게 재귀적으로 실행 해야 합니다. 이로 인해 일반적으로 이전 구조와 새 구조가 서로 관련 되는 방식에 대해 diff 알고리즘이 misinformed 때문에 더 긴 편집 스크립트를 작성 해야 합니다.

### <a name="guidance-and-conclusions"></a>지침 및 결론

* 시퀀스 번호가 동적으로 생성 되 면 앱 성능이 저하 됩니다.
* 컴파일 시간에 캡처되는 경우를 제외 하 고 필요한 정보가 존재 하지 않기 때문에 프레임 워크는 런타임에 고유한 시퀀스 번호를 자동으로 만들 수 없습니다.
* 수동으로 구현 된 `RenderTreeBuilder` 논리의 긴 블록을 작성 하지 마세요. Razor 파일을 선호 하 고 컴파일러가 시퀀스 번호를 처리할 수 있도록 합니다 *.* 수동 `RenderTreeBuilder` 논리를 방지할 수 없는 경우 긴 코드 블록을 `OpenRegion`/`CloseRegion` 호출에 래핑된 작은 조각으로 분할 합니다. 각 영역에는 고유한 시퀀스 번호 공간이 있으므로 각 지역 내에서 0 (또는 다른 임의의 임의의 숫자)으로 다시 시작할 수 있습니다.
* 시퀀스 번호가 하드 코딩 된 경우 diff 알고리즘에서는 값이 증가 하는 시퀀스 번호만 필요 합니다. 초기 값과 간격은 관련이 없습니다. 한 가지 합법적인 옵션은 코드 줄 번호를 시퀀스 번호로 사용 하거나 0부터 시작 하 여 또는 수백 (또는 선호 하는 간격) 만큼 증가 하는 것입니다. 
* Blazor는 시퀀스 번호를 사용 하는 반면 다른 트리 diff UI 프레임 워크는이를 사용 하지 않습니다. Diff는 시퀀스 번호를 사용 하는 경우 훨씬 더 빠르며, Blazor은 *razor* 파일을 작성 하는 개발자를 위해 자동으로 시퀀스 번호를 처리 하는 컴파일 단계를 활용 합니다.
