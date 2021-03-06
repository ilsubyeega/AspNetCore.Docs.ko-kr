---
title: ASP.NET Core SignalR Java 클라이언트
author: mikaelm12
description: ASP.NET Core SignalR Java 클라이언트를 사용 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 6919eabf454f16887e012161a454a4848c45002b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652215"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a>ASP.NET Core SignalR Java 클라이언트

만든 사람 [Mikael](https://twitter.com/MikaelM_12)

Java 클라이언트를 사용 하면 Android 앱을 포함 하 여 Java 코드에서 ASP.NET Core SignalR 서버에 연결할 수 있습니다. [JavaScript 클라이언트](xref:signalr/javascript-client) 및 [.net 클라이언트](xref:signalr/dotnet-client)와 마찬가지로 Java 클라이언트를 사용 하면 실시간으로 허브에 메시지를 보내고 받을 수 있습니다. Java 클라이언트는 ASP.NET Core 2.2 이상에서 사용할 수 있습니다.

이 문서에서 참조 되는 샘플 Java 콘솔 앱은 SignalR Java 클라이언트를 사용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-java-client-package"></a>SignalR Java 클라이언트 패키지를 설치 합니다.

*Signalr-1.0.0* JAR 파일을 사용 하면 클라이언트가 SignalR 허브에 연결할 수 있습니다. 최신 JAR 파일 버전 번호를 찾으려면 [Maven 검색 결과](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)를 참조 하세요.

Gradle를 사용 하는 경우 *Gradle* 파일의 `dependencies` 섹션에 다음 줄을 추가 합니다.

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven를 사용 하는 경우 *pom .xml* 파일의 `<dependencies>` 요소 내에 다음 줄을 추가 합니다.

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>허브에 연결하기

`HubConnection`을 설정 하려면 `HubConnectionBuilder`를 사용 해야 합니다. 연결을 빌드하는 동안 허브 URL 및 로그 수준을 구성할 수 있습니다. `build`하기 전에 `HubConnectionBuilder` 메서드를 호출 하 여 필요한 옵션을 구성 합니다. `start`와의 연결을 시작 합니다.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출하기

`send` 호출은 허브 메서드를 호출 합니다. 허브 메서드 이름 및 허브 메서드에 정의 된 모든 인수를 `send`전달 합니다.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> *서버 리스 모드*에서 Azure SignalR 서비스를 사용 하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 [SignalR 서비스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)를 참조 하세요.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출하기

`hubConnection.on`를 사용 하 여 허브에서 호출할 수 있는 클라이언트에서 메서드를 정의 합니다. 빌드 후와 연결을 시작 하기 전에 메서드를 정의 합니다.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>로깅 추가

SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다. 이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다. 다음 코드 조각에서는 SignalR Java 클라이언트와 함께 `java.util.logging`를 사용 하는 방법을 보여 줍니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이는 무시 해도 됩니다.

## <a name="android-development-notes"></a>Android 개발 정보

SignalR 클라이언트 기능에 대 한 Android SDK 호환성과 관련 하 여 대상 Android SDK 버전을 지정할 때 다음 항목을 고려 하십시오.

* SignalR Java 클라이언트는 Android API 수준 16 이상에서 실행 됩니다.
* Azure [SignalR 서비스](/azure/azure-signalr/signalr-overview) 에는 TLS 1.2이 필요 하 고 SHA-1 기반 암호 그룹은 지원 하지 않으므로 Azure SignalR 서비스를 통해 연결 하려면 Android API 수준 20 이상이 필요 합니다. Android는 API 수준 20에서 [SHA-256 이상 암호 그룹에 대 한 지원을 추가 했습니다](https://developer.android.com/reference/javax/net/ssl/SSLSocket) .

## <a name="configure-bearer-token-authentication"></a>전달자 토큰 인증 구성

SignalR Java 클라이언트에서는 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 "액세스 토큰 팩터리"를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다. [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)를 제공 합니다. [단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>알려진 제한 사항

::: moniker range=">= aspnetcore-3.0"

* JSON 프로토콜만 지원 됩니다.
* 전송 대체 (fallback) 및 전송 된 서버 전송 이벤트 전송은 지원 되지 않습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* JSON 프로토콜만 지원 됩니다.
* Websocket 전송만 지원 됩니다.
* 스트리밍은 아직 지원 되지 않습니다.

::: moniker-end

## <a name="additional-resources"></a>추가 리소스

* [Java API 참조](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
