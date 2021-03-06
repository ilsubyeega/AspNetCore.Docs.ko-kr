---
title: ASP.NET Core의 용도 계층 구조 및 다중 테넌트
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api와 관련 된 용도 문자열 계층 구조 및 다중 테 넌 트에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 1133d40e7b325d58b3f70e7387494dae36ff8ac9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654021"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>ASP.NET Core의 용도 계층 구조 및 다중 테넌트

`IDataProtector`도 암시적 `IDataProtectionProvider`이므로 용도를 함께 연결할 수 있습니다. 이 의미에서 `provider.CreateProtector([ "purpose1", "purpose2" ])`은 `provider.CreateProtector("purpose1").CreateProtector("purpose2")`와 동일 합니다.

그리고 이런 특징을 이용해서 데이터 보호 시스템 내부에 흥미로운 계층 관계를 구성할 수 있습니다. 이전 예의 [contoso.com](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)메시지에서 securemessage 구성 요소는 `provider.CreateProtector("Contoso.Messaging.SecureMessage")`를 한 번 이상 호출 하 고 그 결과를 개인 `_myProvider` 필드에 캐시할 수 있습니다. 그런 다음 `_myProvider.CreateProtector("User: username")`에 대 한 호출을 통해 향후 보호기를 만들 수 있으며, 이러한 보호기는 개별 메시지를 보호 하는 데 사용 됩니다.

이 관계를 반대로 뒤집어 볼 수도 있습니다. 다중 테넌트를 호스트하는 단일 논리 응용 프로그램이 존재하고 (CMS 등의), 각 테넌트가 자체적으로 인증 및 상태 관리 시스템을 구성할 수 있다고 가정해보겠습니다. 파라솔 응용 프로그램은 단일 마스터 공급자를 포함 하 고 `provider.CreateProtector("Tenant 1")` 및 `provider.CreateProtector("Tenant 2")`를 호출 하 여 각 테 넌 트에 데이터 보호 시스템의 고유한 격리 조각을 제공 합니다. 그러면 테넌트는 필요에 따라 자신만의 개별적인 보호자를 파생해서 생성할 수는 있지만, 그 어떤 방법을 쓰더라도 시스템 내부의 다른 테넌트와 충돌하는 보호자를 생성할 수는 없습니다. 이를 도식으로 표현하면 다음과 같습니다.

![다중 테넌트 용도](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> 여기서는 최상위 응용 프로그램이 각각의 테넌트가 사용할 수 있는 API를 제어할 수 있고, 각 테넌트는 서버에서 임의의 코드를 실행할 수 없다고 가정합니다. 만약 테넌트가 임의의 코드를 실행할 수 있다면 내부적으로 리플렉션을 수행해서 격리 보장을 깨뜨리거나 마스터 키 관련 자료를 직접 읽어서 원하는 하위 키를 파생할 수 있습니다.

데이터 보호 시스템은 기본으로 제공되는 구성 하에서 사실상 일종의 다중 테넌트를 사용합니다. 기본적으로 마스터 키 관련 자료는 작업자 프로세스 계정의 사용자 프로필 폴더에, 또는 IIS 응용 프로그램 풀 신원의 경우 레지스트리에 저장됩니다. 그러나 실제로는 단일 계정으로 여러 개의 응용 프로그램들을 실행하는 것이 일반적이므로 결과적으로 해당하는 모든 응용 프로그램들이 마스터 키 관련 자료를 공유하게 됩니다. 이런 문제점을 해결하기 위해서 데이터 보호 시스템은 응용 프로그램별로 고유한 식별자를 전체 용도 체인의 첫 번째 요소로 자동으로 삽입합니다. 이 암시적 용도는 각 응용 프로그램을 시스템 내의 고유 테 넌 트로 효율적으로 처리 하 여 [개별 응용 프로그램](xref:security/data-protection/configuration/overview#per-application-isolation) 을 서로 격리 하는 데 사용 되며, 보호기 생성 프로세스는 위의 이미지와 동일 합니다.
