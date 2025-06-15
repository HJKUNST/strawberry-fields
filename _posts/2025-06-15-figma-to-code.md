---
layout: post
title: "디자인에서 구현까지: Figma와 Cursor MCP를 연결해봤다 (기본편)"
date: 2025-06-15 02:04:00 +0900
categories: [Front-End]
---

<div markdown="1">

### 목적
Cursor AI와 Figma 파일을 연동시켜서, 디자인 리소스를 바로 코드로 전환해보고자 했다. 다른 대부분의 디자인과 예술은 프로덕트 완성까지를 '완성'으로 보는데, 왜 UX, UI 디자인만 구현은 개발자의 몫이어야 할까? 그 질문에 대한 도전이었다. 결론적으로 작가 의식에 기반한 웹사이트 제작을 해보고 싶었던 것 같다.

</div>
<div markdown="1">

### 가능성에 대한 의문들

-  외형은 구현할 수 있더라도, flex, grid layout, margin/padding, 질감 표현까지 내가 만든 대로 옮겨질 수 있을까?

- 개발 공수가 두 배가 되지는 않을까? lovable과 v0에서는 코드만 던져주는 느낌이라 실제 프로덕션에 적용하고 싶지 않았다. 디자인이란 결국 프로덕트에서 감성적인 보완점을 주는 역할을 빼놓을 수 없는데, 트렌드와 간결함에만 충실하고 친화적이지 않았다.

-  Animation이 들어간 prototype도 구현 가능할까?  
    - Figma에서는 프레임 간 연결로 prototype과 animation을 구현하지만, 단일 프레임 기반으로 MCP를 연결하면 어렵지 않을까?  
    - 애니메이션이 가능하다면, 어떤 library이 효과적일까?

- hover 같은 상태(state)는 어떻게 구현되는가?

-  JavaScript로 구현이 필요한 component들은 어떻게 처리할 수 있을까? 단순히 화면만 구성되고 기능은 무시되는 게 아닌가?

</div>

<div markdown="1">

### 이 블로그를 남기는 이유

다른 사람들은 Cursor 연동만 완료한 뒤, 결과물만 보여준다. 개발 배경 지식이 없는 사람이 보더라도 Figma + Cursor MCP를 사용할 수 있도록 만들고 싶었다. 물론 의미있는 삽질이었지만, 다른 사람들은 나와 같은 시행착오를 겪지 않고 빠르게 개발을 하길 바란다. 디자이너와 개발자 간 소통 공수를 줄여, 더 빠르고 만족스러운 제품을 함께 만들어가기 위해 필요한 기술인 만큼 많이들 봐주었으면 좋겠다.

</div>

<div markdown="1">

### 그래서 결과는?
<style>
@media (max-width: 600px) {
  .responsive-flex {
    flex-direction: column !important;
  }
  .responsive-text {
    min-width: auto !important;
  }
  .responsive-images {
    min-width: auto !important;
  }
}
</style>

