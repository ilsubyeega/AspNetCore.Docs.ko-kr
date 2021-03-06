---
title: ASP.NET Core 프로젝트의 스 캐 폴드 Id
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id를 스 캐 폴드 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: b3e077aeac11e62d9e992884100476f7be35b59a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653715"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core 프로젝트의 스 캐 폴드 Id

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다. Id를 포함 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 RCL (Identity Razor 클래스 라이브러리)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다. 코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다. 예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다. 생성된 코드는 ID RCL의 동일한 코드보다 우선합니다. 기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.

인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 Rcl id 패키지를 추가할 수 있습니다. 선택한 ID 코드의 옵션이 생성되어야 합니다.

스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다. 이 문서에서는 Id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.

파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다. Id 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다.

서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 id를 사용 하는 기타 보안 기능을 사용 하는 경우에 필요 합니다. Id를 스 캐 폴딩을 사용할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다. 이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다. 예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.

이 문서에는 스 캐 폴더를 실행할 때 생성 되는 *ScaffoldingReadme* 파일 보다 전체 지침이 포함 되어 있습니다.

## <a name="scaffold-identity-into-an-empty-project"></a>빈 프로젝트에 id 스 캐 폴드

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

다음과 유사한 코드를 사용 하 여 `Startup` 클래스를 업데이트 합니다.

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>마이그레이션, UseAuthentication 및 레이아웃

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>인증 사용

다음과 유사한 코드를 사용 하 여 `Startup` 클래스를 업데이트 합니다.

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>레이아웃 변경

선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>권한 부여가 있는 Razor 프로젝트로 id 스 캐 폴드

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
일부 Id 옵션은 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

선택 사항: *Views/Shared/_Layout* 파일에 로그인 부분 (`_LoginPartial`)을 추가 합니다.

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*

Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다. 자세한 내용은 IHostingStartup를 참조 하세요.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

다음과 유사한 코드를 사용 하 여 `Startup` 클래스를 업데이트 합니다.

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>전체 id UI 원본 만들기

Id UI에 대 한 모든 권한을 유지 하려면 Id 스 캐 폴더를 실행 하 고 **모든 파일 재정의**를 선택 합니다.

다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱에서 기본 Id UI를 Id로 바꾸는 변경 내용을 보여 줍니다. 이 작업을 수행 하 여 Id UI에 대 한 모든 권한을 부여할 수 있습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

기본 Id는 다음 코드에서 바뀝니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>등록 페이지 사용 안 함

사용자 등록을 사용 하지 않도록 설정 하려면:

