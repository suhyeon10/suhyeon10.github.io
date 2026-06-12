# 멀티계정 + 전송 가능한 Gmail MCP 셋업 (B안)

목표: `suhyeonjan10@gmail.com` + `moneynena1010@gmail.com` **두 메일함을
읽기/정리/전송**까지. 네이티브 커넥터는 1계정·초안전용이라 불가 →
**커스텀 멀티계정 Gmail MCP**(`3vening/gmail-mcp`)를 붙인다.

> ⚠️ 보안: 이 저장소는 **공개**다. `GOOGLE_CLIENT_ID/SECRET`·토큰을 **절대 커밋하지 말 것.**
> 시크릿은 환경변수나 비추적 `.mcp.local.json`(아래 .gitignore 처리)에만 둔다.

## 1. 사전 준비
- Python 3.10+, `uv` 패키지 매니저
```bash
git clone https://github.com/3vening/gmail-mcp.git
cd gmail-mcp/server
uv sync
```

## 2. Google Cloud OAuth 설정
1. **APIs & Services > Library**에서 **Gmail API** 사용 설정
2. **OAuth 동의 화면** 생성 → User type = **External**
3. 스코프 추가: `gmail.readonly, gmail.send, gmail.modify, gmail.labels, userinfo.email, openid`
   - `gmail.send` = 전송, `gmail.modify` = 라벨/아카이브
4. **테스트 사용자**에 연결할 두 주소 모두 추가:
   `suhyeonjan10@gmail.com`, `moneynena1010@gmail.com`
5. **OAuth 클라이언트(Desktop app)** 생성 → Client ID / Secret 발급

## 3. MCP 등록
### (A) Claude Code CLI/로컬
프로젝트 루트의 비추적 파일 `.mcp.local.json` 또는 `claude mcp add`로 등록:
```bash
claude mcp add gmail-mcp \
  --env GOOGLE_CLIENT_ID=$GOOGLE_CLIENT_ID \
  --env GOOGLE_CLIENT_SECRET=$GOOGLE_CLIENT_SECRET \
  -- uv run --directory /절대경로/gmail-mcp/server python server.py
```
설정 JSON 형식(참고 — `.mcp.json.example` 템플릿 제공):
```json
{
  "mcpServers": {
    "gmail-mcp": {
      "command": "uv",
      "args": ["run", "--directory", "/절대경로/gmail-mcp/server", "python", "server.py"],
      "env": {
        "GOOGLE_CLIENT_ID": "${GOOGLE_CLIENT_ID}",
        "GOOGLE_CLIENT_SECRET": "${GOOGLE_CLIENT_SECRET}"
      }
    }
  }
}
```

### (B) Claude Code 웹(원격 환경)
- 원격 컨테이너는 휘발성이라 로컬 파이썬 서버 상시 구동이 어렵다.
- 환경 설정의 **MCP/Connectors**에서 동일 커맨드·env로 등록하거나,
  세션 시작 시 위 서버를 띄우는 SessionStart 훅을 둔다(별도 작업).

## 4. 계정 추가 & 사용
Claude 재시작 후:
```
"Add my Gmail account suhyeonjan10@gmail.com"   → 브라우저 OAuth
"Add my Gmail account moneynena1010@gmail.com"  → 브라우저 OAuth
```
이후 `email-manager` 스킬이 두 계정을 인식한다.
- "moneynena 받은편지함 정리해줘" / "suhyeon 계정으로 회신 보내줘"

## 5. 이 MCP가 주는 도구
- 계정: `list_accounts, add_account, remove_account`
- 읽기: `search_emails, read_email, get_thread, save_attachment`
- 작성/전송: `draft_email, draft_reply, **send_draft**, discard_draft, list_drafts`
- 정리: `get_labels, archive_email, trash_email, mark_as_read/unread, apply_label, remove_label`

→ `send_draft`가 있으므로 `email-manager`의 "전송" 경로가 **실제 발송**으로 동작한다.

## 대안: Composio Gmail MCP
OAuth/호스팅을 Composio가 대행. 자체 구글 클라우드 설정이 부담되면 이 경로.
- 참고: https://composio.dev/toolkits/gmail/framework/claude-code

## 체크리스트
- [ ] gmail-mcp 클론 + `uv sync`
- [ ] Google Cloud OAuth(스코프 6종, 테스트 사용자 2명)
- [ ] Client ID/Secret를 **환경변수**로 (커밋 금지)
- [ ] MCP 등록 (CLI 또는 환경설정)
- [ ] 두 계정 add_account
- [ ] `email-manager`로 다중계정·전송 확인
