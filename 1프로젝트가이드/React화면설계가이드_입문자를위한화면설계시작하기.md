# React 화면 설계 가이드 — 입문자를 위한 화면 설계 시작하기

> "화면 설계를 어떻게 시작해야 할지 전혀 모르겠다"는 분을 위한 가이드입니다.
> Figma 같은 도구 없이, 종이와 생각만으로도 시작할 수 있는 방법을 알려드립니다.

---

## 목차

1. [화면 설계가 왜 필요한가?](#1-화면-설계가-왜-필요한가)
2. [화면 설계 7단계 프로세스](#2-화면-설계-7단계-프로세스)
3. [STEP 1 — 페이지 목록 만들기](#3-step-1--페이지-목록-만들기)
4. [STEP 2 — 손으로 그리는 와이어프레임](#4-step-2--손으로-그리는-와이어프레임)
5. [STEP 3 — 와이어프레임에서 컴포넌트 목록 뽑기](#5-step-3--와이어프레임에서-컴포넌트-목록-뽑기)
6. [STEP 4 — 디자인 토큰 결정하기](#6-step-4--디자인-토큰-결정하기)
7. [STEP 5 — UI 라이브러리 선택 및 설치](#7-step-5--ui-라이브러리-선택-및-설치)
8. [STEP 6 — 레이아웃(뼈대) 먼저 코딩하기](#8-step-6--레이아웃뼈대-먼저-코딩하기)
9. [STEP 7 — 정적 화면 → 동적 화면으로 완성하기](#9-step-7--정적-화면--동적-화면으로-완성하기)
10. [TailwindCSS → UI 라이브러리 전환 가이드](#10-tailwindcss--ui-라이브러리-전환-가이드)
11. [shadcn/ui 실전 가이드 (추천)](#11-shadcnui-실전-가이드-추천)
12. [MUI(Material-UI) 실전 가이드](#12-muimaterial-ui-실전-가이드)
13. [레이아웃 패턴 참고](#13-레이아웃-패턴-참고)
14. [자주 묻는 질문 (FAQ)](#14-자주-묻는-질문-faq)

---

## 1. 화면 설계가 왜 필요한가?

화면 설계 없이 코딩을 시작하면 이런 문제가 생깁니다:

```
코딩 먼저 시작했을 때:
  → 버튼 위치가 어색해서 전체 레이아웃 다시 짬
  → "이 데이터를 여기에 보여줄 걸 그랬다" → 컴포넌트 재구성
  → 어떤 컴포넌트가 필요한지 몰라서 코드가 한 파일에 몰림
  → 결국 처음부터 다시 작성

화면 설계 후 코딩:
  → 어떤 페이지가 필요한지 미리 파악
  → 컴포넌트를 어떻게 나눌지 미리 결정
  → 어떤 데이터(API)가 필요한지 파악 가능
  → 코딩이 훨씬 빠르고 체계적
```

**화면 설계의 목표:** 코드를 작성하기 전에 "머릿속에 있는 것"을 문서화하는 것

---

## 2. 화면 설계 7단계 프로세스

```
STEP 1          STEP 2           STEP 3             STEP 4
페이지 목록  →  와이어프레임  →  컴포넌트 목록  →  디자인 토큰
(어떤 화면이     (각 화면의        (박스 → JSX          (색상, 폰트,
 필요한가?)       대략적 구조)       파일 이름 결정)        간격 기준)

30분             1~2시간          30분                 30분

     STEP 5             STEP 6               STEP 7
  UI 라이브러리  →  레이아웃 뼈대  →  정적 → 동적 완성
  (설치 & 선택)     (빈 컴포넌트         (하드코딩 먼저
                     먼저 연결)            → API 연결)

  30분               1~2시간             나머지 시간
```

> **핵심 원칙:** 와이어프레임을 그린 다음에 바로 코딩하지 마세요.
> STEP 3 → 4를 거치면 코딩이 훨씬 빨라지고 실수가 줄어듭니다.

---

## 3. STEP 1 — 페이지 목록 만들기

### 3.1 "어떤 화면이 필요한가?" 질문하기

프로젝트를 시작할 때 먼저 이 질문에 답하세요:

```
사용자가 무엇을 할 수 있어야 하는가?

예시: 온라인 도서관 시스템
  ✅ 회원가입을 한다
  ✅ 로그인한다
  ✅ 도서 목록을 검색하고 본다
  ✅ 도서 상세 정보를 확인한다
  ✅ 도서를 대출 신청한다
  ✅ 내 대출 이력을 확인한다
  ✅ 도서를 반납한다
  ✅ 로그아웃한다
```

### 3.2 URL 구조(사이트맵) 작성

할 수 있는 것들을 URL로 연결합니다:

```
React 화면 (사용자 영역):
├── /login              → 로그인 페이지
├── /register           → 회원가입 페이지
├── /books              → 도서 목록 (검색 + 대출 신청)
├── /books/:id          → 도서 상세 페이지
└── /loans              → 내 대출 이력 + 반납

Thymeleaf 화면 (관리자 영역):
├── /admin/login            → 관리자 로그인
├── /admin/dashboard        → 대시보드 (통계)
├── /admin/books            → 도서 관리 (등록/수정/삭제)
├── /admin/loans            → 대출 현황 (연체 포함)
└── /admin/members          → 회원 목록
```

### 3.3 페이지 설계서 표 작성

| 페이지명 | URL | 주요 기능 | 필요한 API |
|---------|-----|-----------|-----------|
| 로그인 | /login | 이메일/비밀번호 입력, 로그인 | POST /auth/login |
| 회원가입 | /register | 이름/이메일/비밀번호 입력 | POST /auth/register |
| 도서 목록 | /books | 도서 검색, 목록 조회, 대출 신청 | GET /api/books, POST /api/loans |
| 도서 상세 | /books/:id | 도서 정보 상세 조회, 대출 신청 버튼 | GET /api/books/{id} |
| 내 대출 이력 | /loans | 대출 중 목록, 반납 처리 | GET /api/loans/my, PUT /api/loans/{id} |

---

## 4. STEP 2 — 손으로 그리는 와이어프레임

### 4.1 와이어프레임이란?

와이어프레임은 **화면의 뼈대**입니다. 예쁠 필요 없고, 네모와 선으로 그리면 됩니다.

```
나쁜 와이어프레임 (X):
  "그냥 머릿속으로 생각하면 됨"

좋은 와이어프레임 (O):
  종이에 네모박스로 대략적인 레이아웃만 그림
  디자인은 신경 쓰지 않음
```

### 4.2 텍스트 와이어프레임 — 코드 없이 그리기

종이 없어도 메모장에 아래처럼 그릴 수 있습니다:

**도서 목록 페이지 와이어프레임:**
```
┌─────────────────────────────────────────────────────┐
│  [📚 도서관]  도서목록 | 내 대출이력      hong@  [로그아웃]  │  ← Header + Nav
├─────────────────────────────────────────────────────┤
│                                                     │
│  도서 목록                                           │  ← 페이지 제목
│                                                     │
│  [검색어(제목/저자)______] [카테고리 ▼] [검색]          │  ← 검색 + 필터 영역
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │ ID │ 제목       │ 저자  │ 카테고리│ 재고│ 대출 │   │  ← 테이블 헤더
│  ├──────────────────────────────────────────────┤   │
│  │  1 │ 채식주의자  │ 한강  │ 소설   │  3 │[대출]│   │  ← 데이터 행
│  │  2 │ 클린코드    │ 마틴  │ IT     │  0 │ 없음 │   │  ← 재고 없음
│  │  3 │ 파친코     │ 이민진 │ 소설   │  2 │[대출]│   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  [< 이전]  1  2  3  [다음 >]                         │  ← 페이징
│                                                     │
└─────────────────────────────────────────────────────┘

대출 신청 폼 (모달):
┌──────────────────────────────┐
│  대출 신청                    │
│  제목: 채식주의자 (읽기전용)    │
│  저자: 한강        (읽기전용)  │
│  대출 기간: 14일              │
│  반납 예정일: 2026-04-09      │
│          [취소] [대출 신청]    │
└──────────────────────────────┘
```

**내 대출 이력 페이지 와이어프레임:**
```
┌─────────────────────────────────────────────────────┐
│  [📚 도서관]  도서목록 | 내 대출이력      hong@  [로그아웃]  │
├─────────────────────────────────────────────────────┤
│                                                     │
│  내 대출 이력                                         │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │ 도서명     │ 대출일    │ 반납예정일 │ 상태    │   │
│  ├──────────────────────────────────────────────┤   │
│  │ 채식주의자 │ 2026-03-26│ 2026-04-09│[반납]   │   │  ← 대출중
│  │ 클린코드   │ 2026-02-10│ 2026-02-24│반납완료 │   │  ← 반납완료
│  └──────────────────────────────────────────────┘   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### 4.3 각 페이지별 와이어프레임 작성 팁

| 페이지 유형 | 필수 포함 요소 |
|------------|--------------|
| **목록 페이지** | 검색/필터 영역, 테이블/카드 목록, 페이지네이션, [추가] 버튼 |
| **등록/수정 폼** | 입력 필드 목록, 유효성 오류 표시 위치, [취소]/[저장] 버튼 |
| **상세 페이지** | 데이터 표시 영역, [수정]/[삭제] 버튼, [목록으로] 링크 |
| **로그인 페이지** | 이메일/비밀번호 입력, [로그인] 버튼, [회원가입] 링크 |
| **대시보드** | 통계 카드들, 최근 데이터 목록, 바로가기 메뉴 |

---

## 5. STEP 3 — 와이어프레임에서 컴포넌트 목록 뽑기

> 와이어프레임을 그렸다면, 그 다음은 "박스 하나 = 파일 하나" 규칙으로 컴포넌트 목록을 만듭니다.
> 코드를 쓰기 전에 이 목록이 있으면 뭘 만들어야 할지 명확해집니다.

### 5.1 박스 → JSX 파일 이름 짓기

와이어프레임의 각 박스(영역)에 JSX 파일 이름을 붙이는 작업입니다.

```
┌─────────────────────────────────────────────────────┐
│  [📚 도서관]  도서목록 | 내 대출이력    hong@  [로그아웃]  │  ← Header.jsx
├─────────────────────────────────────────────────────┤
│                                                     │
│  도서 목록                                           │  ← BookSection.jsx (페이지 전체)
│                                                     │
│  [검색어(제목/저자)______] [카테고리 ▼] [검색]          │  ← BookSearch.jsx
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │ ID │ 제목    │ 저자  │ 카테고리 │ 재고 │ 대출 │   │  ← BookList.jsx (테이블 전체)
│  ├──────────────────────────────────────────────┤   │  ← BookListRow.jsx (행 하나, 반복)
│  │  1 │ 채식주의자│ 한강 │ 소설    │  3  │[대출]│   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  [< 이전]  1  2  3  [다음 >]                         │  ← Pagination.jsx
│                                                     │
└─────────────────────────────────────────────────────┘

팝업 영역:
┌──────────────────────────────┐
│  대출 신청                    │  ← LoanForm.jsx (모달 안 폼)
│  제목: 채식주의자 (읽기전용)    │
│  대출 기간: 14일              │
│          [취소] [대출 신청]    │
└──────────────────────────────┘
```

### 5.2 컴포넌트 목록 표 작성하기

와이어프레임에서 뽑은 컴포넌트를 표로 정리합니다:

| 파일명 | 역할 | 재사용 여부 | 비고 |
|--------|------|-----------|------|
| `Header.jsx` | 로고, 네비게이션, 로그아웃 | 모든 페이지 | `common/` 폴더 |
| `Pagination.jsx` | 페이지 버튼 (이전/다음/번호) | 도서·대출 목록 | `common/` 폴더 |
| `BookSection.jsx` | 도서 목록 페이지 전체 컨테이너 | 도서 페이지만 | 라우터에 등록 |
| `BookSearch.jsx` | 검색 입력 + 카테고리 필터 | 도서 페이지만 | |
| `BookList.jsx` | 도서 목록 테이블 | 도서 페이지만 | |
| `BookListRow.jsx` | 테이블 한 행 | BookList 내부 | 반복 렌더링 |
| `LoanForm.jsx` | 대출 신청 폼 (모달 안) | 도서 페이지만 | 대출 신청 전용 |
| `MyLoanPage.jsx` | 내 대출 이력 + 반납 버튼 | 내 대출 페이지 | 라우터에 등록 |
| `LoginPage.jsx` | 로그인 폼 | 로그인 페이지만 | `auth/` 폴더 |
| `RegisterPage.jsx` | 회원가입 폼 | 회원가입 페이지만 | `auth/` 폴더 |

### 5.3 상태(State) 목록 뽑기

각 컴포넌트가 "어떤 데이터를 화면에 보여줘야 하는가"를 목록으로 만듭니다.
이것이 Zustand 스토어를 설계하는 기준이 됩니다.

```
BookSection 페이지에 필요한 상태:
  □ books           — 도서 목록 (배열)
  □ loading         — 로딩 중 여부 (true/false)
  □ pageNo          — 현재 페이지 번호
  □ totalPages      — 전체 페이지 수
  □ searchKeyword   — 검색어 (제목/저자)
  □ selectedCategory — 선택된 카테고리 필터
  □ selectedBook    — 대출 신청할 도서 (모달에서 사용)
  □ isLoanModalOpen — 대출 신청 모달 열림/닫힘

→ books, loading, pageNo, totalPages → bookStore.js (서버 데이터)
→ searchKeyword, selectedCategory, isLoanModalOpen, selectedBook
                                    → BookSection 로컬 state (UI 상태)

MyLoanPage에 필요한 상태:
  □ loans      — 내 대출 목록 (배열)
  □ loading    — 로딩 중 여부

→ loans, loading → loanStore.js (서버 데이터)
```

> **규칙:** 서버에서 받아오는 데이터 → Zustand 스토어 / 화면 표시용 상태 → useState

### 5.4 파일 구조 설계

컴포넌트 목록을 폴더로 배치합니다:

```
src/
├── api/
│   ├── axiosInstance.js    # axios 공통 설정 + 인터셉터
│   ├── authApi.js          # 로그인/회원가입 API  (POST /auth/login, /auth/register)
│   ├── bookApi.js          # 도서 API             (GET /api/books)
│   └── loanApi.js          # 대출 API             (POST /api/loans, GET /api/loans/my)
│
├── store/
│   ├── authStore.js        # 인증 상태 (Zustand) — token, email
│   ├── bookStore.js        # 도서 상태 (Zustand) — books, loading, pageNo, totalPages
│   └── loanStore.js        # 대출 상태 (Zustand) — loans, loading
│
├── components/
│   ├── common/             # 재사용 컴포넌트
│   │   ├── Header.jsx      # 네비게이션 + 로그아웃
│   │   ├── Pagination.jsx  # 페이지네이션 버튼
│   │   └── LoadingSpinner.jsx
│   ├── auth/
│   │   ├── LoginPage.jsx
│   │   └── RegisterPage.jsx
│   ├── book/
│   │   ├── BookSection.jsx   # 도서 목록 페이지 전체 (라우터에 등록)
│   │   ├── BookList.jsx      # 도서 목록 테이블
│   │   ├── BookListRow.jsx   # 테이블 한 행
│   │   ├── BookSearch.jsx    # 검색 + 카테고리 필터
│   │   └── LoanForm.jsx      # 대출 신청 폼 (모달)
│   └── loan/
│       └── MyLoanPage.jsx    # 내 대출 이력 + 반납 (라우터에 등록)
│
└── App.jsx                 # 라우팅 + ProtectedRoute
```

---

## 6. STEP 4 — 디자인 토큰 결정하기

> 코딩을 시작하기 전에 색상, 폰트, 간격의 기준을 먼저 정해두면
> 나중에 "왜 버튼마다 색이 다르지?" 같은 문제가 생기지 않습니다.
> **5가지 색상, 2가지 폰트 크기, 간격 단위**만 결정하면 충분합니다.

### 6.1 색상 팔레트 — 5가지만 정하기

```
Primary   — 주요 버튼, 링크, 강조 색상
           예: #3b82f6  (파란색 / Tailwind: blue-500)

Success   — 성공, 저장 완료 색상
           예: #22c55e  (초록색 / Tailwind: green-500)

Warning   — 경고, 주의 색상
           예: #f59e0b  (주황색 / Tailwind: amber-500)

Danger    — 삭제, 오류 색상
           예: #ef4444  (빨간색 / Tailwind: red-500)

Neutral   — 배경, 테두리, 보조 텍스트
           예: #64748b  (회색 / Tailwind: slate-500)
```

> **초보자 팁:** 직접 색상을 고르기 어려우면
> [Tailwind Colors](https://tailwindcss.com/docs/customizing-colors) 에서
> 마음에 드는 색 계열(blue, indigo, violet 등) 하나를 primary로 선택하세요.
> 나머지는 그대로 두면 됩니다.

### 6.2 타이포그래피 — 폰트 크기 기준

```
페이지 제목    — text-2xl  (24px)  font-bold
섹션 제목      — text-xl   (20px)  font-semibold
본문 텍스트    — text-base (16px)  font-normal
보조 텍스트    — text-sm   (14px)  text-slate-500
```

**한국어 웹폰트 추가 (선택):**

```html
<!-- index.html <head>에 추가 -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;500;700&display=swap" rel="stylesheet">
```

```css
/* index.css */
body {
  font-family: 'Noto Sans KR', sans-serif;
}
```

### 6.3 간격(Spacing) 규칙

Tailwind의 간격 단위(`p-`, `m-`, `gap-`)는 일관되게 쓸수록 화면이 깔끔합니다.

```
컴포넌트 내부 패딩  — p-4   (16px)
섹션 간 간격        — my-6  (24px)
카드/박스 패딩      — p-6   (24px)
버튼 사이 간격      — gap-2 (8px)
폼 필드 간격        — space-y-4 (16px)
```

### 6.4 tailwind.config.js에 디자인 토큰 등록

결정한 색상을 코드로 등록하면 `bg-primary`, `text-danger` 처럼 사용할 수 있습니다:

```js
// tailwind.config.js
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: '#3b82f6',   // blue-500
        success: '#22c55e',   // green-500
        warning: '#f59e0b',   // amber-500
        danger:  '#ef4444',   // red-500
        neutral: '#64748b',   // slate-500
      },
    },
  },
  plugins: [],
}
```

```jsx
// 사용 예시
<button className="bg-primary text-white px-4 py-2 rounded hover:opacity-90">
  저장
</button>
<button className="bg-danger text-white px-4 py-2 rounded hover:opacity-90">
  삭제
</button>
```

---

## 7. STEP 5 — UI 라이브러리 선택 및 설치

### 7.1 초보자에게 맞는 라이브러리 선택 기준

```
질문 1: 디자인을 빠르게 완성하고 싶다 → MUI 또는 Ant Design
질문 2: Tailwind를 이미 알고 커스터마이징이 중요하다 → shadcn/ui
질문 3: 깔끔하고 현대적인 UI가 필요하다 → shadcn/ui 또는 Mantine
질문 4: Bootstrap을 알고 있다 → React Bootstrap
```

### 7.2 이 프로젝트(Vite + React + Tailwind) 추천 라이브러리

| 라이브러리 | 초보자 난이도 | Tailwind 호환 | 추천 상황 |
|-----------|-------------|--------------|----------|
| **shadcn/ui** | ⭐⭐⭐ 중간 | ✅ 완벽 | Tailwind를 계속 쓰고 싶을 때 |
| **MUI** | ⭐⭐ 쉬움 | ⚠️ 병용 가능 | 빠르게 완성하고 싶을 때 |
| **Mantine** | ⭐⭐ 쉬움 | ⚠️ 병용 가능 | 현대적 디자인 원할 때 |
| **Ant Design** | ⭐⭐ 쉬움 | ⚠️ 병용 가능 | 관리자 대시보드 스타일 |

**초보자에게 가장 추천: MUI (가장 많은 예제, 문서 풍부) 또는 shadcn/ui (Tailwind 연속성)**

---

## 8. STEP 6 — 레이아웃(뼈대) 먼저 코딩하기

> "화면이 비어있어도 괜찮습니다. 먼저 레이아웃을 잡고, 내용은 나중에 채웁니다."
> 이 단계에서는 **실제 데이터 없이** 컴포넌트의 틀만 만드는 것이 목표입니다.

### 8.1 레이아웃 우선 전략 — 왜 먼저인가?

```
잘못된 순서:
  LoanForm.jsx → BookListRow.jsx → BookList.jsx → BookSection.jsx → App.jsx
  → 각 파일이 완성되기 전까지 아무것도 화면에 안 보임
  → 오류 찾기 어려움

올바른 순서:
  App.jsx(라우팅) → BookSection.jsx(빈 껍데기) → BookList.jsx(빈 테이블)
  → 화면을 보면서 하나씩 채워나감
  → 언제든지 브라우저에서 확인 가능
```

### 8.2 App.jsx — 라우팅 뼈대 먼저

```jsx
// App.jsx — 데이터 없이 라우팅 구조만 먼저
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import LoginPage    from './components/auth/LoginPage';
import RegisterPage from './components/auth/RegisterPage';
import BookSection  from './components/book/BookSection';
import MyLoanPage   from './components/loan/MyLoanPage';
import { useAuthStore } from './store/authStore';

// 로그인 필요한 페이지를 보호하는 컴포넌트
function ProtectedRoute({ children }) {
  const token = useAuthStore(s => s.token);
  return token ? children : <Navigate to="/login" replace />;
}

export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login"    element={<LoginPage />} />
        <Route path="/register" element={<RegisterPage />} />
        {/* 도서 목록은 비로그인도 조회 가능 — ProtectedRoute 불필요 */}
        <Route path="/books"    element={<BookSection />} />
        {/* 내 대출 이력은 로그인 필요 */}
        <Route path="/loans"    element={<ProtectedRoute><MyLoanPage /></ProtectedRoute>} />
        <Route path="*"         element={<Navigate to="/books" replace />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### 8.3 빈 컴포넌트 먼저 만들기

파일을 먼저 만들고, 내용은 나중에 채웁니다.
최소한 이름만 표시되어도 "화면이 연결됐다"는 걸 확인할 수 있습니다:

```jsx
// BookSection.jsx — 껍데기 버전 (내용은 나중에)
import Header     from '../common/Header';
import BookSearch from './BookSearch';
import BookList   from './BookList';
import Pagination from '../common/Pagination';

export default function BookSection() {
  return (
    <div>
      <Header />
      <div className="p-6">
        <h2 className="text-2xl font-bold mb-4">도서 목록</h2>
        <BookSearch />    {/* 아직 검색 기능 없음 — 빈 박스 */}
        <BookList />      {/* 아직 데이터 없음 — 빈 테이블 */}
        <Pagination />    {/* 아직 페이지 없음 — 빈 버튼들 */}
      </div>
    </div>
  );
}
```

```jsx
// BookList.jsx — 껍데기 버전
export default function BookList() {
  return (
    <table className="w-full border-collapse border mt-4">
      <thead className="bg-slate-100">
        <tr>
          <th className="border p-2">ID</th>
          <th className="border p-2">제목</th>
          <th className="border p-2">저자</th>
          <th className="border p-2">카테고리</th>
          <th className="border p-2">재고</th>
          <th className="border p-2">대출</th>
        </tr>
      </thead>
      <tbody>
        {/* 데이터는 STEP 7에서 연결 */}
        <tr><td colSpan={6} className="text-center p-4 text-slate-400">데이터 로딩 예정</td></tr>
      </tbody>
    </table>
  );
}
```

### 8.4 레이아웃 뼈대 완성 체크리스트

```
□ App.jsx에 모든 Route가 등록되어 있는가?
□ /login, /register 에서 해당 페이지가 보이는가?
□ /books 에서 빈 도서 목록 테이블이 보이는가?
□ /loans 에서 빈 대출 이력 테이블이 보이는가?
□ 로그인 없이 /loans 접근 시 /login으로 이동하는가?
□ Header의 네비게이션 클릭 시 URL이 바뀌는가?

→ 여기까지 완료되면 STEP 7으로 진행합니다.
```

---

## 9. STEP 7 — 정적 화면 → 동적 화면으로 완성하기

> "한 번에 다 만들려 하지 마세요. 3단계로 나눠서 완성합니다."

```
1단계: 하드코딩 데이터로 화면 완성  (props 연습)
2단계: Zustand 스토어 연결         (상태 관리)
3단계: API 연결 → 실제 서버 데이터  (비동기 처리)
```

### 9.1 1단계 — 하드코딩 데이터로 화면 먼저 완성

실제 API 없이 더미 데이터로 화면 레이아웃을 완성합니다.
이렇게 하면 API가 없어도 UI를 빠르게 확인할 수 있습니다.

```jsx
// BookSection.jsx — 1단계: 하드코딩 데이터
import { useState } from 'react';
import BookList  from './BookList';
import LoanForm  from './LoanForm';

// ← 임시 더미 데이터 (나중에 서버에서 받아옴)
const DUMMY_BOOKS = [
  { id: 1, title: '채식주의자',  author: '한강',  categoryName: '소설', stock: 3 },
  { id: 2, title: '클린코드',    author: '마틴',  categoryName: 'IT',   stock: 0 },
  { id: 3, title: '파친코',     author: '이민진', categoryName: '소설', stock: 2 },
];

export default function BookSection() {
  const [selectedBook, setSelectedBook] = useState(null);   // 대출 신청할 도서

  return (
    <div className="p-6">
      <h2 className="text-2xl font-bold mb-4">도서 목록</h2>
      {/* props로 더미 데이터 전달 */}
      <BookList books={DUMMY_BOOKS} onLoan={setSelectedBook} />

      {/* 대출 신청 모달 — selectedBook이 있을 때만 표시 */}
      {selectedBook && (
        <LoanForm book={selectedBook} onClose={() => setSelectedBook(null)} />
      )}
    </div>
  );
}
```

```jsx
// BookList.jsx — props로 받은 데이터 화면에 표시
export default function BookList({ books = [], onLoan }) {
  return (
    <table className="w-full border-collapse border mt-4">
      <thead className="bg-slate-100">
        <tr>
          <th className="border p-2 text-left">ID</th>
          <th className="border p-2 text-left">제목</th>
          <th className="border p-2 text-left">저자</th>
          <th className="border p-2 text-left">카테고리</th>
          <th className="border p-2 text-left">재고</th>
          <th className="border p-2 text-left">대출</th>
        </tr>
      </thead>
      <tbody>
        {books.map(book => (
          <tr key={book.id} className="hover:bg-slate-50">
            <td className="border p-2">{book.id}</td>
            <td className="border p-2 font-medium">{book.title}</td>
            <td className="border p-2">{book.author}</td>
            <td className="border p-2">{book.categoryName}</td>
            <td className="border p-2">
              {/* 재고 0이면 빨간색 표시 */}
              <span className={book.stock === 0 ? 'text-red-500 font-bold' : ''}>
                {book.stock}
              </span>
            </td>
            <td className="border p-2">
              {book.stock > 0
                ? <button className="text-blue-600 hover:underline text-sm"
                          onClick={() => onLoan(book)}>대출 신청</button>
                : <span className="text-slate-400 text-sm">대출 불가</span>
              }
            </td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

> **체크:** 더미 데이터가 화면에 잘 나오는가? → 다음 단계로

### 9.2 2단계 — Zustand 스토어 연결

화면이 완성되면 하드코딩 데이터를 Zustand 스토어로 옮깁니다.

```js
// store/bookStore.js
import { create } from 'zustand';
import { BookApi } from '../api/bookApi';

const api = new BookApi();

export const useBookStore = create((set, get) => ({
  books:      [],       // 도서 목록
  loading:    false,    // 로딩 중 여부
  pageNo:     0,        // 현재 페이지
  totalPages: 0,        // 전체 페이지 수

  // 목록 불러오기 (2단계에서는 더미 데이터 반환)
  loadPage: async (pageNo = 0, keyword = '') => {
    set({ loading: true });
    try {
      // 3단계에서 api.getAll(pageNo, keyword) 호출로 교체
      const dummyData = {
        content: [
          { id: 1, title: '채식주의자',  author: '한강',  categoryName: '소설', stock: 3 },
          { id: 2, title: '클린코드',    author: '마틴',  categoryName: 'IT',   stock: 0 },
          { id: 3, title: '파친코',     author: '이민진', categoryName: '소설', stock: 2 },
        ],
        totalPages: 1,
      };
      set({ books: dummyData.content, totalPages: dummyData.totalPages, pageNo });
    } finally {
      set({ loading: false });
    }
  },
}));
```

```jsx
// BookSection.jsx — 2단계: Zustand 스토어 연결
import { useEffect, useState } from 'react';
import { useBookStore } from '../../store/bookStore';
import BookList from './BookList';
import LoanForm from './LoanForm';

export default function BookSection() {
  const { books, loading, loadPage } = useBookStore();
  const [selectedBook, setSelectedBook] = useState(null);

  useEffect(() => {
    loadPage(0);   // 페이지 진입 시 목록 불러오기
  }, []);

  if (loading) return <p className="p-6">로딩 중...</p>;

  return (
    <div className="p-6">
      <h2 className="text-2xl font-bold mb-4">도서 목록</h2>
      <BookList books={books} onLoan={setSelectedBook} />
      {selectedBook && (
        <LoanForm book={selectedBook} onClose={() => setSelectedBook(null)} />
      )}
    </div>
  );
}
```

### 9.3 3단계 — API 연결로 실제 서버 데이터 사용

스토어의 더미 데이터를 실제 API 호출로 교체합니다.
**화면과 스토어 코드는 그대로** — `loadPage` 내부만 바꿉니다:

```js
// store/bookStore.js — 3단계: 실제 API 연결
loadPage: async (pageNo = 0, keyword = '') => {
  set({ loading: true });
  try {
    // 더미 데이터 → 실제 API 호출로 교체
    const data = await api.getAll(pageNo, keyword);   // GET /api/books?page=0&keyword=...
    set({ books: data.content, totalPages: data.totalPages, pageNo });
  } catch (err) {
    console.error('도서 목록 로드 실패:', err);
  } finally {
    set({ loading: false });
  }
},
```

```js
// api/bookApi.js — 도서 API 호출 클래스
import axiosInstance from './axiosInstance';

export class BookApi {
  // GET /api/books?page=0&size=10&keyword=검색어
  async getAll(page = 0, keyword = '', size = 10) {
    const res = await axiosInstance.get('/api/books', { params: { page, size, keyword } });
    return res.data;   // { content: [...], totalPages: 3, ... }
  }

  // GET /api/books/{id}
  async getById(id) {
    const res = await axiosInstance.get(`/api/books/${id}`);
    return res.data;
  }
}
```

```js
// api/loanApi.js — 대출 API 호출 클래스
import axiosInstance from './axiosInstance';

export class LoanApi {
  // POST /api/loans — 대출 신청
  async create(bookId) {
    const res = await axiosInstance.post('/api/loans', { bookId });
    return res.data;
  }

  // GET /api/loans/my — 내 대출 이력
  async getMy() {
    const res = await axiosInstance.get('/api/loans/my');
    return res.data;   // [ { id, bookTitle, loanDate, dueDate, status }, ... ]
  }

  // PUT /api/loans/{id} — 반납 처리
  async returnBook(id) {
    const res = await axiosInstance.put(`/api/loans/${id}`, { status: 'RETURNED' });
    return res.data;
  }
}
```

### 9.4 전체 흐름 요약

```
1단계 완료 기준: 더미 도서 목록이 화면에 표시됨
               재고 0인 도서는 "대출 불가"로 표시됨
               대출 신청 버튼 클릭 시 모달이 열림

2단계 완료 기준: Zustand bookStore에서 데이터 관리
               로딩 중 스피너 / 로딩 완료 후 목록 표시

3단계 완료 기준: GET /api/books 호출 시 실제 도서 목록 수신
               POST /api/loans 호출 시 대출 신청 완료
               GET /api/loans/my 호출 시 내 대출 이력 수신
               네트워크 탭에서 요청/응답 JSON 확인
```

---

## 10. TailwindCSS → UI 라이브러리 전환 가이드

### 10.1 전환 시 발생하는 충돌과 해결

UI 라이브러리와 Tailwind를 함께 쓸 때 스타일 충돌이 생길 수 있습니다.

```
MUI + Tailwind 충돌 예시:
  MUI Button의 padding이 Tailwind 기본 리셋에 의해 사라짐

해결 방법:
```

**MUI + TailwindCSS 함께 사용하기:**

```js
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  // important: '#root',  ← MUI와 충돌 시 이 옵션으로 우선순위 조정
  corePlugins: {
    preflight: false,  // ← MUI와 함께 쓸 때 Tailwind CSS 리셋 비활성화
  },
  theme: {
    extend: {},
  },
  plugins: [],
}
```

```jsx
// main.jsx — MUI 테마를 Tailwind와 통일
import { ThemeProvider, createTheme } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';

const theme = createTheme({
  palette: {
    primary: { main: '#3b82f6' },   // Tailwind blue-500
    secondary: { main: '#64748b' }, // Tailwind slate-500
  },
});

ReactDOM.createRoot(document.getElementById('root')).render(
  <ThemeProvider theme={theme}>
    <CssBaseline />  {/* MUI 기본 CSS 리셋 */}
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </ThemeProvider>
);
```

### 10.2 점진적 전환 전략

한 번에 모두 바꾸지 말고, 컴포넌트 단위로 점진적으로 전환하세요:

```
1단계: UI 라이브러리 설치 (기존 코드 유지)
2단계: 새로 만드는 컴포넌트만 UI 라이브러리 사용
3단계: 공통 컴포넌트(Button, Input 등)부터 교체
4단계: 페이지 단위로 순서대로 교체
```

---

## 11. shadcn/ui 실전 가이드 (추천)

### 11.1 shadcn/ui란?

shadcn/ui는 "설치하는 라이브러리"가 아니라 **"코드를 복사해서 사용하는 컴포넌트 모음"** 입니다.

```
일반 UI 라이브러리:
  npm install → node_modules에 설치 → import해서 사용
  → 커스터마이징 어려움

shadcn/ui:
  npx shadcn-ui add button → src/components/ui/button.tsx 파일이 생성됨
  → 파일을 직접 수정 가능 → 완전한 커스터마이징
```

### 11.2 Vite + React + Tailwind 프로젝트에 설치

```bash
# 1. shadcn/ui 초기화
npx shadcn@latest init

# 설정 질문에 다음과 같이 답합니다:
# Which style would you like to use? › Default
# Which color would you like to use as the base color? › Slate
# Do you want to use CSS variables? › Yes

# 2. 필요한 컴포넌트 추가 (예시)
npx shadcn@latest add button
npx shadcn@latest add input
npx shadcn@latest add table
npx shadcn@latest add dialog      # 모달
npx shadcn@latest add form
npx shadcn@latest add card
npx shadcn@latest add badge
npx shadcn@latest add toast
```

### 11.3 shadcn/ui 컴포넌트 사용 예시

```jsx
// 도서 목록에 shadcn/ui 적용 예시
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Badge } from '@/components/ui/badge';
import {
  Table, TableBody, TableCell, TableHead,
  TableHeader, TableRow,
} from '@/components/ui/table';
import {
  Dialog, DialogContent, DialogHeader,
  DialogTitle, DialogTrigger,
} from '@/components/ui/dialog';
import { useState } from 'react';

export default function BookList({ books, onLoan }) {
  const [open, setOpen] = useState(false);
  const [selectedBook, setSelectedBook] = useState(null);

  const handleLoanClick = (book) => {
    setSelectedBook(book);
    setOpen(true);
  };

  return (
    <div className="space-y-4">
      {/* 검색 */}
      <Input placeholder="제목 또는 저자 검색..." className="max-w-sm" />

      {/* 테이블 */}
      <Table>
        <TableHeader>
          <TableRow>
            <TableHead>ID</TableHead>
            <TableHead>제목</TableHead>
            <TableHead>저자</TableHead>
            <TableHead>카테고리</TableHead>
            <TableHead>재고</TableHead>
            <TableHead>대출</TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          {books.map(book => (
            <TableRow key={book.id}>
              <TableCell>{book.id}</TableCell>
              <TableCell className="font-medium">{book.title}</TableCell>
              <TableCell>{book.author}</TableCell>
              <TableCell>
                <Badge variant="secondary">{book.categoryName}</Badge>
              </TableCell>
              <TableCell>
                <span className={book.stock === 0 ? 'text-red-500 font-bold' : ''}>
                  {book.stock}
                </span>
              </TableCell>
              <TableCell>
                {book.stock > 0 ? (
                  <Dialog open={open && selectedBook?.id === book.id}
                          onOpenChange={(v) => { setOpen(v); if (!v) setSelectedBook(null); }}>
                    <DialogTrigger asChild>
                      <Button size="sm" onClick={() => handleLoanClick(book)}>
                        대출 신청
                      </Button>
                    </DialogTrigger>
                    <DialogContent>
                      <DialogHeader>
                        <DialogTitle>대출 신청</DialogTitle>
                      </DialogHeader>
                      {/* LoanForm 컴포넌트 */}
                    </DialogContent>
                  </Dialog>
                ) : (
                  <span className="text-slate-400 text-sm">대출 불가</span>
                )}
              </TableCell>
            </TableRow>
          ))}
        </TableBody>
      </Table>
    </div>
  );
}
```

### 11.4 shadcn/ui 자주 쓰는 컴포넌트 목록

| 컴포넌트 | 용도 | 설치 명령 |
|---------|------|----------|
| `Button` | 버튼 (variant: default/outline/destructive/ghost) | `npx shadcn@latest add button` |
| `Input` | 텍스트 입력 | `npx shadcn@latest add input` |
| `Table` | 데이터 테이블 | `npx shadcn@latest add table` |
| `Dialog` | 모달 팝업 | `npx shadcn@latest add dialog` |
| `Form` | react-hook-form 연동 폼 | `npx shadcn@latest add form` |
| `Select` | 드롭다운 선택 | `npx shadcn@latest add select` |
| `Badge` | 상태 배지 | `npx shadcn@latest add badge` |
| `Card` | 카드 레이아웃 | `npx shadcn@latest add card` |
| `Toaster` | 알림 토스트 | `npx shadcn@latest add toast` |
| `Pagination` | 페이지네이션 | `npx shadcn@latest add pagination` |
| `Skeleton` | 로딩 스켈레톤 | `npx shadcn@latest add skeleton` |

---

## 12. MUI(Material-UI) 실전 가이드

### 12.1 설치

```bash
npm install @mui/material @emotion/react @emotion/styled
npm install @mui/icons-material   # 아이콘 사용 시
```

### 12.2 도서 목록 MUI 적용 예시

```jsx
import {
  Box, Typography, Button, TextField,
  Table, TableBody, TableCell, TableContainer,
  TableHead, TableRow, Paper, Chip,
  Dialog, DialogTitle, DialogContent,
} from '@mui/material';
import { MenuBook } from '@mui/icons-material';
import { useState } from 'react';

export default function BookList({ books, onLoan }) {
  const [open, setOpen] = useState(false);
  const [selectedBook, setSelectedBook] = useState(null);

  const handleLoanClick = (book) => {
    setSelectedBook(book);
    setOpen(true);
  };

  return (
    <Box sx={{ p: 3 }}>
      {/* 제목 */}
      <Typography variant="h5" fontWeight="bold" mb={3}>도서 목록</Typography>

      {/* 검색 */}
      <TextField
        placeholder="제목 또는 저자 검색..."
        size="small"
        sx={{ mb: 2, width: 300 }}
      />

      {/* 테이블 */}
      <TableContainer component={Paper}>
        <Table>
          <TableHead sx={{ bgcolor: 'grey.100' }}>
            <TableRow>
              <TableCell>ID</TableCell>
              <TableCell>제목</TableCell>
              <TableCell>저자</TableCell>
              <TableCell>카테고리</TableCell>
              <TableCell>재고</TableCell>
              <TableCell>대출</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {books.map(book => (
              <TableRow key={book.id} hover>
                <TableCell>{book.id}</TableCell>
                <TableCell sx={{ fontWeight: 'medium' }}>{book.title}</TableCell>
                <TableCell>{book.author}</TableCell>
                <TableCell>
                  <Chip label={book.categoryName} size="small" variant="outlined" />
                </TableCell>
                <TableCell>
                  <Typography
                    color={book.stock === 0 ? 'error' : 'text.primary'}
                    fontWeight={book.stock === 0 ? 'bold' : 'normal'}
                  >
                    {book.stock}
                  </Typography>
                </TableCell>
                <TableCell>
                  {book.stock > 0 ? (
                    <Button
                      size="small"
                      variant="contained"
                      startIcon={<MenuBook />}
                      onClick={() => handleLoanClick(book)}
                    >
                      대출 신청
                    </Button>
                  ) : (
                    <Typography variant="body2" color="text.disabled">대출 불가</Typography>
                  )}
                </TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>

      {/* 대출 신청 모달 */}
      <Dialog open={open} onClose={() => setOpen(false)} maxWidth="sm" fullWidth>
        <DialogTitle>대출 신청 — {selectedBook?.title}</DialogTitle>
        <DialogContent>
          {/* LoanForm 컴포넌트 */}
        </DialogContent>
      </Dialog>
    </Box>
  );
}
```

### 12.3 MUI 자주 쓰는 컴포넌트 목록

| 컴포넌트 | 용도 |
|---------|------|
| `Box` | 레이아웃 div (sx prop으로 스타일링) |
| `Typography` | 텍스트 (variant: h1~h6, body1, body2) |
| `Button` | 버튼 (variant: contained/outlined/text) |
| `TextField` | 입력 필드 |
| `Table / TableContainer` | 데이터 테이블 |
| `Chip` | 태그/배지 |
| `Dialog` | 모달 팝업 |
| `Snackbar + Alert` | 알림 토스트 |
| `Pagination` | 페이지네이션 |
| `CircularProgress` | 로딩 스피너 |
| `Select + MenuItem` | 드롭다운 |
| `Grid` | 그리드 레이아웃 |

---

## 13. 레이아웃 패턴 참고

### 13.1 이 프로젝트 레이아웃 구조

```
App.jsx (최상위)
  └── BrowserRouter
      └── Routes
          ├── /login      → LoginPage
          ├── /register   → RegisterPage
          ├── /books      → BookSection     (비로그인도 도서 조회 가능)
          └── /loans      → MyLoanPage      (ProtectedRoute — 로그인 필요)

BookSection.jsx (도서 목록 페이지)
  ├── Header              (로고 + 네비게이션 + 로그아웃)
  ├── BookSearch          (검색 입력 + 카테고리 필터)
  ├── BookList            (도서 테이블)
  │   └── BookListRow     (행 하나 — 반복)
  ├── Pagination          (페이지 버튼)
  └── LoanForm            (대출 신청 모달 — 대출 버튼 클릭 시 표시)

MyLoanPage.jsx (내 대출 이력 페이지)
  ├── Header
  └── 대출 이력 테이블     (반납 버튼 포함)
```

### 13.2 일반적인 관리자 대시보드 레이아웃

```jsx
// 관리자 레이아웃 예시 (App Shell 패턴)
export default function AdminLayout() {
  return (
    <div style={{ display: 'flex', minHeight: '100vh' }}>

      {/* 사이드바 (고정) */}
      <Sidebar style={{ width: 240, flexShrink: 0 }} />

      {/* 메인 영역 */}
      <div style={{ flex: 1, display: 'flex', flexDirection: 'column' }}>

        {/* 상단 헤더 (고정) */}
        <Header style={{ height: 64 }} />

        {/* 페이지 콘텐츠 (스크롤 가능) */}
        <main style={{ flex: 1, padding: 24, overflow: 'auto' }}>
          <Outlet />  {/* React Router - 현재 경로의 컴포넌트 렌더링 */}
        </main>

      </div>
    </div>
  );
}
```

### 13.3 반응형 디자인 기준점 (Breakpoints)

| 이름 | 너비 | 대상 기기 |
|------|------|---------|
| xs | < 576px | 스마트폰 (세로) |
| sm | 576px ~ 767px | 스마트폰 (가로) |
| md | 768px ~ 991px | 태블릿 |
| lg | 992px ~ 1199px | 작은 데스크톱 |
| xl | ≥ 1200px | 큰 데스크톱 |

---

## 14. 자주 묻는 질문 (FAQ)

### Q1. Figma를 꼭 배워야 하나요?

```
꼭 필요하지 않습니다.
팀 협업이나 전문적인 디자인이 필요할 때 유용하지만,
개인 프로젝트나 간단한 설계는 텍스트 와이어프레임으로 충분합니다.

추천 순서:
  1단계: 종이 또는 텍스트로 와이어프레임 그리기 (지금 당장 가능)
  2단계: draw.io (drawio.com) - 무료, 설치 불필요, 박스/선 그리기 특화
  3단계: Figma - 팀 협업 또는 더 상세한 디자인이 필요할 때
```

### Q2. UI 라이브러리 없이 Tailwind만으로 할 수 없나요?

```
충분히 가능합니다.
하지만 테이블, 모달, 페이지네이션 같은 복잡한 컴포넌트를
처음부터 만들면 시간이 많이 걸립니다.

Tailwind만 → 디자인 자유도 높음, 개발 시간 많이 필요
UI 라이브러리 → 빠른 개발, 일관된 디자인, 약간의 제약
```

### Q3. MUI와 Tailwind를 동시에 쓰면 문제없나요?

```
tailwind.config.js에서 preflight: false 설정 후 병용 가능합니다.
단, 두 가지 스타일 시스템이 섞이면 나중에 유지보수가 어려울 수 있습니다.

권장:
  - 하나만 선택 (MUI만 또는 Tailwind + shadcn/ui)
  - 이미 Tailwind를 쓰고 있다면 shadcn/ui 추가가 자연스러움
  - 새로 시작한다면 MUI가 학습 자료가 더 많아 쉬움
```

### Q4. 컴포넌트를 어떻게 나눠야 할지 모르겠어요.

```
처음에는 크게 나누고 나중에 작게 분리하세요.

단계적 접근:
  1. 일단 한 파일에 다 작성 (BookSection.jsx에 모든 코드)
  2. 50줄이 넘으면 분리 고민
  3. 다른 곳에서도 쓸 것 같으면 분리 (Pagination, Header 등)
  4. 완성 후 리팩터링으로 정리

처음부터 완벽하게 나누려 하지 마세요.
```

### Q5. 어떤 색깔/폰트를 써야 하나요?

```
처음에는 UI 라이브러리의 기본값을 그대로 쓰세요.
색상 선택에 너무 시간을 쓰는 것보다 기능 구현이 먼저입니다.

나중에 정리할 때:
  - 주 색상(Primary): 브랜드 색상 1가지
  - 성공(Success): 초록색 계열
  - 경고(Warning): 주황색 계열
  - 오류(Error): 빨간색 계열
  - 중립(Neutral): 회색 계열

이 5가지만 정해도 충분합니다.
```

---

## 실전 체크리스트

7단계를 순서대로 진행하면서 아래를 확인하세요:

**STEP 1 — 페이지 목록**
- [ ] 페이지 목록(사이트맵)을 URL과 함께 작성했는가?
- [ ] 각 페이지에 필요한 API 엔드포인트를 파악했는가?

**STEP 2 — 와이어프레임**
- [ ] 주요 페이지(목록/폼/로그인)의 와이어프레임을 그렸는가?
- [ ] 모달/팝업 영역도 그렸는가?

**STEP 3 — 컴포넌트 목록**
- [ ] 와이어프레임의 박스마다 JSX 파일 이름을 붙였는가?
- [ ] 컴포넌트 목록 표를 작성했는가? (파일명, 역할, 재사용 여부)
- [ ] 서버 데이터 상태 vs UI 상태를 구분했는가?
- [ ] 파일 구조(src/components, src/store, src/api)를 설계했는가?

**STEP 4 — 디자인 토큰**
- [ ] Primary 색상 1가지를 결정했는가?
- [ ] tailwind.config.js에 색상 토큰을 등록했는가?

**STEP 5 — UI 라이브러리**
- [ ] UI 라이브러리를 선택하고 설치했는가?
- [ ] shadcn/ui라면 `npx shadcn@latest init` 완료?
- [ ] MUI라면 `tailwind.config.js`에 `preflight: false` 설정?

**STEP 6 — 레이아웃 뼈대**
- [ ] App.jsx에 모든 Route가 등록되어 있는가?
- [ ] 각 페이지 URL에서 빈 컴포넌트가 보이는가?
- [ ] ProtectedRoute가 동작하는가?

**STEP 7 — 정적 → 동적**
- [ ] 더미 데이터로 화면 레이아웃이 완성됐는가?
- [ ] Zustand 스토어가 연결됐는가?
- [ ] 실제 API와 연동됐는가?