* 스 캐 폴드 Id입니다. Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다. 다음은 그 예입니다.

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 사용자가이 끝점에서 등록할 수 없도록 *영역/i d/페이지/계정/등록을 업데이트 합니다* .

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 이전 변경 내용과 일치 하도록 *영역/i d/페이지/계정*

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *영역/i d/페이지/계정/로그인* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* *영역/i d/페이지/계정/* a p 확인 페이지를 업데이트 합니다.

  * Cshtml 파일에서 코드 및 링크를 제거 합니다.
  * `PageModel`에서 확인 코드를 제거 합니다.

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>다른 앱을 사용 하 여 사용자 추가

웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다. 사용자를 추가 하는 옵션은 다음과 같습니다.

* 전용 관리 웹 앱입니다.
* 콘솔 앱입니다.

다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.

* 사용자 목록을 메모리로 읽습니다.
* 각 사용자에 대해 강력한 고유 암호가 생성 됩니다.
* 사용자가 Id 데이터베이스에 추가 됩니다.
* 사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.

## <a name="prevent-publish-of-static-identity-assets"></a>정적 Id 자산 게시 방지

정적 ID 자산을 웹 루트에 게시하지 않으려면 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core 2.1 이상으로 인증 코드 변경](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 이상에서는 [Razor 클래스 라이브러리로](xref:razor-pages/ui-class) [ASP.NET Core id](xref:security/authentication/identity) 를 제공 합니다. Id를 포함 하는 응용 프로그램은 스 캐 폴더를 적용 하 여 RCL (Identity Razor 클래스 라이브러리)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다. 코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다. 예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다. 생성된 코드는 ID RCL의 동일한 코드보다 우선합니다. 기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.

인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 Rcl id 패키지를 추가할 수 있습니다. 선택한 ID 코드의 옵션이 생성되어야 합니다.

스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다. 이 문서에서는 Id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.

Id 스 캐 폴더를 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme* 파일이 만들어집니다. *ScaffoldingReadme* 파일에는 id 스 캐 폴딩 업데이트를 완료 하는 데 필요한 사항에 대 한 일반적인 지침이 포함 되어 있습니다. 이 문서에는 *ScaffoldingReadme* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.

파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다. Id 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다.

> [!NOTE]
> 서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 id를 사용 하는 기타 보안 기능을 사용 하는 경우에 필요 합니다. Id를 스 캐 폴딩을 사용할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다. 이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다. 예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.

## <a name="scaffold-identity-into-an-empty-project"></a>빈 프로젝트에 id 스 캐 폴드

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

`Startup` 클래스에 다음의 강조 표시 된 호출을 추가 합니다.

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>기존 권한 부여 없이 Razor 프로젝트에 id 스 캐 폴드

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>마이그레이션, UseAuthentication 및 레이아웃

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>인증 사용

`Startup` 클래스의 `Configure` 메서드에서 `UseStaticFiles`후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다.

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>레이아웃 변경

선택 사항: 로그인 부분 (`_LoginPartial`)을 레이아웃 파일에 추가 합니다.

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>권한 부여가 있는 Razor 프로젝트로 id 스 캐 폴드

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
일부 Id 옵션은 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>기존 권한 부여 없이 MVC 프로젝트에 id 스 캐 폴드

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

선택 사항: *Views/Shared/_Layout* 파일에 로그인 부분 (`_LoginPartial`)을 추가 합니다.

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*

Id는 *Areas/identity/IdentityHostingStartup*에서 구성 됩니다. 자세한 내용은 IHostingStartup를 참조 하세요.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

`UseStaticFiles`후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다.

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>권한 부여를 사용 하 여 MVC 프로젝트에 id 스 캐 폴드

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>전체 id UI 원본 만들기

Id UI에 대 한 모든 권한을 유지 하려면 Id 스 캐 폴더를 실행 하 고 **모든 파일 재정의**를 선택 합니다.

다음 강조 표시 된 코드는 ASP.NET Core 2.1 웹 앱에서 기본 Id UI를 Id로 바꾸는 변경 내용을 보여 줍니다. 이 작업을 수행 하 여 Id UI에 대 한 모든 권한을 부여할 수 있습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

기본 Id는 다음 코드에서 바뀝니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

`IEmailSender` 구현을 등록 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>등록 페이지 사용 안 함

사용자 등록을 사용 하지 않도록 설정 하려면:

* 스 캐 폴드 Id입니다. Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다. 다음은 그 예입니다.

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 사용자가이 끝점에서 등록할 수 없도록 *영역/i d/페이지/계정/등록을 업데이트 합니다* .

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 이전 변경 내용과 일치 하도록 *영역/i d/페이지/계정*

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *영역/i d/페이지/계정/로그인* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* *영역/i d/페이지/계정/* a p 확인 페이지를 업데이트 합니다.

  * Cshtml 파일에서 코드 및 링크를 제거 합니다.
  * `PageModel`에서 확인 코드를 제거 합니다.

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>다른 앱을 사용 하 여 사용자 추가

웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다. 사용자를 추가 하는 옵션은 다음과 같습니다.

* 전용 관리 웹 앱입니다.
* 콘솔 앱입니다.

다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.

* 사용자 목록을 메모리로 읽습니다.
* 각 사용자에 대해 강력한 고유 암호가 생성 됩니다.
* 사용자가 Id 데이터베이스에 추가 됩니다.
* 사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core 2.1 이상으로 인증 코드 변경](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end