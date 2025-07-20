기존에 trace32를 제어하는 함수들 (예: lauterbach-trace32-rcl 라이브러리 기반)이 이미 있다면, fastmcp를 사용해 이 함수들을 MCP 서버의 'tool'로 모아서 등록할 수 있어. fastmcp는 함수를 @mcp.tool 데코레이터로 간단히 래핑해 MCP 프로토콜을 지원하는 서버로 변환해줘. 이렇게 하면 여러 함수를 하나의 서버에 통합할 수 있고, LLM이나 클라이언트가 이 tool들을 호출해 trace32를 제어할 수 있게 돼.

아래는 단계별 가이드야. fastmcp 2.0 버전 (2025년 7월 기준 최신)을 기반으로 설명할게.<grok:render card_id="f9e5f2" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render> TRACE32 Remote API가 활성화된 상태라고 가정해 (config.t32에 RCL=NETTCP, PORT=20000 설정).

### 1. 환경 설정 및 라이브러리 설치
- Python 3.8+ 환경.
- 터미널에서 설치:
  ```
  pip install fastmcp lauterbach-trace32-rcl
  ```
  - fastmcp: MCP 서버 빌드.<grok:render card_id="8aeddf" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render>
  - lauterbach-trace32-rcl: trace32 제어 라이브러리.

- TRACE32 연결 준비: RemoteControl 인스턴스 생성 (기존 함수에서 이미 사용 중일 거야).

### 2. 기존 함수들을 tool로 모아서 등록
기존 trace32 제어 함수들을 @mcp.tool 데코레이터로 감싸서 등록해. 여러 함수를 하나의 FastMCP 인스턴스에 추가할 수 있어. 함수의 docstring과 타입 힌트가 자동으로 MCP 스키마로 변환돼.

예시 코드 (server.py 파일로 저장):
```python
from fastmcp import FastMCP
from lauterbach.trace32.rcl import RemoteControl  # trace32 라이브러리

# TRACE32 연결 (공유 인스턴스, 기존 함수에서 재사용 가능)
t32 = RemoteControl(host='localhost', port=20000, packlen=1024)
t32.connect()

mcp = FastMCP("Trace32 MCP Server 🚀")  # MCP 서버 인스턴스

# 기존 함수 1: 명령 실행 (예시로 래핑)
@mcp.tool
def execute_trace32_command(command: str) -> str:
    """TRACE32 PRACTICE 명령 실행하고 결과를 반환.
    :param command: 실행할 명령 (e.g., 'Data.List')
    :return: 결과 또는 에러 메시지."""
    try:
        t32.api.T32_Cmd(command.encode('utf-8'))
        state = 1
        while state != 0:
            state = t32.api.T32_GetPracticeState()
        error_code = bytearray(4)
        message = bytearray(256)
        t32.api.T32_GetMessage(message, error_code)
        result = message.decode('utf-8').strip()
        return f"Error: {result}" if error_code[0] != 0 else result
    except Exception as e:
        return f"Failed: {str(e)}"

# 기존 함수 2: 메모리 읽기 (추가 예시)
@mcp.tool
def read_memory(address: int, size: int) -> str:
    """메모리 읽기.
    :param address: 시작 주소 (hex or int)
    :param size: 읽을 바이트 수
    :return: 메모리 데이터 (hex string)"""
    try:
        buffer = bytearray(size)
        t32.api.T32_ReadMemory(address, 0, buffer, size)  # 0은 access mode
        return buffer.hex()
    except Exception as e:
        return f"Failed: {str(e)}"

# 기존 함수 3: 변수 값 설정 (추가 예시)
@mcp.tool
def set_variable(var_name: str, value: str) -> str:
    """변수 값 설정.
    :param var_name: 변수 이름
    :param value: 설정할 값
    :return: 성공 메시지 또는 에러"""
    try:
        command = f"Var.Set {var_name}={value}"
        return execute_trace32_command(command)  # 다른 tool 호출 가능 (내부 재사용)
    except Exception as e:
        return f"Failed: {str(e)}"

if __name__ == "__main__":
    mcp.run(transport="http", host="127.0.0.1", port=8000, path="/mcp")  # HTTP 서버 실행
```

- **설명**:
  - `FastMCP("Name")`: 서버 생성.<grok:render card_id="48bad8" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render>
  - `@mcp.tool`: 각 함수를 tool로 등록. 기존 함수를 그대로 래핑하거나, 필요 시 수정 (e.g., 에러 처리 추가).
  - 여러 tool 등록: 위처럼 함수를 추가하면 돼. 내부에서 다른 tool 호출도 가능 (e.g., set_variable에서 execute_trace32_command 사용).
  - 비동기 함수라면 `@mcp.tool async def ...`로 사용.<grok:render card_id="9335be" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render>

### 3. 서버 실행
- 코드 실행:
  ```
  python server.py
  ```
  - 기본: STDIO 모드 (로컬 테스트).
  - HTTP 모드 (위 코드처럼): http://localhost:8000/mcp에서 접근.
- CLI로 실행: `fastmcp run server.py`<grok:render card_id="f808db" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render>

### 4. 테스트 및 사용
- 클라이언트로 테스트 (fastmcp Client 사용):
  ```python
  from fastmcp import Client
  import asyncio

  async def test():
      async with Client("http://localhost:8000/mcp") as client:
          tools = await client.list_tools()  # tool 목록 확인
          print(tools)
          
          # tool 호출 예시
          result = await client.call_tool("execute_trace32_command", {"command": "Data.List"})
          print(result.text)

  asyncio.run(test())
  ```
- LLM 통합: MCP 클라이언트나 LLM 프레임워크 (e.g., LangChain)에서 이 서버 URL을 지정해 tool 호출.

### 추가 팁
- **Context 사용**: 함수에 `ctx: Context` 파라미터 추가하면, 로그나 프로그레스 보고 가능 (e.g., `await ctx.info("Executing...")`).<grok:render card_id="a8392b" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render>
- **에러 처리**: 함수 내에서 예외를 잘 잡아 MCP 클라이언트에 문자열로 반환.
- **확장**: 더 많은 함수 추가하거나, resource/prompt도 @mcp.resource나 @mcp.prompt로 등록 가능.
- **문제 해결**: 연결 오류 시 TRACE32 포트 확인. fastmcp 로그로 디버깅.<grok:render card_id="1ff9bc" card_type="citation_card" type="render_inline_citation">
<argument name="citation_id">0</argument>
</grok:render>

이 방법으로 기존 함수들을 쉽게 MCP 서버로 통합할 수 있어. 더 구체적인 함수 예시나 수정이 필요하면 알려줘!
