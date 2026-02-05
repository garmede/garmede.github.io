# CLAUDE.md - GeoGon 블로그 AI 어시스턴트 가이드

이 문서는 GeoGon 블로그 저장소에서 작업하는 AI 어시스턴트를 위한 종합 가이드입니다.

## 프로젝트 개요

**GeoGon**은 컴퓨터 그래픽에 관한 개인 기술 블로그입니다. 셰이더, 렌더링, 게임 엔진, 테크니컬 아트 관련 주제를 다룹니다.

- **사이트 URL:** https://garmede.github.io
- **저자:** 차영곤
- **언어:** 한국어 (ko-KR)
- **프레임워크:** Jekyll + Chirpy 테마
- **호스팅:** GitHub Pages

## 기술 스택

| 구성요소 | 기술 |
|---------|------|
| 정적 사이트 생성기 | Jekyll 4.2.2 |
| 테마 | jekyll-theme-chirpy (~> 5.2.1) |
| 언어 | Ruby |
| 마크다운 프로세서 | Kramdown (GFM 지원) |
| 구문 강조 | Rouge |
| 댓글 시스템 | Giscus (GitHub Discussions 기반) |
| 분석 도구 | Google Analytics |
| CI/CD | GitHub Actions |

## 디렉토리 구조

```
garmede.github.io/
├── _config.yml              # Jekyll 메인 설정 파일
├── _posts/                  # 발행된 블로그 포스트 (YYYY-MM-DD-slug.md)
├── _drafts/                 # 초안 (날짜 접두사 불필요)
├── _tabs/                   # 정적 페이지 (about, archives, categories, tags)
├── _data/
│   ├── author.yml           # 저자 정보
│   ├── contact.yml          # 연락처/소셜 링크 설정
│   ├── share.yml            # 소셜 공유 옵션
│   ├── locales/             # 다국어 번역 (ko-KR이 기본)
│   └── assets/              # 에셋 로딩 설정
├── _sass/                   # SCSS 커스텀 스타일
├── _plugins/                # Jekyll Ruby 플러그인
│   └── posts-lastmod-hook.rb  # 포스트 수정일 자동 감지
├── assets/
│   ├── img/
│   │   ├── blog/            # 블로그 포스트 이미지 (.webp)
│   │   ├── tutorial/        # 튜토리얼 이미지 (.webp)
│   │   └── favicons/        # 사이트 파비콘
│   ├── fonts/               # 커스텀 폰트
│   └── lib/                 # 서브모듈: chirpy-static-assets
├── tools/
│   └── deploy.sh            # 배포 스크립트
├── .github/workflows/
│   └── pages.yml            # GitHub Actions CI/CD 워크플로우
├── Gemfile                  # Ruby 의존성
└── .editorconfig            # 코드 스타일 규칙
```

## 개발 워크플로우

### 로컬 개발 환경

```bash
# 의존성 설치
bundle install

# 초안 포함 개발 서버 시작
bundle exec jekyll serve --drafts

# 프로덕션 빌드
JEKYLL_ENV=production bundle exec jekyll build
```

### 새 콘텐츠 작성

#### 블로그 포스트

1. `_posts/` 폴더에 다음 형식으로 파일 생성:
   ```
   YYYY-MM-DD-제목-슬러그.md
   ```

2. 프론트매터 템플릿:
   ```yaml
   ---
   title: "포스트 제목"
   categories: [Blog]  # 또는 [Tutorial]
   tags: [태그1, 태그2]
   image:
     path: /assets/img/blog/이미지명.webp
   author: gon
   ---
   ```

3. 마크다운으로 한국어 콘텐츠 작성

#### 초안 작성

- `_drafts/` 폴더에 날짜 접두사 없이 저장
- 파일명: `제목-슬러그.md`
- `jekyll serve --drafts`로 미리보기

### 이미지 규칙

- **포맷:** WebP (웹 최적화를 위해 권장)
- **블로그 이미지:** `/assets/img/blog/`
- **튜토리얼 이미지:** `/assets/img/tutorial/`
- **정렬:** 가운데 정렬시 `{: .align-center}` 사용
- **캡션:** 이미지 다음 줄에 `*캡션 텍스트*` 추가

예시:
```markdown
![대체 텍스트](/assets/img/blog/image.webp){: .align-center}
*이미지 캡션*
```

## 콘텐츠 작성 규칙

### 언어 및 서식