<div class="responsive-flex" style="display: flex; gap: 24px; align-items: flex-start;">
  <p class="responsive-text" style="line-height: 1.6; min-width: 400px;">
    꽤나 구조적으로, 예쁘게 뽑아줬던 것 같다. 이 랜딩페이지는 배포 직전까지의 거의 모든 과정을 내가 담당할 수 있었던 기회였다.
    <br/><br/> 아쉽게도 Carousel이나 모션 등은 커스텀 해야하긴 했지만 (그러나 이것도 AI와 조금 함께하긴 했다), 초기 구조나 디자인 시스템은 Cursor의 도움을 많이 받았다.
    <br/><br/> 아래 과정을 따라 하면, 여러분도 아래와 같은 랜딩페이지를 만들 수 있다! (아마도) 사이트 이름 안나오게 보여줄 수 있는 부분만 올려보자면 이런 느낌이다.
  </p>
  <div class="responsive-images" style="display: flex; flex-direction: column; min-width: 600px;">
    <img src="https://private-user-images.githubusercontent.com/127034324/455272134-88d22c30-d1c7-4088-8b8c-064045ffed16.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTAwMDcwNzUsIm5iZiI6MTc1MDAwNjc3NSwicGF0aCI6Ii8xMjcwMzQzMjQvNDU1MjcyMTM0LTg4ZDIyYzMwLWQxYzctNDA4OC04YjhjLTA2NDA0NWZmZWQxNi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwNjE1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDYxNVQxNjU5MzVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0yZGNhYjBhMTJlZTM2YjQwN2JhY2M1ODJjMzA2ODU3YWQ4NjUyZDIyMjFiYTQ3YTMzNDZiZGU0YjIwMzMxZGFkJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9._I09LiyDyfs2rz4eIWxQvxfWSF6yuDumaQDkEwa6u4c" alt="landing" style="width: 100%; height: auto; display: block;"/>
    <img src="https://private-user-images.githubusercontent.com/127034324/455272149-8f2d3e2a-3f58-43fb-a9a8-4bb284ec2914.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTAwMDcwNzUsIm5iZiI6MTc1MDAwNjc3NSwicGF0aCI6Ii8xMjcwMzQzMjQvNDU1MjcyMTQ5LThmMmQzZTJhLTNmNTgtNDNmYi1hOWE4LTRiYjI4NGVjMjkxNC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwNjE1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDYxNVQxNjU5MzVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01MDNjZTE4ZTU1NjkyMDExMDQxNjkwMWRhMDI1MGJlMGY5NzRjYmFiYjc2ODg3NmFkM2I1MjQxOTZiZDQ5ZjAzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.s_mtiEhMRCKeHtYXSYyk2T90LTHutnV5akAFUPjtsFo" alt="landing-2"  style="width: 100%; height: auto; display: block;" />
   </div>
</div>


</div>

---

### 순서

#### 1. Figma 파일에서 연결할 Frame을 열어두기
   **(이미지)**

#### 2. Cursor 로그인
   - 계정당 2주 무료  
   - 미국 학생은 Pro 요금제 무료 사용 가능  
   - 필자는 계정 3개를 넘기면서 IP 밴을 당해 결국 1년 Pro 결제함

#### 3. Cursor Settings > MCP Tools > New MCP Server 클릭
생성된 `mcp.json` 파일에 다음 내용 입력:

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
#### 4. 여기서 `--figma-api-key=YOUR-KEY` 부분을 채워야 하는데
- Figma > Settings > Security에 들어가서 ‘Generate new token’ 을 누른다
- 자기가 구분할 수 있는 이름 넣고 만들면 되고, Scopes를 선택해서 생성해준다
(이미지)
- Copy this token하고 `YOUR-KEY` 에 넣어준다
- gpt같은 ai chatbot에 질문할 때 들어가지 않도록 조심하기
(이미지)

#### 5. 설정을 마쳤다면 다음과 같은 화면이 보일 것이다  
   **(이미지)**

#### 4. 지금까지 mcp.json에 복붙했을텐데, 이걸 닫고 다시 cursor setting으로 돌아가자. 방금 만든 json이 적용되어 이렇게 뜰 것이다.
MCP 서버 토글이 꺼져 있다면 **켜기**  
   **(이미지)**

---

### Cursor Chat으로 구현 요청하기
기본편에서는 심화적인 프롬프팅과 설정보다는 일단 만드는 것만을 소개한다
Cursor Chat을 열고 다음과 같이 입력:

```md
implement this figma file and generate a new react project by (html/css/javascript or react or next app) on desktop @(링크)
```

**추가 프롬프트:**

```text
I need new project and want you to adapt get_figma_data
Always choose the best consideration for this project
```

- 연결이 성공하면 다음과 같은 화면이 보인다:  
  **(이미지)**

- 생성된 코드를 확인 후, 우측 하단 **Accept 버튼 클릭**  
  **(이미지)**

---

### 로컬에서 실행하기

나는 `next app`으로 생성한 뒤, 로컬 서버를 실행했다:
간단한 컴포넌트 페이지지만, Figma의 auto-layout 설정이 div 구조로 잘 반영되어 있었다.

**(이미지)**  
**(이미지)**

---

### 참고할만한 자료들

- [Figma Context MCP GitHub](https://github.com/GLips/Figma-Context-MCP)  
- [Framelink Quickstart Docs](https://www.framelink.ai/docs/quickstart)  
- [Cursor 실험기 - reese-log](https://www.reese-log.com/cursor-figma-model-context-protocol)
