# CLAUDE.md — cha-interview-bot-liveavatar

이 파일은 Claude Code가 본 저장소에서 작업할 때 참조하는 지침입니다.

## 프로젝트 개요

**cha-interview-bot-liveavatar** 는 차의과학대학교 미래융합대학 신입생을 위한
전공 선택·진로 상담 멀티모달 아바타 챗봇의 **LiveAvatar 마이그레이션 버전**
입니다.

- 원본 시스템: `C:\dev\cha-interview-bot` (HeyGen InteractiveAvatar 기반,
  운영 종료 일정 사전 공지)
- 본 저장소: HeyGen InteractiveAvatar → **LiveAvatar (LiveKit 기반)** 로
  마이그레이션한 차세대 버전
- 원본 시스템은 논문 reproducibility 보존을 위해 그대로 유지하며,
  본 저장소에서 자유롭게 마이그레이션 작업을 진행합니다

## 마이그레이션 범위

원본 repo에서 HeyGen 의존이 있는 4개 파일이 본 저장소의 마이그레이션 대상:

| 원본 위치                       | 역할                                  | LiveAvatar 대응 작업 |
|---------------------------------|---------------------------------------|----------------------|
| `src/App.jsx`                   | 클라이언트 측 아바타 SDK 사용          | LiveKit + LiveAvatar SDK로 교체 |
| `api/heygen-token.js`           | HeyGen 토큰 발급 프록시                | LiveAvatar 토큰 발급 엔드포인트로 교체 |
| `api/heygen-proxy.js`           | HeyGen API 프록시                      | LiveAvatar 프록시로 교체 |
| `public/index.html`             | HeyGen SDK 스크립트 임베드             | LiveAvatar SDK 스크립트로 교체 |

마이그레이션과 무관한 부분(RAG 파이프라인·DB·설문·UI 컴포넌트·STT/TTS
후처리)은 원본 동작과 동일하게 유지하는 것이 1차 목표입니다.

## 기술 스택 (예상)

원본 동일하게 시작 후 필요 시 변경:

- **Frontend**: React 19 + Vite
- **Avatar**: LiveAvatar (LiveKit 기반) — *원본은 HeyGen InteractiveAvatar*
- **LLM**: Gemma 4 (Ollama 런타임, 자체 GPU)
- **Embedding**: BGE-M3
- **RAG**: 131 청크 (C대학 11개 전공 공식 소개 영상 기반 + 학과 컨택)
- **Backend**: Node.js (api/), 학교 서버 (인증·로그·설문)
- **DB**: 학교 서버 측 보관

## 보안 정책 (엄격)

본 저장소에서는 다음을 **절대로** 하지 않습니다:

1. **시크릿 평문 커밋 금지** — API 키·토큰·DB 비밀번호·자격증명·SSH 키 등은
   `.env*` 파일에 두고 `.gitignore`로 차단. 문서·코드에 평문 노출 금지.
2. **GitHub push는 사용자 명시 승인 후에만** — Claude가 임의로 `git push`
   실행 금지. 원격 저장소 추가(`git remote add`)·푸시(`git push`) 모두
   사용자가 직접 명령했을 때만 진행.
3. **history rewrite는 사용자 명시 승인 필요** — `git reset --hard`,
   `--force` push, `git filter-branch`, BFG 등 이력 변조성 작업은
   사용자가 명시 요청한 경우에만.
4. **학내 데이터 커밋 금지** — 학생 응답 데이터·개인정보·학교 서버 인증
   정보는 본 저장소에 절대 포함하지 않음. RAG 코퍼스 중 학내 사적
   데이터(학과별 컨택·내부 문서)도 별도 경로에서만 다루며 `.gitignore`로
   차단.
5. **운영 비밀 문서 분리** — `.htaccess`, `nginx.conf` 등 서버 설정은
   `.gitignore` 패턴으로 차단되어 있음.

## 작업 원칙

- **원본 보존**: 원본 `cha-interview-bot`은 변경하지 않음. 본 저장소에서만
  작업.
- **점진적 마이그레이션**: HeyGen 의존 4개 파일 단위로 한 번에 한 파일씩
  교체. 각 단계마다 동작 확인.
- **Korean-first**: 본 시스템의 사용자(C대학 신입생)는 한국어 사용자.
  UI·프롬프트·후처리는 한국어 우선, 영문 SDK 라벨은 보조.
- **문서 작업물은 git에 올리지 않음** — 논문 작업물·내부 문서는
  `docs/paper-private/`, `docs/INTERNAL*` 패턴으로 차단.
- **Reference 사실 검증** — 학술 인용 추가 시 Crossref·arXiv API 등으로
  사실 검증 후 인용.

## 주요 명령

```bash
# 개발 서버 (원본 동일 — 변경 시 갱신)
npm run dev

# 빌드
npm run build

# 미리보기
npm run preview
```

## 참고

- 원본 repo: `C:\dev\cha-interview-bot`
- 원본 GitHub: `github.com/sungbongju/cha-interview-bot`
- 본 저장소 GitHub remote: **아직 미설정** (사용자 명시 승인 후 연결)
