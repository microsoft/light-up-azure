# GitHub Copilot 사용하기 &ndash; ASP.NET Core Web API

## 사전 준비사항

- GitHub Copilot은 유료 서비스입니다. 따라서, 사전에 서비스 사용과 관련한 준비가 되어 있어야 합니다. 자세한 내용은 [이 문서](https://docs.github.com/ko/copilot/quickstart)를 참조하세요.


## ASP.NET Core Web API 프로젝트

1. 아래 명령어를 실행시켜 `global.json` 파일을 생성합니다.

    ```bash
    # global.json 파일 생성
    dotnet new globaljson
    ```

1. 리포지토리 루트에 `global.json` 파일이 만들어지면, 아래와 같이 .NET SDK 프리뷰 버전을 사용하지 않는 것으로 수정합니다.

    ```json
    {
      "sdk": {
        "allowPrerelease": false
      }
    }
    ```

1. 아래 명령어를 실행시켜 ASP.NET Core Web API 프로젝트를 생성합니다.

    ```bash
    # ASP.NET Core Web API 프로젝트 생성
    dotnet new webapi -n WebApi -o src/WebApi
    ```

1. `src/WebApi/Properties/launchSettings.json` 파일을 열어 아래와 같이 포트번호를 수정합니다.

    ```jsonc
    {
      "$schema": "https://json.schemastore.org/launchsettings.json",
      "iisSettings": {
        "windowsAuthentication": false,
        "anonymousAuthentication": true,
        "iisExpress": {
          // 포트번호 수정
          "applicationUrl": "http://localhost:8080",
          "sslPort": 44380
        }
      },
      "profiles": {
        "http": {
          "commandName": "Project",
          "dotnetRunMessages": true,
          "launchBrowser": true,
          "launchUrl": "swagger",
          // 포트번호 수정
          "applicationUrl": "http://localhost:5000",
          "environmentVariables": {
            "ASPNETCORE_ENVIRONMENT": "Development"
          }
        },
        "https": {
          "commandName": "Project",
          "dotnetRunMessages": true,
          "launchBrowser": true,
          "launchUrl": "swagger",
          // 포트번호 수정
          "applicationUrl": "https://localhost:5001;http://localhost:5000",
          "environmentVariables": {
            "ASPNETCORE_ENVIRONMENT": "Development"
          }
        },
        "IIS Express": {
          "commandName": "IISExpress",
          "launchBrowser": true,
          "launchUrl": "swagger",
          "environmentVariables": {
            "ASPNETCORE_ENVIRONMENT": "Development"
          }
        }
      }
    }
    ```

1. 솔루션 파일을 생성합니다.

    ```bash
    # 솔루션 파일 생성
    dotnet new sln -n App
    ```

1. 솔루션 파일에 ASP.NET Core Web API 프로젝트를 연결합니다.

    ```bash
    # ASP.NET Core Web API 프로젝트를 솔루션 파일에 추가
    dotnet sln add src/WebApi -s src
    ```

1. 솔루션을 빌드합니다.

    ```bash
    # 솔루션 빌드
    dotnet restore && dotnet build
    ```

1. 앱을 실행시킵니다.

    ```bash
    # ASP.NET Core Web API 실행
    dotnet run --project src/WebApi
    ```

1. 아래와 같은 팝업창이 나타나면 `Open in Browser` 버튼을 클릭합니다. 아래 그림은 예시일 뿐이고 포트 번호는 `5000`입니다.

   ![로컬에서 웹 API 앱 열기](../images/copilot-01.png)

1. 웹 브라우저를 열었을 때 404 에러 페이지가 보이는 것이 정상입니다.
1. 주소 뒤에 `/WeatherForecast`를 추가한 후 다시 실행시켜 JSON 개체를 볼 수 있으면 앱이 잘 동작하는 것입니다.
1. `Ctrl + C` 키를 눌러 앱을 종료합니다.


## ASP.NET Core Web API 테스트 프로젝트

1. 아래 명령어를 실행시켜 .NET MSTest 프로젝트를 생성합니다.

    ```bash
    # .NET MSTest 프로젝트 생성
    dotnet new mstest -n WebApiTests -o test/WebApiTests
    ```

1. 테스트 프로젝트에 ASP.NET Core Web API 프로젝트를 연결합니다.

    ```bash
    ## 테스트 프로젝트에 Web API 프로젝트 연결
    dotnet add ./test/WebApiTests reference ./src/WebApi
    ```

1. 솔루션 파일에 .NET MSTest 프로젝트를 연결합니다.

    ```bash
    # .NET MSTest 프로젝트를 솔루션 파일에 추가
    dotnet sln add test/WebApiTests -s test
    ```

1. 솔루션을 빌드합니다.

    ```bash
    # 솔루션 빌드
    dotnet restore && dotnet build
    ```

## GitHub Copilot 사용해서 Swagger UI 페이지 리디렉션하기

현재 웹사이트의 루트 주소로 접속하면, 404 페이지가 나옵니다. 이를 자동으로 리디렉션 시켜 Swagger UI 페이지를 보여주게끔 합니다. 아래 프롬프트를 사용해 보세요.

1. `src/WebApi/Program.cs` 파일을 엽니다.
1. 파일의 맨 윗줄에 아래와 같이 주석을 추가합니다.

    ```csharp
    // add a namespace for rewriteoptions
    ```

1. 파일의 중간쯤 어딘가 `app.UseSwaggerUI();` 바로 밑 줄에 아래와 같이 주석을 추가합니다.

    ```csharp
    // add a new rewriteoption that redirects root to /swagger
    ```

1. 다시 한 번 앱을 빌드하고 실행 시킵니다.

    ```bash
    dotnet build
    dotnet run --project src/WebApi
    ```

1. 아래와 같은 팝업창이 나타나면 `Open in Browser` 버튼을 클릭합니다. 아래 그림은 예시일 뿐이고 포트 번호는 `5000`입니다.

   ![로컬에서 웹 API 앱 열기](../images/copilot-01.png)

1. 자동으로 Swagger UI 페이지로 리디렉션된 것을 확인합니다.
1. `Ctrl + C` 키를 눌러 앱을 종료합니다.


## 엔드포인트 추가하기

1. `src/WebApi/Controllers/WeatherForecastController.cs` 파일을 엽니다.
1. 파일의 맨 아래에 아래와 같이 주석을 추가합니다.

    ```c#
    // add a class of DateRange that defines the Length property of the integer type
    ```

1. `WeatherForecastController` 클래스의 맨 아래에 아래와 같이 주석을 추가합니다.

    ```csharp
    // add an action method of GetRange that returns a list of WeatherForecast objects and has a parameter of DateRange with the FromQuery attribute
    ```

1. 다시 한 번 앱을 빌드하고 실행 시킵니다.

    ```bash
    dotnet build
    dotnet run --project src/WebApi
    ```

1. Swagger UI 에서 `Length` 값을 조정해 가면서 결과를 확인합니다.
1. `Ctrl + C` 키를 눌러 앱을 종료합니다.


## 테스트 코드 작성하기

1. `test/WebApiTests/UnitTest1.cs` 파일을 `test/WebApiTests/Controllers/WeatherForecastControllerTests.cs` 파일로 디렉토리와 파일 이름을 변경합니다.
1. `test/WebApiTests/Controllers/WeatherForecastControllerTests.cs` 파일을 엽니다.
1. 파일의 맨 위에 아래와 같이 주석을 추가합니다.

    ```csharp
    // add a namespace for the WeatherForecastController
    ```

1. `TestMethod1()` 메소드 아래에 아래와 같이 주석을 추가합니다.

    ```csharp
    // add a test method that tests the GetRange method of WeatherForecastContoller
    ```

1. 아래 명령어를 실행시켜 테스트를 수행합니다.

    ```csharp
    dotnet build && dotnet test
    ```

👈 이전: [GitHub 코드스페이스](./codespace.md)
👉 다음: [GitHub 코파일럿 &ndash; 애저 Bicep으로 클라우드 인프라 만들기](./copilot-bicep.md)
