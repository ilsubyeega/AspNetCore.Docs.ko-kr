# <a name="additional-claims-sample-app"></a>추가 클레임 샘플 앱

샘플 앱에서는 다음을 수행 하는 방법을 보여 줍니다.

* Google에서 사용자의 이름과 성을 얻고 Google에서 제공 하는 값을 사용 하 여 이름 클레임을 저장 합니다.
* 사용자의 `AuthenticationProperties`에 Google 액세스 토큰을 저장 합니다.

샘플 앱을 사용 하려면 다음을 수행 합니다.

1. 앱을 등록 하 고 Google 인증에 대 한 유효한 클라이언트 ID 및 클라이언트 암호를 가져옵니다. 자세한 내용은 [Google external login 설정](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins)을 참조 하세요.
1. `Startup.ConfigureServices`[GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 에서 앱에 클라이언트 ID 및 클라이언트 암호를 제공 합니다.
1. 앱을 실행 하 고 내 클레임 페이지를 요청 합니다. 사용자가 로그인 하지 않으면 앱이 Google으로 리디렉션됩니다. Google을 사용 하 여 로그인 합니다. Google은 사용자를 앱으로 다시 리디렉션합니다 (`/MyClaims`). 사용자가 인증 되 고 내 클레임 페이지가 로드 됩니다. 지정 된 이름 및 성 클레임은 Google에서 제공 하는 값을 사용 하 여 **사용자 클레임** 에 포함 됩니다. 액세스 토큰은 **인증 속성**아래에 표시 됩니다.
