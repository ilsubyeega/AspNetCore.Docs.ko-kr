---
title: ASP.NET Core의 해시 암호
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 암호를 해시 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651435"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core의 해시 암호

데이터 보호 코드 베이스에는 암호화 키 파생 함수를 포함 하는 *AspNetCore* 패키지가 포함 되어 있습니다. 이 패키지는 독립적인 구성 요소로서 데이터 보호 시스템의 나머지 다른 부분에 의존하지 않습니다. 이 패키지는 완벽히 독립적으로 사용이 가능합니다. 다만 편의상 소스가 데이터 보호 코드 베이스와 함께 위치해 있을 뿐입니다.

패키지는 현재 [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)을 사용 하 여 암호를 해시할 수 있는 메서드 `KeyDerivation.Pbkdf2` 제공 합니다. 이 API는 .NET Framework의 기존 [Rfc2898DeriveBytes 형식과](/dotnet/api/system.security.cryptography.rfc2898derivebytes)매우 유사 하지만 세 가지 중요 한 차이점이 있습니다.

1. `KeyDerivation.Pbkdf2` 메서드는 여러 PRFs (현재 `HMACSHA1`, `HMACSHA256`및 `HMACSHA512`) 사용을 지원 하지만 `Rfc2898DeriveBytes` 형식은 `HMACSHA1`만 지원 합니다.

2. `KeyDerivation.Pbkdf2` 메서드는 현재 운영 체제를 검색 하 고 가장 최적화 된 루틴 구현을 선택 하려고 시도 하 여 특정 경우에 훨씬 더 나은 성능을 제공 합니다. Windows 8에서는 `Rfc2898DeriveBytes`의 처리량을 10 배 하는 것을 제공 합니다.

3. `KeyDerivation.Pbkdf2` 메서드를 사용 하려면 호출자가 모든 매개 변수 (솔트, PRF 및 반복 횟수)를 지정 해야 합니다. `Rfc2898DeriveBytes` 형식은 이러한 값에 대 한 기본값을 제공 합니다.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

실제 사용 사례는 ASP.NET Core Id의 `PasswordHasher` 형식에 대 한 [소스 코드](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) 를 참조 하세요.
