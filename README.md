# eeoon.github.io

김영언(Youngeon Kim)의 CV · 포트폴리오 · 논문 리뷰 사이트. [al-folio](https://github.com/alshedivat/al-folio) v1 테마(Jekyll) 기반.

## 구조

| 경로 | 내용 |
| --- | --- |
| `_pages/about.md` | 첫 화면(소개·핵심 역량·최근 소식·선정 논문·최근 글) |
| `_projects/*.md` | 프로젝트 카드. `category: company`(KETI) / `personal`(입사 전). `importance`가 낮을수록 앞 |
| `_bibliography/papers.bib` | 논문·특허. `selected={true}`면 첫 화면에 노출, `preview`는 `assets/img/publication_preview/` |
| `_posts/*.md` | 블로그. `categories: paper-review`(논문 리뷰) / `tech-note`(기술 노트) |
| `_news/*.md` | 타임라인(첫 화면 news 영역) |
| `_data/cv.yml` | CV 페이지 데이터 (RenderCV 유사 형식, `Patents`는 generic 섹션) |
| `_data/socials.yml` | 이메일·GitHub 등 소셜 아이콘 |
| `_config.yml` | 사이트 설정 (`url`, `lang: ko`, scholar 이름 등) |
| `assets/img/` | `prof_pic.jpg`, `projects/<slug>/`, `publication_preview/`, `blog/` |

## 로컬 빌드

```bash
bundle install
bundle exec jekyll serve --livereload   # http://localhost:4000
```

Ruby 3.3 이상, ImageMagick(반응형 이미지) 권장. Docker를 쓰려면 `docker compose up`.

## 배포

`main`에 push하면 `.github/workflows/deploy.yml`이 사이트를 빌드해 `gh-pages` 브랜치에 올린다.
저장소 **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `gh-pages` / (root)** 로 설정해야 한다.

## 글 추가

- 논문 리뷰: `_posts/YYYY-MM-DD-slug.md`, `categories: paper-review`, 태그는 소문자 영문.
- 프로젝트: `_projects/slug.md`, 이미지는 `assets/img/projects/slug/`에 두고 `{% include figure.liquid path="..." %}`로 삽입.
- 논문: `papers.bib`에 항목 추가 + `assets/img/publication_preview/<name>.png`.
