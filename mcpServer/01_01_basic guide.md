FastMCP로 MCP(Model-Centric Protocol) 서버를 만드는 방법은 @modelcontextprotocol/fastmcp 패키지를 활용하는 방식으로 진행됩니다. 이 패키지는 Node.js 기반으로 작동하며, MCP 서버를 빠르게 만들 수 있도록 도와줍니다.

아래에 FastMCP로 MCP 서버 만드는 전체 가이드를 정리해 드릴게요:

⸻

✅ 1. Node.js 설치

FastMCP는 Node.js 기반이므로 Node.js가 필요합니다.
	•	설치 확인:

node -v
npm -v


	•	없다면 https://nodejs.org/에서 설치

⸻

✅ 2. FastMCP 설치 및 초기화

💡 방법 1: npx로 바로 실행 (가장 간단)

npx @modelcontextprotocol/fastmcp

이렇게 실행하면 기본 템플릿이 다운로드되고, MCP 서버가 구동됩니다.

💡 방법 2: 프로젝트 디렉토리 생성 후 구성

mkdir my-fastmcp
cd my-fastmcp
npm init -y
npm install @modelcontextprotocol/fastmcp

package.json 안에 실행 스크립트를 추가:

"scripts": {
  "start": "fastmcp"
}

그리고 실행:

npm start


⸻

✅ 3. MCP 서버 설정 파일 생성 (mcp.config.ts 또는 .js)

기본적으로 다음과 같은 구조로 설정합니다:

// mcp.config.ts
import { defineConfig } from '@modelcontextprotocol/fastmcp'

export default defineConfig({
  name: 'my-fastmcp',
  description: 'My custom FastMCP server',
  icon: '🧠',
  actions: {
    sayHello: {
      description: 'Prints hello world',
      parameters: {
        name: { type: 'string', required: true }
      },
      handler: async ({ parameters }) => {
        return { message: `Hello, ${parameters.name}!` }
      }
    }
  }
})


⸻

✅ 4. 서버 실행

npx @modelcontextprotocol/fastmcp

혹은 프로젝트 내에서 실행:

npm run start


⸻

✅ 5. Dive나 Gemini CLI에서 연결 확인

~/.gemini/settings.json 혹은 Dive 설정에 다음과 같이 등록합니다:

{
  "mcpServers": {
    "my-fastmcp": {
      "command": "npx",
      "args": [
        "@modelcontextprotocol/fastmcp"
      ]
    }
  }
}


⸻

✅ 6. Custom Action 만들기 (예: 파일 실행)

예시: 특정 명령 실행

openApp: {
  description: 'Opens a local app',
  parameters: {
    path: { type: 'string', required: true }
  },
  handler: async ({ parameters }) => {
    const { exec } = require('child_process');
    exec(`start "" "${parameters.path}"`);
    return { status: 'launched', path: parameters.path };
  }
}


⸻

✅ 참고
	•	공식 GitHub: https://github.com/modelcontextprotocol/fastmcp
	•	FastMCP 문서: npx @modelcontextprotocol/fastmcp --help
	•	설정 파일 예시: .mcp.config.ts, package.json 등

⸻

필요하면 바로 쓸 수 있는 전체 템플릿도 만들어 드릴게요.
어떤 기능을 넣고 싶은 MCP인지 알려주시면 맞춤 템플릿 드릴게요.\



좋아요! 처음부터 차근차근 FastMCP로 MCP 서버 만드는 전체 과정을 자세히 설명해줄게요.
전혀 몰라도 이해할 수 있도록 하나씩 설명할게요.

⸻

✅ 먼저, MCP 서버란?

MCP는 Model-Centric Protocol의 약자예요. 쉽게 말하면:

AI 모델과 사람, 또는 다른 시스템이 소통할 수 있도록 만들어진 규칙(protocol) 이에요.

FastMCP는 이 MCP 서버를 빠르게 만들 수 있도록 도와주는 도구예요.
“서버”라는 건 네가 명령을 주면 거기에 반응하는 프로그램이라고 생각하면 돼요.
(예: “Hello!“라고 말하면 “Hi there!“라고 답하는 AI 같은 것)

