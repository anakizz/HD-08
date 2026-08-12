# Reusable Supabase Membership Page

구성 파일

- `index.html` : CSS/JS가 모두 포함된 단일 회원가입·로그인·내 정보 페이지
- `supabase_membership.sql` : Supabase SQL Editor에서 1회 실행
- `.github/workflows/deploy-pages.yml` : GitHub Repository Variables를 주입해 Pages 배포
- `.env.example` : 변수 이름 참고용

## 1. Supabase SQL 실행

Supabase Dashboard → SQL Editor → `supabase_membership.sql` 전체 실행

생성되는 테이블:

`public.site_member_profiles`

기존 `public.profiles`를 변경하지 않으므로 다른 프로젝트 데이터와 충돌 가능성을 줄였습니다.

## 2. Supabase Auth 설정

Authentication에서 다음을 확인합니다.

- Email provider 활성화
- Confirm email 활성화 권장
- Password Security 최소 길이 10 이상
- Required characters: digits + lowercase + uppercase + symbols
- Site URL / Redirect URLs에 실제 GitHub Pages 또는 서비스 주소 등록

## 3. GitHub에 올리는 최소 파일

루트:

`index.html`

선택:

`.github/workflows/deploy-pages.yml`

GitHub Pages를 기본 branch 직접 배포 방식으로 쓸 경우 `index.html`만으로도 동작합니다.

## 4. GitHub Repository Variables 사용

Repository → Settings → Secrets and variables → Actions → Variables 에 아래 2개를 등록합니다.

- `SUPABASE_URL`
- `SUPABASE_PUBLISHABLE_KEY`

이 값은 `deploy-pages.yml`이 배포용 `_site/index.html`을 만들 때 주입합니다.

중요: GitHub Pages는 정적 웹이므로 브라우저에서 `process.env` 같은 서버 환경변수를 직접 읽을 수 없습니다. 따라서 빌드/배포 단계에서 정적 파일에 주입해야 합니다.

## 5. 새 프로젝트에 재사용할 때

`index.html` 상단 JS 설정에서 아래 값만 우선 변경하세요.

- `APP_NAME`
- `SITE_CODE`

여러 사이트가 동일 Supabase Auth 프로젝트를 사용하면 이메일 계정은 Auth 기준으로 공통 관리됩니다.

## 6. 이메일 검사 방식

- 일반 이메일 형식 검사
- 흔한 도메인 오타 경고
- 일부 대표 일회용 이메일 도메인 차단
- test/demo/fake 같은 의심 로컬파트는 추가 확인
- 최종적으로 Supabase 이메일 인증을 통해 실제 메일 수신 여부 확인

클라이언트만으로 전 세계 모든 일회용/허위 이메일을 완벽히 판별할 수는 없습니다.

## 7. 보안

브라우저에는 `sb_publishable_...` 키만 사용합니다.

다음 키는 절대로 `index.html`에 넣지 마세요.

- `sb_secret_...`
- `service_role`

실제 데이터 보호의 핵심은 SQL에 포함된 RLS 정책입니다.

## 8. 전체 관리자 회원목록이 필요한 경우

현재 페이지는 회원 본인의 정보만 조회/수정하도록 설계했습니다.

전체 회원관리 기능은 별도의 관리자 권한 정책 또는 서버/Edge Function을 추가해 구현해야 합니다. 브라우저에 secret/service_role 키를 넣어 전체 회원을 조회하는 방식은 사용하지 마세요.
