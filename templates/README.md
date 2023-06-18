# [세션 제목]

실습 문서를 작성하면서 몇 가지 부탁 드릴 것이 있습니다.

1. 이 **Light-up Azure** 시리즈는 한국어 사용자를 대상으로 컨텐츠를 제작하는 만큼 가능한 한 모든 부분을 한국어로 제공하는 것을 목표로 하고 있습니다. 따라서, 기술 용어들을 한국어로 바꿔 작성해 주세요. 아래는 예시입니다. 이것 말고도 훨씬 더 많으니 작성자 분들께서 최대한 재량껏 사용해 주세요.

   - Microsoft - 마이크로소프트
   - Azure ➡️ 애저
   - Cognitive Services ➡️ 인지서비스
   - Azure OpenAI Service ➡️ 애저 OpenAI 서비스
   - Machine Learning ➡️ 머신러닝 (적당한 한국어 표기가 없을 경우 이와 같이 음차합니다)

1. 한국어 문법에는 수동태가 없습니다. 따라서, 수동태 문장을 사용하기 보다는 피동 접사를 사용한다거나 하는 식으로 작성해 주세요. 아래는 대략의 예시입니다.

   - "IP 주소가 xxx.xxx.xxx.xxx에서 yyy.yyy.yyy.yyy로 전환됩니다" ➡️ `IP 주소가 xxx.xxx.xxx.xxx에서 yyy.yyy.yyy.yyy로 바뀝니다`
   - "우측 상단의 버튼이 활성화됩니다" ➡️ `우측 상단의 버튼을 클릭할 수 있게 됩니다`

1. 높임을 나타내는 접미사 `-시-`를 사용하지 않습니다.
1. 중언부언을 하지 않습니다. 아래는 예시입니다.

   - "~을(를) 하도록 하겠습니다" ➡️ `~을(를) 하겠습니다`
   - "~을(를) 해 보도록 하겠습니다" ➡️ `~을(를) 하겠습니다`
   - "~을(를) 하는 것을 확인했습니다" ➡️ `~을(를) 했습니다`
   - "~을 해 보았습니다" ➡️ `~을(를) 했습니다`

1. 한줄 짜리 명령어 같은 경우에는 코드는 `` ` ``(백틱)으로 감싼 인라인 코드를 사용하면 좋습니다.
1. 여러줄 짜리 코드 블록 같은 경우에는 아래와 같은 형식으로 코드 블록을 만들어 주세요.

    ```bash
    npm install -g @githubnext/github-copilot-cli
    eval "$(github-copilot-cli alias -- "$0")"

    echo '
    # Add GitHub Copilot CLI alias
    alias ghcp='github-copilot-cli'
    eval "$(github-copilot-cli alias -- "$0")"
    ' >> $HOME/.bashrc
    ```

위 정도만 신경써서 문서를 작성해도 꽤 완성도가 높은 문서가 만들어집니다.

> 아래와 같이 목차를 표기합니다

- [\[세션 제목\]](#세션-제목)
  - [실습 주제](#실습-주제)
  - [실습 목표](#실습-목표)
  - [실습 내용](#실습-내용)
    - [실습 내용 #1](#실습-내용-1)
    - [실습 내용 #2](#실습-내용-2)
    - [실습 내용 #3](#실습-내용-3)
  - [참고 문서 링크](#참고-문서-링크)
  - [Microsoft Learn 학습 링크](#microsoft-learn-학습-링크)


## 실습 주제

> 이 실습의 주제를 두세 문장 정도로 요약합니다.


## 실습 목표

> 이 실습의 목표를 불릿포인트 형태로 3개-5개 정도로 요약합니다. 문장은 보통 `~ 할 수 있다` 형태로 끝내면 좋습니다.


## 실습 내용

### 실습 내용 #1

### 실습 내용 #2

### 실습 내용 #3

## 참고 문서 링크

> 모든 참고 문서 링크는 `ko` 또는 `ko-kr` 로케일이 있는 경우 해당 문서를 링크합니다.
>
> 예: [애저 OpenAI 서비스](https://azure.microsoft.com/ko-kr/products/cognitive-services/openai-service)


## Microsoft Learn 학습 링크

> 모든 Microsoft Learn 학습 링크는 `ko-kr` 로케일 문서를 링크합니다.
>
> 예: [애저 OpenAI를 사용하여 AI 솔루션 개발하기](https://learn.microsoft.com/ko-kr/training/paths/develop-ai-solutions-azure-openai/)

