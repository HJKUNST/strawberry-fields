---
layout: post
title: "디자인에서 구현까지: Figma와 Cursor MCP를 연결해봤다"
date: 2025-06-15 02:04:00 +0900
categories: [Front-End]
---

## 목적: Figma 리소스를 코드로 바로 변환하기

Cursor AI와 Figma 파일을 연동시켜 Figma 리소스를 바로 코드화하는 과정을 공유합니다.

### 시도 이유: 작가의식에 기반한 웹 사이트 제작

> "다른 대부분의 디자인과 예술은 프로덕트 제작 완료까지를 완성으로 보는데, 왜 UX, UI 디자인만 실제 구현까지 디자이너가 담당하지 않는가?"

이 질문에 대한 저의 도전이었습니다.

### 시작 전, 몇 가지 의문점들

시작하기 전, 다음과 같은 의문이 있었습니다.

1.  **외형 구현의 한계**
    외형은 구현하겠지만, flex, grid layout이나 margin/padding 적용, 질감 표현 등을 제가 만든 그대로 옮길 수 있을까요? 결국 개발 공수가 2배가 되지 않을까 걱정됐습니다. (과거 lovable, v0 등에서 어려움을 겪었던 이유입니다.)

2.  **애니메이션 구현**
    Figma의 프로토타입 애니메이션을 구현할 수 있을까요? Figma는 프레임 간 연결로 애니메이션을 구현하는데, 단일 프레임 기반으로 연동하면 어려울 것 같았습니다. 만약 된다면 어떤 라이브러리를 사용할지도 궁금했습니다.

3.  **JavaScript 로직 처리**
    JS로 구현해야 하는 동적인 컴포넌트들은 어떻게 처리될까요? 단순히 화면의 겉모습만 만들어주는 것은 아닐까 의심했습니다.

4.  **인터랙션 상태 처리**
    `:hover` 같은 상태(State) 변화는 어떻게 처리될까요?

### 이 글을 공유하는 이유

-   대부분의 가이드가 '연결 완료' 후의 모습만 보여줍니다.
-   저처럼 헤매실 분들을 위해 제 모든 시행착오를 공유하고자 합니다.
-   개발자와 디자이너 간의 소통 비용을 줄여, 모두가 만족하는 제품을 더 빠르게 만들 수 있기를 바랍니다.
-   실무에 직접 사용해보니, 특히 프로젝트 초기 세팅이 정말 간편해졌습니다.

### 그래서 결과는?

결론부터 말하면, **성공적이었습니다.**

-   아래 랜딩페이지는 배포 전까지의 모든 과정을 제가 직접 담당할 수 있었던 소중한 기회였습니다.
-   물론 캐러셀(Carousel)이나 세부 모션은 AI의 도움을 받아 추가로 커스터마이징했지만, 웹사이트의 초기 구조와 디자인 시스템을 잡는 데 Cursor의 도움을 정말 많이 받았습니다.
-   이제 여러분도 아래 과정을 통해 멋진 랜딩 페이지를 만들 수 있습니다!

![랜딩페이지 결과물 1](attachment:093b5f3d-ebf7-46c1-91cc-893236f9335a:Screenshot_2025-06-15_at_1.40.26_AM.png)
![랜딩페이지 결과물 2](attachment:8cda9140-4bc9-4553-9c30-ac4bd1db45ee:Screenshot_2025-06-15_at_1.41.30_AM.png)
![랜딩페이지 결과물 3](attachment:c89322f8-e3cd-445c-97dc-7fd5d74b195c:Screenshot_2025-06-15_at_1.42.14_AM.png)
![랜딩페이지 결과물 4](attachment:9bca05a9-a4a3-40df-b2fa-52e8bf7c73b6:Screenshot_2025-06-15_at_1.24.48_AM.png)

---

## 연동 과정 (Step-by-Step)

### 1. Figma에서 작업 프레임 준비하기

먼저 Figma 파일에서 코드로 변환하길 원하는 Frame을 화면에 띄워두세요.

![Figma에서 작업할 프레임 선택](attachment:e4671c00-a609-465f-8195-854ac0693220:Screenshot_2025-06-14_at_3.50.43_PM.png)

### 2. Cursor 로그인하기

(계정 당 2주 무료 평가판을 제공하며, 미국 학생일 경우 Pro를 무료로 사용할 수 있습니다. 저는 계정 3개를 사용한 뒤 IP가 차단되어 결국 Pro를 1년 결제했습니다.)

### 3. Cursor에서 MCP 서버 설정하기

1.  Cursor 에디터에서 `Settings` > `MCP Tools` > `New MCP Server`를 차례로 클릭합니다.

2.  `mcp.json` 파일이 열리면 아래 내용을 그대로 붙여넣습니다.
    ```json
    {
      "mcpServers": {
        "Framelink Figma MCP": {
          "command": "npx",
          "args": [
            "-y",
            "figma-developer-mcp",
            "--figma-api-key=YOUR-KEY",
            "--stdio"
          ]
        }
      }
    }
    ```