⸻

✅ 사전 지식: npm, npx란?

1. npm이란?

npm은 Node.js라는 프로그래밍 환경에서 프로그램을 설치하고 관리해주는 도구예요.
npm은 마치 “앱스토어”라고 보면 돼요. 필요한 도구를 다운받고 설치해주는 명령어예요.
	•	예:

npm install 패키지이름

위 명령은 도구(패키지)를 설치해주는 거예요.

2. npx이란?

npx는 npm에서 설치하지 않고도 즉석에서 프로그램을 실행할 수 있게 해주는 도구예요.
	•	예:

npx cowsay hello

이건 “cowsay”라는 프로그램을 설치하지 않고 바로 실행시켜요.

⸻

✅ 전체 순서 다시 설명 (하나씩 따라오면 돼요)

⸻

🔷 1단계: Node.js 설치하기 (npm, npx 포함됨)

Node.js는 자바스크립트라는 언어를 컴퓨터에서 실행할 수 있도록 해주는 환경이에요.

설치 방법:
	1.	아래 링크에 접속:
👉 https://nodejs.org/
	2.	초록 버튼 (LTS 버전)을 눌러서 설치
	3.	설치가 끝나면 확인:

node -v
npm -v

	•	예:

v20.10.0
10.2.0



⸻

🔷 2단계: FastMCP 실행하기 (설치 안 해도 됨, npx로 실행)

터미널(명령창)을 열고 아래 명령어를 입력하세요:

npx @modelcontextprotocol/fastmcp

처음 실행하면 자동으로 필요한 파일들을 내려받고 MCP 서버를 실행합니다.

이때 기본으로 mcp.config.ts 파일을 만들게 돼요. 거기서 “어떤 명령을 수행할지” 정의할 수 있어요.

⸻

🔷 3단계: MCP 서버 만들기

예를 들어 “이름을 입력하면 인사해주는 MCP”를 만들고 싶다면 설정 파일을 이렇게 만듭니다:

// mcp.config.ts
import { defineConfig } from '@modelcontextprotocol/fastmcp'

export default defineConfig({
  name: 'hello-mcp',
  description: '이름을 받으면 인사하는 서버',
  icon: '👋',
  actions: {
    sayHello: {
      description: '인사 메시지를 돌려줍니다',
      parameters: {
        name: { type: 'string', required: true }
      },
      handler: async ({ parameters }) => {
        return { message: `안녕하세요, ${parameters.name}님!` }
      }
    }
  }
})

이 MCP는 sayHello라는 명령어를 처리할 수 있어요.
누군가 { name: "태경" }이라고 보내면 → 안녕하세요, 태경님! 이라고 답해요.

⸻

🔷 4단계: MCP 서버 실행하기

이제 터미널에서 다시 다음을 입력하면 MCP 서버가 실행돼요:

npx @modelcontextprotocol/fastmcp

혹은 파일에 저장해두고 아래처럼 실행할 수도 있어요:

node mcp.config.ts

(※ 이건 세팅에 따라 달라요. 일반적으로는 npx로 실행해요.)

⸻

🔷 5단계: Dive나 Gemini CLI에서 연결하기

~/.gemini/settings.json에 아래처럼 추가해서 Dive 같은 AI 도구가 네 서버에 연결할 수 있어요:

{
  "mcpServers": {
    "hello-mcp": {
      "command": "npx",
      "args": [
        "@modelcontextprotocol/fastmcp"
      ]
    }
  }
}

이렇게 하면 Dive에서 이 MCP 서버를 호출할 수 있어요.

⸻

🔚 요약

단계	설명
1단계	Node.js 설치 (npm, npx 포함됨)
2단계	FastMCP 실행 (npx로 빠르게)
3단계	명령을 처리하는 mcp.config.ts 만들기
4단계	MCP 서버 실행
5단계	다른 툴에서 이 서버를 불러서 명령 수행


⸻

원하는 기능이 있다면 예시 코드를 만들어줄게요.
예를 들어 “파일을 열기”, “스크린샷 찍기”, “문자 입력하기” 같은 것들로 만들어볼 수 있어요.
필요한 기능을 말해줘봐요!
