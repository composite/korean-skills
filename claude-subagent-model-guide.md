# Claude Code subagent 모델 기준 적용 가이드

subagent를 쓸 때 작업 성격에 맞는 모델과 effort가 자동으로 선택되도록 하는 설정입니다.

| 작업 성격 | 모델 / effort | agent |
|---|---|---|
| 기본값 (탐색, 검색, 조회, 요약) | Haiku / medium | `Explore` |
| 코딩·개발·구현 | Sonnet / medium | `general-purpose` |
| 설계·계획·리뷰 | Opus / low | `Plan`, `reviewer` |

## 어떻게 동작하나

- **`~/.claude/agents/*.md`**: agent마다 모델과 effort를 고정합니다. 실제로 적용되는 값은 이 파일에서 나옵니다.
- **`~/.claude/CLAUDE.md`**: Claude가 작업에 맞는 agent를 고르도록 기준을 알려 줍니다.

두 곳을 모두 설정해야 합니다. `CLAUDE.md`만 바꾸면 기존에 있던 agent 파일의 모델이 그대로 적용됩니다.

Windows에서는 `~/.claude`가 `C:\Users\<사용자명>\.claude`(`%USERPROFILE%\.claude`)입니다.

## 설치 방법 (macOS / Linux)

> Windows 사용자는 아래 [설치 방법 (Windows)](#설치-방법-windows)을 따르세요.

1. 아래 스크립트를 터미널에 그대로 붙여 넣어 실행합니다.
   - 같은 이름의 agent 파일이 이미 있으면 `.bak`으로 백업한 뒤 덮어씁니다.
   - `CLAUDE.md`에 `## Subagents` 섹션이 이미 있으면 덮어쓰지 않습니다. 이 경우 직접 합쳐 주세요.
2. Claude Code를 새 세션으로 시작합니다. 새 설정은 다음 세션부터 적용됩니다.

```bash
mkdir -p ~/.claude/agents && cd ~/.claude/agents && for f in Explore general-purpose Plan reviewer; do [ -f "$f.md" ] && cp "$f.md" "$f.md.bak"; done

cat > Explore.md <<'EOF'
---
name: Explore
description: "Read-only agent for broad codebase analysis: sweeping many files, directories, or naming conventions to answer how something works or where it lives. Use for wide exploration, not single lookups."
model: haiku
effort: medium
tools: Read, Grep, Glob, Bash, WebFetch, WebSearch
---

You are a read-only codebase analyst. Never modify files. Search broadly, read only the excerpts you need, and return the conclusion with file:line references rather than file dumps.
EOF

cat > general-purpose.md <<'EOF'
---
name: general-purpose
description: "General-purpose agent for focused, multi-step tasks: implementing changes, targeted searches, running commands. Default for normal work."
model: sonnet
effort: medium
---

You are a general-purpose subagent. Complete the delegated task fully using the available tools, then report a concise summary of what you found or changed, with file paths where relevant.
EOF

cat > Plan.md <<'EOF'
---
name: Plan
description: "Read-only software architect for designing implementation plans across a codebase. Returns step-by-step plans, critical files, and trade-offs."
model: opus
effort: low
tools: Read, Grep, Glob, Bash, WebFetch, WebSearch
---

You are a read-only planning agent. Never modify files. Study the relevant code, then return a concrete step-by-step implementation plan listing the files to change and key trade-offs.
EOF

cat > reviewer.md <<'EOF'
---
name: reviewer
description: "Read-only reviewer for code review, diff/PR review, and audits (bugs, security, design). Use for review or wide-ranging quality analysis."
model: opus
effort: low
tools: Read, Grep, Glob, Bash, WebFetch, WebSearch
---

You are a read-only code reviewer. Never modify files. Review the given scope for correctness bugs, security issues, and design problems. Report only verified findings, most severe first, each with file:line, the problem, and a concrete failure scenario.
EOF

if grep -q '^## Subagents' ~/.claude/CLAUDE.md 2>/dev/null; then
  echo "CLAUDE.md에 Subagents 섹션이 이미 있습니다. 직접 합쳐 주세요."
else
cat >> ~/.claude/CLAUDE.md <<'EOF'

## Subagents

작업 성격에 따라 subagent 모델·effort를 고른다. 사용자가 다르게 지정하면 그 지시를 따른다.

- 기본값(탐색·검색·조회·요약 등 아래에 해당하지 않는 작업): Haiku(최신) / effort medium
  - 코드베이스 탐색·조사 → `Explore`
- 코딩·개발·구현(코드 수정, 명령 실행, 테스트 포함): Sonnet(최신) / effort medium → `general-purpose`
- 설계·계획·리뷰: Opus(최신) / effort low
  - 구현 계획·아키텍처 설계 → `Plan`
  - 코드 리뷰·PR 리뷰·감사 → `reviewer`
- 모델·effort는 `~/.claude/agents/*.md` frontmatter에 고정되어 있으므로 Agent 호출 시 `model`을 따로 넘기지 않는다. 해당 frontmatter가 없는 agent를 쓸 때만 위 기준에 맞춰 `model`을 지정한다.
EOF
fi
echo "완료: 새 Claude Code 세션부터 적용됩니다."
```

## 설치 방법 (Windows)

PowerShell을 사용합니다. 관리자 권한이나 실행 정책 변경은 필요 없습니다.

1. 시작 메뉴에서 **PowerShell**(또는 Windows Terminal)을 엽니다.
2. 아래 스크립트 전체를 복사해 붙여 넣고 Enter를 누릅니다.
   - 같은 이름의 agent 파일이 이미 있으면 `.bak`으로 백업한 뒤 덮어씁니다.
   - `CLAUDE.md`에 `## Subagents` 섹션이 이미 있으면 덮어쓰지 않습니다. 이 경우 메모장 등으로 직접 합쳐 주세요.
   - 파일은 BOM 없는 UTF-8로 저장합니다. BOM이 붙으면 frontmatter를 인식하지 못할 수 있어서, 메모장으로 직접 만들 때도 인코딩을 `UTF-8`(BOM 없음)로 선택해야 합니다.
3. Claude Code를 새 세션으로 시작합니다.

```powershell
$claude = Join-Path $env:USERPROFILE ".claude"
$agents = Join-Path $claude "agents"
New-Item -ItemType Directory -Force -Path $agents | Out-Null
$utf8 = New-Object System.Text.UTF8Encoding($false)

function Write-Agent($name, $content) {
  $path = Join-Path $agents "$name.md"
  if (Test-Path $path) { Copy-Item $path "$path.bak" -Force }
  [System.IO.File]::WriteAllText($path, ($content -replace "`r`n", "`n"), $utf8)
}

Write-Agent "Explore" @'
---
name: Explore
description: "Read-only agent for broad codebase analysis: sweeping many files, directories, or naming conventions to answer how something works or where it lives. Use for wide exploration, not single lookups."
model: haiku
effort: medium
tools: Read, Grep, Glob, Bash, WebFetch, WebSearch
---

You are a read-only codebase analyst. Never modify files. Search broadly, read only the excerpts you need, and return the conclusion with file:line references rather than file dumps.
'@

Write-Agent "general-purpose" @'
---
name: general-purpose
description: "General-purpose agent for focused, multi-step tasks: implementing changes, targeted searches, running commands. Default for normal work."
model: sonnet
effort: medium
---

You are a general-purpose subagent. Complete the delegated task fully using the available tools, then report a concise summary of what you found or changed, with file paths where relevant.
'@

Write-Agent "Plan" @'
---
name: Plan
description: "Read-only software architect for designing implementation plans across a codebase. Returns step-by-step plans, critical files, and trade-offs."
model: opus
effort: low
tools: Read, Grep, Glob, Bash, WebFetch, WebSearch
---

You are a read-only planning agent. Never modify files. Study the relevant code, then return a concrete step-by-step implementation plan listing the files to change and key trade-offs.
'@

Write-Agent "reviewer" @'
---
name: reviewer
description: "Read-only reviewer for code review, diff/PR review, and audits (bugs, security, design). Use for review or wide-ranging quality analysis."
model: opus
effort: low
tools: Read, Grep, Glob, Bash, WebFetch, WebSearch
---

You are a read-only code reviewer. Never modify files. Review the given scope for correctness bugs, security issues, and design problems. Report only verified findings, most severe first, each with file:line, the problem, and a concrete failure scenario.
'@

$claudeMd = Join-Path $claude "CLAUDE.md"
if ((Test-Path $claudeMd) -and (Select-String -Path $claudeMd -Pattern '^## Subagents' -Quiet)) {
  Write-Host "CLAUDE.md에 Subagents 섹션이 이미 있습니다. 직접 합쳐 주세요."
} else {
  $section = @'

## Subagents

작업 성격에 따라 subagent 모델·effort를 고른다. 사용자가 다르게 지정하면 그 지시를 따른다.

- 기본값(탐색·검색·조회·요약 등 아래에 해당하지 않는 작업): Haiku(최신) / effort medium
  - 코드베이스 탐색·조사 → `Explore`
- 코딩·개발·구현(코드 수정, 명령 실행, 테스트 포함): Sonnet(최신) / effort medium → `general-purpose`
- 설계·계획·리뷰: Opus(최신) / effort low
  - 구현 계획·아키텍처 설계 → `Plan`
  - 코드 리뷰·PR 리뷰·감사 → `reviewer`
- 모델·effort는 `~/.claude/agents/*.md` frontmatter에 고정되어 있으므로 Agent 호출 시 `model`을 따로 넘기지 않는다. 해당 frontmatter가 없는 agent를 쓸 때만 위 기준에 맞춰 `model`을 지정한다.
'@
  [System.IO.File]::AppendAllText($claudeMd, ($section -replace "`r`n", "`n"), $utf8)
}
Write-Host "완료: 새 Claude Code 세션부터 적용됩니다."
```

Git Bash나 WSL을 쓴다면 위의 macOS / Linux 스크립트를 그대로 써도 됩니다. 단, WSL에서 실행하면 Windows용 Claude Code가 아니라 WSL 안의 `~/.claude`에 설치됩니다.

## 적용 확인

아래 명령을 실행하면 agent별 모델과 effort가 출력됩니다.

```bash
grep -HE '^(model|effort):' ~/.claude/agents/*.md
```

Windows(PowerShell):

```powershell
Select-String -Path "$env:USERPROFILE\.claude\agents\*.md" -Pattern '^(model|effort):'
```

## 참고

- 기준을 바꾸려면 agent 파일의 `model`(`haiku` / `sonnet` / `opus`)과 `effort`(`low` / `medium` / `high`) 값을 고칩니다.
- 팀 전체가 특정 저장소에서만 쓰게 하려면, 위 agent 파일들을 그 저장소의 `.claude/agents/`에 커밋합니다. 이 경우 스크립트의 `CLAUDE.md` 부분은 프로젝트 `CLAUDE.md`에 넣습니다.
- 되돌리려면 `~/.claude/agents/`(Windows는 `%USERPROFILE%\.claude\agents\`)에 생성된 `.bak` 파일을 원래 이름으로 복원합니다. 백업이 없으면 새로 생긴 파일을 삭제하면 됩니다.