3.  `--figma-api-key=YOUR-KEY` 부분을 실제 API 키로 교체해야 합니다.
    -   **Figma**로 이동하여 `우측 상단 프로필` > `Settings` > `Personal access tokens` 섹션으로 갑니다.
    -   `Generate new token` 버튼을 클릭합니다.
    -   토큰 이름(예: `Cursor 연동용`)을 입력하고, Scopes에서 `File content: Read-only`를 선택한 뒤 토큰을 생성합니다.
        ![Figma API 토큰 생성 설정](attachment:60f16367-51f9-43b6-a5d5-01c021eb24d7:Screenshot_2025-06-14_at_4.02.11_PM.png)
    -   생성된 토큰을 복사(`Copy this token`)하여 `YOUR-KEY` 자리에 붙여넣습니다.
    -   **⚠️ 주의:** 이 토큰은 다시 볼 수 없으니 잘 보관해야 하며, GPT 같은 외부 AI 챗봇에 실수로 노출되지 않도록 각별히 조심하세요.
        ![Figma API 토큰 복사](attachment:3ac845a6-b24c-4200-97ba-32d684175543:Screenshot_2025-06-14_at_4.07.52_PM.png)

4.  지금까지 올바르게 설정했다면 `mcp.json` 파일이 아래와 같이 보일 것입니다.
    ![mcp.json 파일 완성 모습](attachment:3c98391b-d495-4f63-b4c7-bdf2cc835eab:Frame_5.png)

5.  파일을 저장하고 닫은 뒤, 다시 `Cursor Settings` > `MCP Tools`로 돌아갑니다. 방금 만든 `Framelink Figma MCP` 항목 오른쪽에 있는 **토글을 켜서 활성화**해주세요.
    ![MCP 서버 활성화](attachment:7d69a7f5-f771-4c60-85ab-9b52200ca0ce:Screenshot_2025-06-15_at_1.00.29_AM.png)

### 4. Cursor Chat으로 디자인 구현 요청하기

이제 Cursor Chat을 열어 AI에게 작업을 지시할 차례입니다. (이 글은 기본편이므로, 심화 프롬프팅보다는 핵심적인 요청 방법을 소개합니다.)

> **프롬프트 예시**
>
> ```
> implement this figma file and generate a new react project by (next app) on desktop @(파일링크)
> 
> - I need new project and want you to adapt `get_figma_data`
> - Always choose the best consideration for this project
> ```
> *참고: `(next app)` 부분은 `react`, `html/css/javascript` 등 원하는 기술 스택으로 변경할 수 있습니다.*

-   **파일 링크 얻는 법**
    -   1단계에서 띄워 둔 피그마 프레임을 우클릭합니다.
    -   `Copy/Paste as` > `Copy link`를 선택하여 링크를 복사합니다.
    -   프롬프트의 `@(파일링크)` 부분에 붙여넣습니다.

요청을 보내면, Cursor가 잠시 후 아래와 같이 `get_figma_data` 툴을 사용하겠다고 제안합니다. 이 화면이 나타나면 MCP 연동에 성공한 것입니다.

![Cursor AI의 get_figma_data 사용 제안](attachment:a979d2d8-7a98-450c-a5da-a79aea0ea0ce:Screenshot_2025-06-15_at_1.12.48_AM.png)

AI가 생성한 파일 구조와 코드를 미리 보여줍니다. 내용을 확인하고 오른쪽 하단의 **`Accept`** 버튼을 누르면 프로젝트에 바로 적용됩니다.

![AI가 생성한 코드 수락](attachment:8983dfb2-863f-4ed8-a33b-8e24737c6a06:Screenshot_2025-06-15_at_1.16.26_AM.png)

### 5. 결과 확인하기

짠! 저는 Next.js 앱을 요청했고, 터미널에서 아래 명령어로 서버를 실행했습니다.

```bash
# npm 사용 시
npm install && npm run dev

# yarn 사용 시
yarn && yarn dev

![로컬 서버에서 확인한 결과물](attachment:1ab2a00c-5606-4f0a-b3f5-e7955e8fcde8:Screenshot_2025-06-15_at_1.17.37_AM.png)

![생성된 코드 예시](attachment:a8c0e222-ec2f-4dbe-a63b-537ca33caac5:Screenshot_2025-06-15_at_1.17.59_AM.png)

---

## 참고할 만한 다른 자료들

-   [https://github.com/GLips/Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP)
-   [https://www.framelink.ai/docs/quickstart](https://www.framelink.ai/docs/quickstart)
-   [https://www.reese-log.com/cursor-figma-model-context-protocol](https://www.reese-log.com/cursor-figma-model-context-protocol)