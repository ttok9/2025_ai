# FastMCP로 MCP 서버 만드는 법: 메뉴얼

이 메뉴얼은 FastMCP 라이브러리를 사용해 Model Context Protocol (MCP) 서버를 만드는 방법을 설명합니다. FastMCP는 MCP 서버와 클라이언트를 Pythonic하게 빌드할 수 있게 해주는 프레임워크입니다. (참조: [FastMCP GitHub](https://github.com/jlowin/fastmcp) 및 [공식 문서](https://gofastmcp.com))

이 내용을 PDF로 변환하려면:
1. 아래 Markdown 내용을 `manual.md` 파일로 저장하세요.
2. Node.js가 설치되어 있다면 터미널에서 다음 명령어를 실행하세요 (npx로 markdown-pdf 패키지를 임시 설치 및 실행):
   ```
   npx markdown-pdf manual.md -o manual.pdf
   ```
   - 이 명령어는 Markdown을 PDF로 간단히 변환합니다. (필요 시 CSS 스타일 추가: `--css "body { font-family: sans-serif; }"` 옵션 사용)
   - Node.js가 없으면 [Node.js 다운로드](https://nodejs.org) 후 설치하세요. 전체 과정 1-2분 소요.

## 1. 소개
FastMCP는 MCP 프로토콜을 구현하는 데 필요한 보일러플레이트를 처리해줍니다. MCP 서버는 LLM(대형 언어 모델)에 데이터(Resources), 기능(Tools), 프롬프트(Prompts)를 제공합니다.

- **왜 FastMCP?** 간단한 데코레이터로 도구를 등록하고, 서버를 실행할 수 있습니다. 복잡한 프로토콜 핸들링을 자동화합니다.
- **필수 요구사항:** Python 3.8 이상.

## 2. 설치
터미널에서 다음 명령어를 실행하세요:
```
pip install fastmcp
```
- 업그레이드 시: `pip install fastmcp --upgrade`
- (참고: FastMCP 1.0은 공식 MCP SDK에 통합되었으나, 2.0 버전은 확장된 기능을 제공합니다.)

## 3. 기본 MCP 서버 만들기
간단한 서버를 만드는 예시입니다. `server.py` 파일을 생성하고 아래 코드를 작성하세요.

```python
from fastmcp import FastMCP

# MCP 서버 인스턴스 생성 (서버 이름 지정)
mcp = FastMCP("Demo MCP Server 🚀")

# 도구(Tool) 등록: 함수를 @mcp.tool 데코레이터로 감싸기
@mcp.tool
def add(a: int, b: int) -> int:
    """두 숫자를 더합니다."""
    return a + b

# 서버 실행 (기본: STDIO 모드, 로컬 테스트용)
if __name__ == "__main__":
    mcp.run()
```

### 서버 실행 방법
- 로컬에서 실행:
  ```
  fastmcp run server.py
  ```
  또는 직접:
  ```
  python server.py
  ```
- HTTP 모드로 실행 (외부 접근 가능):
  ```python
  mcp.run(transport="http", host="0.0.0.0", port=8000, path="/mcp")
  ```
  - 접근 URL: http://localhost:8000/mcp

## 4. 고급 기능 추가
### Resources 추가
데이터를 노출하는 Resources를 정의합니다.
```python
@mcp.resource
def get_data() -> str:
    """샘플 데이터 반환"""
    return "Hello from MCP Resource!"
```

### Prompts 정의
재사용 가능한 프롬프트 템플릿.
```python
@mcp.prompt
def greeting(name: str) -> str:
    return f"Hello, {name}! This is a prompt template."
```

### 클라이언트 사용
서버와 상호작용하는 클라이언트 예시:
```python
from fastmcp import Client
import asyncio

async def main():
    async with Client("http://localhost:8000/mcp") as client:
        result = await client.call_tool("add", {"a": 1, "b": 2})
        print(result)  # 출력: 3

asyncio.run(main())
```

## 5. 배포 및 확장
- **인증 추가:** `mcp.run(auth="basic", username="user", password="pass")`
- **프록시/컴포지션:** 여러 서버를 연결.
- **테스트:** FastMCP 내장 테스트 도구 사용.
- **배포:** Docker나 클라우드에 HTTP 서버로 배포. 예: `fastmcp deploy server.py`
- 더 많은 예시: [공식 문서](https://gofastmcp.com/getting-started) 참조.

## 6. 문제 해결
- 연결 오류: 포트 확인 (기본 8000).
- 프로토콜 이슈: FastMCP가 자동 처리하나, 로그 확인.
- 추가 도움: [GitHub 이슈](https://github.com/jlowin/fastmcp/issues) 또는 [llms.txt 문서](https://gofastmcp.com/llms.txt).

이 메뉴얼은 FastMCP 2.0 기반 (2025년 기준). 업데이트 확인: `pip show fastmcp`