- **기본 언어:** 한국어 (ko-KR)
- **약어 정의:** 포스트 끝에 `*[용어]: 정의` 형식으로 추가
- **강조:** 핵심 용어에 `**굵게**` 사용
- **인용/팁:** Chirpy 테마의 프롬프트 블록 사용

### 프롬프트 블록 (Chirpy 테마)

```markdown
> **제목**\
> 내용
{: .prompt-tip }

> 경고 내용
{: .prompt-warning }

> 정보 내용
{: .prompt-info }

> 위험 내용
{: .prompt-danger }
```

### 미디어 삽입

YouTube 영상:
```html
<iframe width="100%" height="450"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="YouTube video player" frameborder="0"
  allow="accelerometer; autoplay; clipboard-write;
  encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen></iframe>
```

## 설정 참조

### 주요 `_config.yml` 설정

| 설정 | 값 | 설명 |
|-----|---|------|
| `lang` | `ko-KR` | 한국어 |
| `timezone` | `Asia/Seoul` | 한국 시간대 |
| `theme_mode` | (비어있음) | 자동 라이트/다크 모드 |
| `comments.active` | `giscus` | GitHub Discussions 댓글 |
| `paginate` | `10` | 페이지당 포스트 수 |
| `toc` | `true` | 목차 활성화 |

### 저자 설정

`_data/author.yml`에 정의:
```yaml
gon:
  name: 차영곤
  github: garmede
  url: https://garmede.github.io
```

포스트에서 `author: gon`으로 참조

## 배포

### 자동 배포 (권장)

`main` 브랜치에 푸시하면 GitHub Actions 워크플로우가 실행:

1. 서브모듈 포함하여 코드 체크아웃
2. Ruby 3.1 설정 및 번들러 캐싱
3. Jekyll 사이트 빌드
4. GitHub Pages에 배포

### 수동 배포

배포 스크립트 사용 (CI 환경에서만 실행):
```bash
./tools/deploy.sh
```

옵션:
- `--dry-run`: 배포 없이 빌드만 실행
- `--config FILE`: 커스텀 설정 파일 사용

## 코드 스타일

`.editorconfig`에 정의:

- **문자 인코딩:** UTF-8
- **들여쓰기:** 스페이스 2칸
- **줄바꿈:** LF (Unix 스타일)
- **후행 공백:** 제거
- **마지막 줄 개행:** 필수

## Git 워크플로우

### 브랜치 전략

- `main`: 프로덕션 브랜치, 자동 배포 트리거
- 기능 브랜치: 새 콘텐츠나 변경사항용

### 커밋 메시지

한국어 또는 영어로 작성. 최근 예시:
- `테마 업데이트`
- `초안 추가`
- `엔진리뷰-유니티 추가`

## AI 어시스턴트 주의사항

### 해야 할 것

- 콘텐츠는 한국어로 작성
- 새 이미지는 WebP 포맷 사용
- 기존 프론트매터 패턴 따르기
- Chirpy 테마의 내장 스타일 클래스 활용
- 커밋 전 `bundle exec jekyll serve --drafts`로 테스트

### 하지 말아야 할 것

- 명시적 요청 없이 테마 설정 변경 금지
- 논의 없이 새 의존성 추가 금지
- `assets/lib/` 수정 금지 (git 서브모듈임)
- 주요 변경사항은 `main`에 직접 푸시 금지

### 자주 하는 작업

1. **새 블로그 포스트 추가:**
   - `_posts/`에 날짜 접두사 포함하여 파일 생성
   - title, categories, tags, image, author가 포함된 프론트매터 추가
   - 이미지는 `assets/img/blog/`에 저장

2. **기존 포스트 수정:**
   - `posts-lastmod-hook.rb` 플러그인이 수정일 자동 업데이트
   - 수동으로 `last_modified_at` 설정 불필요

3. **초안 미리보기:**
   - `_drafts/` 폴더에 저장
   - `bundle exec jekyll serve --drafts` 실행

4. **사이트 설정 변경:**
   - `_config.yml` 수정
   - Jekyll 서버 재시작하여 적용

## 관련 자료

- [Jekyll 공식 문서](https://jekyllrb.com/docs/)
- [Chirpy 테마 위키](https://github.com/cotes2020/jekyll-theme-chirpy/wiki)
- [Kramdown 문법](https://kramdown.gettalong.org/syntax.html)
