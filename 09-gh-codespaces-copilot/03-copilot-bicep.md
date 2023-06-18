# GitHub Copilot 사용하기 &ndash; Azure Bicep

## 사전 준비사항

- GitHub Copilot은 유료 서비스입니다. 따라서, 사전에 서비스 사용과 관련한 준비가 되어 있어야 합니다. 자세한 내용은 [이 문서](https://docs.github.com/ko/copilot/quickstart)를 참조하세요.


## `resources.bicep` 만들기

1. `infra/resources.bicep` 파일을 생성합니다.
1. Bing Chat에 아래 프롬프트를 던져 스켈레톤 구조를 생성합니다.

    ```text
    Could you show me the skeleton structure of Azure Bicep that creates an Azure App Service instance?
    ```

   만들어진 스켈레톤 워크플로우를 복사해서 `infra/resources.bicep` 파일에 붙여 넣습니다.

1. 파일의 맨 위에 아래 주석을 추가합니다.

    ```bicep
    // add parameters for name and location that defaults to the resourcegroup's location
    ```

1. 앱 서비스 플랜의 이름을 `asplan`으로 수정합니다.
1. 앱 서비스 플랜의 `name` 속성을 아래 주석을 통해 수정합니다.

    ```bicep
    // add name with the prefix of 'asplan-'
    ```

1. 앱 서비스 플랜의 `location` 속성 값을 `location`으로 수정합니다.

1. 앱 서비스의 이름을 `appsvc`로 수정합니다.
1. 앱 서비스의 `name` 속성을 아래 주석을 통해 수정합니다.

    ```bicep
    // add name with the prefix of 'appsvc-'
    ```

1. 앱 서비스의 `location` 속성 값을 `location`으로 수정합니다.


## `main.bicep` 만들기

1. `infra/main.bicep` 파일을 생성합니다.
1. Bing Chat에 아래 프롬프트를 던져 스켈레톤 구조를 생성합니다.

    ```text
    Could you show me the skeleton structure of Azure Bicep that creates a resource group and calls a module?
    ```

   만들어진 스켈레톤 워크플로우를 복사해서 `infra/main.bicep` 파일에 붙여 넣습니다.

1. 파일의 맨 위에 아래 주석을 추가합니다.

    ```bicep
    // add target scope to subscription
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```bicep
    // add parameters for name and location that defaults to koreacentral
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```bicep
    // add name with the prefix of 'rg-'
    ```

1. 모듈 호출하는 부분을 아래와 같이 수정합니다.

    ```bicep
    module appsvc './resources.bicep' = {
      name: 'AppService'
      scope: rg
      params: {
        name: name
        location: location
      }
    }
    ```

1. `infra/main.parameters.json` 파일을 생성하고 아래 내용을 입력합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "name": {
          "value": "${AZURE_ENV_NAME}"
        },
        "location": {
          "value": "${AZURE_LOCATION}"
        }
      }
    }
    ```


## Azure Dev CLI 구성하기

1. 터미널에서 아래 명령어를 입력합니다.

    ```bash
    azd auth login --use-device-code=false
    ```

   > 새 웹 브라우저 탭이 나타나면서 404 에러가 보인다면 주소창의 `http://localhost...`로 시작하는 주소를 복사해서 새 터미널 창에 `curl` 명령어와 함께 붙여넣습니다.
   > 이 때 새 터미널 창을 bash 터미널로 열어서 잘 실행이 안 된다면, zsh 터미널로 열어서 해 보세요.

1. 아래 명령어를 차례로 입력합니다.

    ```bash
    # Set AZURE_ENV_NAME
    AZURE_ENV_NAME="lua$RANDOM"

    # Get AZURE_ENV_NAME
    echo $AZURE_ENV_NAME
    ```

1. 아래 명령어를 입력해서 Azure Dev CLI 구성을 시작합니다.

    ```bash
    azd init -e $AZURE_ENV_NAME
    ```

   아래와 같은 프롬프트가 몇 개 나오는데 계속해서 `y`를 입력합니다.

   - **The current directory is not empty. Would you like to initialize a project here in '/workspaces/{{REPOSITORY_NAME}}'? (y/N)** 👈 `y` 입력
   - **Select a project template:  [Use arrows to move, type to filter]** 👈 `Starter - Bicep` 선택
   - **What would you like to do with these files?  [Use arrows to move, type to filter]** 👈 `Keep my existing files unchanged` 선택

1. `azure.yaml` 파일 및 `.azure` 디렉토리, `.github/workflows` 디렉토리가 만들어 진 것을 확인합니다.
1. `azure.yaml` 파일을 열어 아래와 같이 내용을 수정합니다.

    ```yml
    # yaml-language-server: $schema=https://raw.githubusercontent.com/Azure/azure-dev/main/schemas/v1.0/azure.yaml.json

    name: lua-codespace-copilot

    infra:
      provider: "bicep"
      path: "infra"
      module: "main"

    pipeline:
      provider: "github"
    ```

1. 아래 명령어를 입력해서 앞서 작성했던 애저 Bicep 파일을 실행시킵니다.

    ```bash
    azd up
    ```

   - "**Please select an Azure Subscription to use**" 👈 자신의 애저 구독 선택
   - "**Please select an Azure location to use**" 👈 `Korea Central` 선택

[GitHub 코파일럿 &ndash; ASP.NET Core Web API 앱 만들기](./02-copilot-dotnet.md) 👈 이전 | 다음 👉 [GitHub 코파일럿 &ndash; GitHub 액션 워크플로우 만들기](./04-copilot-gha.md)
