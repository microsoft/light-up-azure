# GitHub Copilot 사용하기 &ndash; GitHub Actions

## 사전 준비사항

- GitHub Copilot은 유료 서비스입니다. 따라서, 사전에 서비스 사용과 관련한 준비가 되어 있어야 합니다. 자세한 내용은 [이 문서](https://docs.github.com/ko/copilot/quickstart)를 참조하세요.


## GitHub Actions 시크릿 추가하기

1. 터미널에서 아래 명령어를 통해 애저에 로그인합니다.

    ```bash
    az login
    ```

   > 새 웹 브라우저 탭이 나타나면서 404 에러가 보인다면 주소창의 `http://localhost...`로 시작하는 주소를 복사해서 새 터미널 창에 `curl` 명령어와 함께 붙여넣습니다.
   > 이 때 새 터미널 창을 bash 터미널로 열어서 잘 실행이 안 된다면, zsh 터미널로 열어서 해 보세요.

1. 아래 명령어를 통해 현재 구독을 확인합니다.

    ```bash
    az account show
    ```

1. 아래 명령어를 통해 애저 로그인 키를 생성합니다. 이 때 이름의 `swm{{숫자}}`는 앞서 생성한 `AZURE_ENV_NAME`입니다.

    ```bash
    subscriptionId=$(az account show --query "id" -o tsv)
    az ad sp create-for-rbac \
        --name "spn-swm{{숫자}}" --role contributor \
        --scopes /subscriptions/$subscriptionId \
        --sdk-auth
    ```

   생성된 키는 대략 아래와 같은 모양입니다. 아래 `clientId`, `clientSecret`, `subscriptionId`, `tenantId` 값은 예시일 뿐 실제로 존재하지 않는 값입니다.

    ```json
    {
      "clientId": "9814fa03-26b8-4ce0-9021-121d16cc929f",
      "clientSecret": "uOR^Pt*Tv7iMqtzrQe1Zq5Djlf*K4P8QzALlNfw#",
      "subscriptionId": "a348f9f2-dc63-4083-b19b-4f235e0b5175",
      "tenantId": "4e18dd75-d9e5-4afc-ad7e-ac436d98e812",
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
      "resourceManagerEndpointUrl": "https://management.azure.com/",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
      "galleryEndpointUrl": "https://gallery.azure.com/",
      "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 위에 생성한 JSON 개체를 GitHub 리포지토리 설정 탭의 Secrets 섹션에서 `AZURE_CREDENTIALS` 값으로 입력합니다.
1. 앞서 생성한 `AZURE_ENV_NAME` 값을 시크릿으로 입력합니다.


## GitHub Actions 워크플로우 만들기

1. `.github/workfows/main.yaml` 파일을 생성합니다.
1. Bing Chat에 아래 프롬프트를 던져 스켈레톤 워크플로우를 생성합니다.

    ```text
    Could you show me the skeleton structure of GitHub Actions workflow?
    ```

   만들어진 스켈레톤 워크플로우를 복사해서 `.github/workfows/main.yaml` 파일에 붙여 넣습니다.

1. 워크플로우의 `name` 속성에 `Development` 라고 값을 줍니다.
1. `on` 속성 밑에 아래 주석을 추가합니다.

    ```yml
    # add a trigger for the push event
    ```

1. job 이름을 `build-test`로 지정합니다.
1. `runs-on`: 속성값을 `ubuntu-latest`로 지정합니다.
1. `steps` 속성 밑에 아래 주석을 추가합니다.

    ```yml
    # add a step to checkout the code
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to install .NET SDK 7
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to restore NuGet packages
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to build the solution
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to test the solution
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to publish src/WebApi
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to zip the publish folder
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to login to Azure
    ```

1. 그 다음에 아래 주석을 추가합니다.

    ```yml
    # add a step to deploy the artifact using Azure CLI
    ```

   이 때 필요하다면 아래와 같이 커맨드를 수정해야 할 수도 있습니다.

    ```bash
    az webapp deployment source config-zip \
        --resource-group "rg-${{ secrets.AZURE_ENV_NAME }}" \
        --name "appsvc-${{ secrets.AZURE_ENV_NAME }}" \
        --src ./publish/azure-dev.zip
    ```

1. 아래 명령어를 통해 코드를 푸시하고 GitHub 액션 워크플로우가 작동하는 것을 확인합니다.

    ```bash
    git add .
    git commit -m "Add GitHub Actions workflow"
    git push origin
    ```

1. GitHub 액션 워크플로우에 녹색 불이 켜지면 성공적으로 배포한 것입니다. 웹 브라우저의 주소창에 아래 내용을 입력해서 데이터가 제대로 나오는지 확인합니다.

    ```text
    https://appsvc-{{AZURE_ENV_NAME}}.azurewebsites.net/WeatherForecast
    ```

👈 이전 | [GitHub 코파일럿 &ndash; 애저 Bicep으로 클라우드 인프라 만들기](./03-copilot-bicep.md) | [참고 문서](./05-references.md) | 다음 👉
