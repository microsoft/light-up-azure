# GitHub Codespaces 설정하기

## 코드스페이스 인스턴스 만들기

아래 그림과 같이 코드스페이스 인스턴스를 생성합니다.

![새 코드스페이스 인스턴스 만들기](../images/codespace-01.png)


## 디렉토리 및 파일 만들기

1. `.devcontainer` 디렉토리를 리포지토리 루트에 만듭니다.
1. `.devcontainer` 디렉토리 아래 아래와 같은 세 파일을 만듭니다.
   - `Dockerfile`
   - `devcontainer.json`
   - `post-create.sh`
1. `post-create.sh` 파일에 실행 권한을 줍니다.

   ```bash
   chmod +x .devcontainer/post-create.sh
   ```


## `Dockerfile`

[https://github.com/devcontainers/images](https://github.com/devcontainers/images/tree/main/src) 또는 Docker Hub에서 베이스 이미지를 참조합니다. 아래는 몇가지 예시입니다.

- Ubuntu 20.04 LTS 기반에 .NET SDK 7을 설치한 베이스 이미지

    ```dockerfile
    ARG VARIANT="7.0-jammy"
    FROM mcr.microsoft.com/dotnet/sdk:${VARIANT}

    ENV PATH $PATH:/home/vscode/.dotnet:/home/vscode/.dotnet/tools
    ```


## `devcontainer.json`

`devcontainer.json` 파일의 기본 구조는 아래와 같습니다.

```jsonc
{
  "name": "DevContainer 이름",
  
  "build": {
    // 컨테이너 기본 이미지 설정
  },
  
  "forwardPorts": [
    // 포트 포워딩
  ],
  
  "features": {
    // 추가 기능
  },
  
  "overrideFeatureInstallOrder": [
    // 추가 기능 설치 순서
  ],
  
  "customizations": {
    "vscode": {
      // 코드스페이스 커스터마이징
      "extensions": [
        // VS Code 익스텐션
      ],
  
      "settings": {
        // VS Code 환경 설정
      }
    }
  },

  // post-create.sh 스크립트 실행 명령어
  "postCreateCommand": "/bin/bash ./.devcontainer/post-create.sh > ~/post-create.log",

  // DevContainer 사용자 계정
  "remoteUser": "vscode"
}
```

1. `build` 속성을 정의합니다. 아래는 .NET을 예시로 들었습니다.

    ```jsonc
    {
      ...
      "build": {
        "dockerfile": "./Dockerfile",
        "context": ".",
        "args": {
          "VARIANT": "7.0"
        }
      },
      ...
    }
    ```

1. `forwardPorts` 속성을 정의합니다. 아래는 `3000`, `5000`, `5001`, `8080`, `44380` 포트를 포워딩하겠다는 의미입니다.

    ```jsonc
    {
      ...
      "forwardPorts": [ 3000, 5000, 5001, 8080, 44380 ],
      ...
    }
    ```

1. `features` 속성을 정의합니다. 설치를 원하는 피처는 [https://github.com/devcontainers/features](https://github.com/devcontainers/features/tree/main/src) 리포지토리를 참조합니다. 아래는 Azure CLI, GitHub CLI, node 등을 설치하는 예시입니다.

    ```jsonc
    {
      ...
      "features": {
        // Azure CLI
        "ghcr.io/devcontainers/features/azure-cli:latest": {
          "version": "latest"
        },

        // GitHub CLI
        "ghcr.io/devcontainers/features/github-cli:latest": {
          "version": "latest"
        },
    
        // node.js
        "ghcr.io/devcontainers/features/node:latest": {
          "version": "lts",
          "nodeGypDependencies": true,
          "nvmInstallPath": "/usr/local/share/nvm"
        },
    
        // common utilities
        "ghcr.io/devcontainers/features/common-utils:latest": {
          "installZsh": true,
          "installOhMyZsh": true,
          "upgradePackages": true,
          "username": "vscode",
          "uid": "1000",
          "gid": "1000"
        }
      },
      ...
    }
    ```

1. `features` 속성에 정의한 피처들을 설치하기 위한 순서를 정의합니다. 여기서는 `common-utils` 피처를 가장 먼저 설치하고 나머지 피처는 어떤 순서로 설치하든 상관없게끔 했습니다.

    ```jsonc
    {
      ...
      "overrideFeatureInstallOrder": [
        "ghcr.io/devcontainers/features/common-utils"
      ],
      ...
    }
    ```

1. 코드스페이스에 설치할 익스텐션을 정의합니다. 여기서는 GitHub 코파일럿 익스텐션, C# 익스텐션, Azure 익스텐션을 설치하는 것을 예로 들었습니다.

    ```jsonc
    {
      ...
      "customizations": {
        "extensions": [
          // GitHub 코파일럿 관련
          "GitHub.copilot",
          "GitHub.copilot-labs",

          // C# 관련
          "ms-dotnettools.vscode-dotnet-pack",
          "kreativ-software.csharpextensions",

          // Azure 관련
          "ms-vscode.vscode-node-azure-pack",
          "ms-azuretools.vscode-bicep"
        ],
      },
      ...
    }
    ```

   > 더 많은 익스텐션을 설치하고 싶다면 [https://marketplace.visualstudio.com/vscode](https://marketplace.visualstudio.com/vscode??WT.mc_id=dotnet-95000-juyoo)에서 검색한 후 추가하세요.

1. 코드스페이스 UI 구성을 정의합니다. 아래는 미니맵을 비활성화하고 파일을 확장자 기준으로 정리하는 것을 예로 들었습니다.

    ```jsonc
    {
      ...
      "customizations": {
        "settings": {
          "editor.minimap.enabled": false,
          "explorer.sortOrder": "type"
        }
      },
      ...
    }
    ```


## `post-create.sh`

`devcontainer.json` 파일로 컨테이너 설정을 끝낸 이후, 마지막으로 추가 기능을 설치하는 스크립트입니다. 아래 내용은 운영체제 업데이트 및 추가 패키지를 설치하는 과정에 대한 예시입니다.

```bash
## Install additional apt packages
sudo apt-get update \
    && sudo apt-get install -y dos2unix libsecret-1-0 xdg-utils \
    && sudo apt clean -y \
    && sudo rm -rf /var/lib/apt/lists/*

## Configure git
git config --global pull.rebase false
git config --global core.autocrlf input

## GitHub Copilot CLI ##
npm install -g @githubnext/github-copilot-cli
eval "$(github-copilot-cli alias -- "$0")"

## GitHub Copilot CLI ##
echo '
# Add GitHub Copilot CLI alias
alias ghcp='github-copilot-cli'
eval "$(github-copilot-cli alias -- "$0")"
' >> $HOME/.zshrc
echo '
# Add GitHub Copilot CLI alias
alias ghcp='github-copilot-cli'
eval "$(github-copilot-cli alias -- "$0")"
' >> $HOME/.bashrc

## AZURE BICEP CLI ##
az bicep install

## AZURE DEV CLI ##
curl -fsSL https://aka.ms/install-azd.sh | bash
```


## 코드스페이스 재실행

1. 아래 명령어를 실행시켜 변경사항을 저장하고 리포지토리에 반영합니다.

    ```bash
    git add .
    git commit -m "Add devcontainer settings"
    git push origin
    ```

1. `F1` 키를 눌러 `Codespaces: Full Rebuild Container` 명령을 실행시킵니다.
1. 아래 명령어를 통해 각종 도구가 제대로 설치가 되었는지 확인합니다.

    ```bash
    # OS 정보
    cat /etc/os-release

    # .NET SDK 설치 여부
    dotnet --list-sdks

    # 파워셸 설치 여부
    pwsh --version

    # nvm 설치 여부
    nvm --version

    # node 설치 여부
    node --version

    # npm 설치 여부
    npm --version

    # GitHub CLI 설치 여부
    gh --version

    # Azure CLI 설치 여부
    az --version

    # Azure Dev CLI 설치 여부
    azd version

    # 기타 환경 변수 확인
    printenv -0 | sort -z | tr '\0' '\n'
    ```

👉 다음: [GitHub 코파일럿 &ndash; ASP.NET Core Web API 앱 만들기](./copilot-dotnet.md)
