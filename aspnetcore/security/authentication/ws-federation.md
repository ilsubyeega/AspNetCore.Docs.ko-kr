---
title: ASP.NET Core에서 WS-FEDERATION을 사용 하 여 사용자 인증
author: chlowell
description: 이 자습서에서는 ASP.NET Core 앱에서 WS-FEDERATION을 사용 하는 방법을 보여 줍니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651333"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>ASP.NET Core에서 WS-FEDERATION을 사용 하 여 사용자 인증

이 자습서에서는 사용자가 Active Directory Federation Services (ADFS) 또는 [Azure Active Directory](/azure/active-directory/) (AAD)와 같은 ws-federation 인증 공급자를 사용 하 여 로그인 할 수 있도록 하는 방법을 보여 줍니다. [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)에 설명 된 ASP.NET Core 2.0 샘플 앱을 사용 합니다.

ASP.NET Core 2.0 앱의 경우 [WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)에서 ws-federation 지원을 제공 합니다. 이 구성 요소는 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 에서 이식 되며이 구성 요소의 여러 가지 메커니즘을 공유 합니다. 그러나 구성 요소에는 몇 가지 중요 한 차이점이 있습니다.

기본적으로 새 미들웨어는 다음과 같습니다.

* 임의로 로그인을 허용 하지 않습니다. WS-FEDERATION 프로토콜의이 기능은 XSRF 공격에 취약 합니다. 그러나 `AllowUnsolicitedLogins` 옵션을 사용 하 여 활성화할 수 있습니다.
* 로그인 메시지의 모든 폼 게시를 확인 하지 않습니다. `CallbackPath`에 대 한 요청만 로그인에 대해 확인 됩니다. `CallbackPath` 기본값은 `/signin-wsfed` 이지만 상속 된 [Remoteauthenticationoptions.](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) 클래스의 callbackpath 속성을 통해 변경할 수 있습니다. 이 경로는 [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) 옵션을 사용 하도록 설정 하 여 다른 인증 공급자와 공유할 수 있습니다.

## <a name="register-the-app-with-active-directory"></a>Active Directory에 앱 등록

### <a name="active-directory-federation-services"></a>Active Directory Federation Services

* ADFS 관리 콘솔에서 서버의 **신뢰 당사자 트러스트 추가 마법사** 를 엽니다.

![신뢰 당사자 트러스트 추가 마법사: 시작](ws-federation/_static/AdfsAddTrust.png)

* 데이터를 수동으로 입력 하도록 선택:

![신뢰 당사자 트러스트 추가 마법사: 데이터 원본 선택](ws-federation/_static/AdfsSelectDataSource.png)

* 신뢰 당사자에 대 한 표시 이름을 입력 합니다. 이 이름은 ASP.NET Core 앱에 중요 하지 않습니다.

* [AspNetCore. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 는 토큰 암호화를 지원 하지 않으므로 토큰 암호화 인증서를 구성 하지 않습니다.

![신뢰 당사자 트러스트 추가 마법사: 인증서 구성](ws-federation/_static/AdfsConfigureCert.png)

* 앱의 URL을 사용 하 여 WS-FEDERATION 수동 프로토콜에 대 한 지원을 사용 하도록 설정 합니다. 앱에 대 한 포트가 올바른지 확인 합니다.

![신뢰 당사자 트러스트 추가 마법사: URL 구성](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> 이 URL은 HTTPS URL 이어야 합니다. IIS Express는 개발 중에 앱을 호스팅할 때 자체 서명 된 인증서를 제공할 수 있습니다. Kestrel에는 수동 인증서 구성이 필요 합니다. 자세한 내용은 [Kestrel 설명서](xref:fundamentals/servers/kestrel) 를 참조 하세요.

* 마법사의 나머지 부분에서 **다음** 을 클릭 하 고 끝에를 **닫습니다** .

* ASP.NET Core Id에는 **이름 id** 클레임이 필요 합니다. **클레임 규칙 편집** 대화 상자에서 하나를 추가 합니다.

![클레임 규칙 편집](ws-federation/_static/EditClaimRules.png)

* **변환 클레임 규칙 추가 마법사**에서 기본 **LDAP 특성을 클레임으로 보내기** 템플릿을 선택 된 채로 두고 **다음**을 클릭 합니다. **이름 ID** 발신 클레임에 **SAM-name** LDAP 특성을 매핑하는 규칙을 추가 합니다.

![변환 클레임 규칙 추가 마법사: 클레임 규칙 구성](ws-federation/_static/AddTransformClaimRule.png)

* **클레임 규칙 편집** 창에서 **마침** > **확인을** 클릭 합니다.

### <a name="azure-active-directory"></a>Azure Active Directory

* AAD 테 넌 트의 앱 등록 블레이드로 이동 합니다. **새 응용 프로그램 등록**을 클릭 합니다.

![Azure Active Directory: 앱 등록](ws-federation/_static/AadNewAppRegistration.png)

* 앱 등록에 대 한 이름을 입력 합니다. 이는 ASP.NET Core 앱에는 중요 하지 않습니다.
* 앱이 로그온 **url**로 수신 하는 url을 입력 합니다.

![Azure Active Directory: 앱 등록 만들기](ws-federation/_static/AadCreateAppRegistration.png)

* **끝점** 을 클릭 하 고 **페더레이션 메타 데이터 문서** URL을 확인 합니다. WS-FEDERATION 미들웨어의 `MetadataAddress`입니다.

![Azure Active Directory: 끝점](ws-federation/_static/AadFederationMetadataDocument.png)

* 새 앱 등록으로 이동 합니다. **설정** > **속성** 을 클릭 하 고 **앱 ID URI**를 적어 둡니다. WS-FEDERATION 미들웨어의 `Wtrealm`입니다.

![Azure Active Directory: 앱 등록 속성](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>ASP.NET Core Id 없이 WS-FEDERATION 사용

WS-FEDERATION 미들웨어는 Id 없이 사용할 수 있습니다. 다음은 그 예입니다.
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>ASP.NET Core Id의 외부 로그인 공급자로 WS-FEDERATION 추가

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 에 대 한 종속성을 프로젝트에 추가 합니다.
* `Startup.ConfigureServices`에 WS-FEDERATION을 추가 합니다.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>WS-FEDERATION을 사용 하 여 로그인

앱으로 이동 하 고 nav 헤더의 **로그인** 링크를 클릭 합니다. WsFederation: ![로그인 페이지를 사용 하 여 로그인 할 수 있는 옵션이 있습니다](ws-federation/_static/WsFederationButton.png)

ADFS를 공급자로 사용 하 여이 단추를 클릭 하면 adfs 로그인 페이지로 리디렉션됩니다. ![ADFS 로그인 페이지](ws-federation/_static/AdfsLoginPage.png)

Azure Active Directory 공급자 인 경우 단추는 AAD 로그인 페이지로 리디렉션됩니다. ![AAD 로그인 페이지](ws-federation/_static/AadSignIn.png)

새 사용자에 대 한 로그인이 성공 하면 앱의 사용자 등록 페이지로 리디렉션됩니다. ![등록 페이지](ws-federation/_static/Register.png)