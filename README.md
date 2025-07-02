# Developmentudy Blog

Hugo와 PaperMod 테마를 사용한 개인 개발 블로그입니다.

## 🚀 기술 스택

- **정적 사이트 생성기**: Hugo v0.147.9
- **테마**: PaperMod
- **배포**: GitHub Pages
- **CI/CD**: GitHub Actions

## 📝 블로그 내용

- 개발 학습 내용
- 프로젝트 후기
- 문제 해결 과정
- 기술 리뷰

## 🛠️ 로컬 개발

### 필수 요구사항

- Hugo Extended 버전 (v0.147.9 이상)
- Git

### 설치 및 실행

```bash
# 저장소 클론
git clone https://github.com/SummerRiversound/hugo-developmentudy-blog.git
cd hugo-developmentudy-blog

# 서브모듈 업데이트 (테마 포함)
git submodule update --init --recursive

# 로컬 서버 실행
hugo server --buildDrafts --bind 0.0.0.0
```

브라우저에서 `http://localhost:1313`으로 접속하여 확인할 수 있습니다.

### 새 포스트 작성

```bash
# 새 포스트 생성
hugo new content posts/새-포스트-제목.md

# 새 페이지 생성
hugo new content about.md
```

## 🚀 배포

이 블로그는 GitHub Actions를 통해 자동 배포됩니다.

- `master` 브랜치에 푸시하면 자동으로 빌드 및 배포
- GitHub Pages에서 `https://summerriversound.github.io/hugo-developmentudy-blog/`로 접속 가능

## 📁 프로젝트 구조

```
hugo-developmentudy-blog/
├── content/           # 블로그 콘텐츠
│   ├── posts/        # 블로그 포스트
│   └── about.md      # About 페이지
├── themes/           # Hugo 테마
│   └── PaperMod/     # PaperMod 테마
├── layouts/          # 커스텀 레이아웃 (선택사항)
├── static/           # 정적 파일 (이미지, CSS, JS 등)
├── hugo.toml         # Hugo 설정 파일
└── .github/          # GitHub Actions 워크플로우
    └── workflows/
        └── deploy.yml
```

## 🎨 커스터마이징

### 테마 설정

`hugo.toml` 파일에서 PaperMod 테마의 다양한 옵션을 설정할 수 있습니다:

- 메뉴 구성
- 소셜 미디어 링크
- 색상 테마
- 메타데이터

### 새 포스트 작성 시 프론트매터

```yaml
+++
title = "포스트 제목"
date = 2025-07-02T22:21:17+09:00
draft = false
description = "포스트 설명"
tags = ['태그1', '태그2']
categories = ['카테고리']
+++
```

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다.

## 🤝 기여

버그 리포트나 기능 제안은 언제든 환영합니다!

---

**개발자**: Haeun
**GitHub**: [@SummerRiversound](https://github.com/SummerRiversound)