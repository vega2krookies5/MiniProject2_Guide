# React 화면 설계 가이드 — shadcn/ui + MUI DataGrid 조합

> **대상:** `React화면설계가이드_입문자를위한화면설계시작하기.md` 7단계 프로세스를 이해한 후,
> shadcn/ui + MUI DataGrid 조합으로 온라인 도서관 시스템을 구현하려는 분을 위한 가이드입니다.
>
> **왜 이 조합인가?**
> - **shadcn/ui** — 버튼·폼·모달 등 범용 UI를 Tailwind 기반으로 커스터마이징
> - **MUI DataGrid** — 도서 목록·대출 이력처럼 열 정렬·필터·페이징이 필요한 테이블에 특화
> - 두 라이브러리가 서로 다른 영역을 담당하므로 스타일 충돌이 최소화됩니다.

---

## 목차

1. [조합 개요 및 역할 분담](#1-조합-개요-및-역할-분담)
2. [설치 및 초기 설정](#2-설치-및-초기-설정)
3. [디자인 토큰 통합 — Tailwind ↔ MUI 색상 일치](#3-디자인-토큰-통합--tailwind--mui-색상-일치)
4. [STEP 6 — 레이아웃 뼈대 (shadcn/ui 기반)](#4-step-6--레이아웃-뼈대-shadcnui-기반)
5. [STEP 7 — 도서 목록 페이지 구현 (MUI DataGrid)](#5-step-7--도서-목록-페이지-구현-mui-datagrid)
6. [대출 신청 모달 (shadcn/ui Dialog)](#6-대출-신청-모달-shadcnui-dialog)
7. [내 대출 이력 페이지 (MUI DataGrid + shadcn/ui Badge)](#7-내-대출-이력-페이지-mui-datagrid--shadcnui-badge)
8. [로그인 · 회원가입 페이지 (shadcn/ui Form)](#8-로그인--회원가입-페이지-shadcnui-form)
9. [Header 및 네비게이션 (shadcn/ui)](#9-header-및-네비게이션-shadcnui)
10. [파일 구조 최종 정리](#10-파일-구조-최종-정리)
11. [자주 발생하는 문제 (FAQ)](#11-자주-발생하는-문제-faq)

---

## 1. 조합 개요 및 역할 분담

```
shadcn/ui 담당 영역              MUI DataGrid 담당 영역
─────────────────────────────    ─────────────────────────────
Button       — 버튼              도서 목록 테이블
Dialog       — 대출 신청 모달    내 대출 이력 테이블
Input        — 검색 입력창       열 정렬 / 필터링
Select       — 카테고리 필터     페이지네이션 (자동)
Badge        — 재고/상태 표시    행 선택 체크박스
Card         — 통계 카드
Form + Label — 로그인·회원가입
```

### 언제 무엇을 쓰는가?

| 상황 | 선택 |
|------|------|
| 버튼, 입력창, 셀렉트, 모달 | **shadcn/ui** |
| 열이 5개 이상인 목록 테이블 | **MUI DataGrid** |
| 정렬·필터·페이징이 필요한 테이블 | **MUI DataGrid** |
| 상태 표시 (재고있음/연체/반납완료) | **shadcn/ui Badge** |
| 폼 유효성 검사 UI | **shadcn/ui Form** |

---

## 2. 설치 및 초기 설정

### 2.1 프로젝트 전제 조건

이 가이드는 아래 환경을 기준으로 합니다:

```bash
# 이미 설치되어 있어야 할 것들
node -v      # v18 이상
npm -v       # v9 이상

# 프로젝트 생성 (아직 없다면)
npm create vite@latest library-app -- --template react
cd library-app
npm install
```

### 2.2 Tailwind CSS 설치

shadcn/ui의 필수 의존성입니다.

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```js
// tailwind.config.js
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        // 도서관 시스템 디자인 토큰
        primary: '#3b82f6',   // blue-500  — 대출 신청 버튼, 링크
        success: '#22c55e',   // green-500 — 재고 있음
        warning: '#f59e0b',   // amber-500 — 연체 경고
        danger:  '#ef4444',   // red-500   — 재고 없음, 삭제
        neutral: '#64748b',   // slate-500 — 보조 텍스트
      },
    },
  },
  plugins: [],
}
```

```css
/* src/index.css — Tailwind 지시어 + CSS 변수 */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 221.2 83.2% 53.3%;       /* #3b82f6 */
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 221.2 83.2% 53.3%;
    --radius: 0.5rem;
  }
}
```

### 2.3 shadcn/ui 설치

```bash
npx shadcn@latest init
```

```
# 대화형 설정
√ Which style would you like to use? › Default
√ Which color would you like to use as base color? › Slate
√ Would you like to use CSS variables for colors? › yes
```

```bash
# 이 프로젝트에 필요한 컴포넌트 한 번에 설치
npx shadcn@latest add button input select badge dialog card label form
```

### 2.4 MUI DataGrid 설치

```bash
# MUI 코어 + DataGrid (Community 버전 — 무료)
npm install @mui/material @mui/x-data-grid @emotion/react @emotion/styled
```

> **DataGrid Community vs Pro**
> - Community (무료): 기본 정렬, 필터, 페이징, 행 선택 — **이 가이드에서 사용**
> - Pro (유료): 트리 데이터, 행 그룹핑, 엑셀 내보내기

### 2.5 기타 패키지

```bash
npm install react-router-dom zustand axios
```

---

## 3. 디자인 토큰 통합 — Tailwind ↔ MUI 색상 일치

shadcn/ui(Tailwind)와 MUI는 각자의 색상 시스템을 가지고 있습니다.
DataGrid 버튼 색이 shadcn/ui 버튼 색과 다르면 어색해집니다.
아래 설정으로 MUI의 primary 색을 Tailwind와 일치시킵니다.

```jsx
// src/theme/muiTheme.js
import { createTheme } from '@mui/material/styles';

export const muiTheme = createTheme({
  palette: {
    primary: {
      main:        '#3b82f6',  // Tailwind blue-500 (도서관 Primary)
      light:       '#93c5fd',  // blue-300
      dark:        '#1d4ed8',  // blue-700
      contrastText: '#ffffff',
    },
    success: {
      main: '#22c55e',         // green-500
    },
    warning: {
      main: '#f59e0b',         // amber-500
    },
    error: {
      main: '#ef4444',         // red-500
    },
  },
  typography: {
    fontFamily: [
      'Noto Sans KR',
      '-apple-system',
      'sans-serif',
    ].join(','),
    fontSize: 14,
  },
  shape: {
    borderRadius: 8,           // shadcn/ui의 rounded-lg와 맞춤
  },
  components: {
    // DataGrid 헤더를 shadcn/ui 스타일에 맞게 조정
    MuiDataGrid: {
      styleOverrides: {
        root: {
          border: '1px solid #e2e8f0',  // slate-200
          borderRadius: '0.5rem',
          '& .MuiDataGrid-columnHeaders': {
            backgroundColor: '#f8fafc',  // slate-50
            borderBottom: '2px solid #e2e8f0',
          },
          '& .MuiDataGrid-row:hover': {
            backgroundColor: '#f1f5f9',  // slate-100
          },
        },
      },
    },
  },
});
```

```jsx
// src/main.jsx — MUI ThemeProvider 적용
import React from 'react';
import ReactDOM from 'react-dom/client';
import { ThemeProvider } from '@mui/material/styles';
import { muiTheme } from './theme/muiTheme';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <ThemeProvider theme={muiTheme}>
      <App />
    </ThemeProvider>
  </React.StrictMode>
);
```

---

## 4. STEP 6 — 레이아웃 뼈대 (shadcn/ui 기반)

입문자를위한화면설계시작하기.md STEP 6 기준으로 빈 껍데기부터 만듭니다.

### 4.1 App.jsx — 라우팅 구조

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import LoginPage    from './components/auth/LoginPage';
import RegisterPage from './components/auth/RegisterPage';
import BookSection  from './components/book/BookSection';
import MyLoanPage   from './components/loan/MyLoanPage';
import { useAuthStore } from './store/authStore';

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
        {/* 도서 목록 — 비로그인도 조회 가능 */}
        <Route path="/books"    element={<BookSection />} />
        {/* 내 대출 이력 — 로그인 필요 */}
        <Route path="/loans"    element={
          <ProtectedRoute><MyLoanPage /></ProtectedRoute>
        } />
        <Route path="*"         element={<Navigate to="/books" replace />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### 4.2 레이아웃 뼈대 완성 체크리스트

```
□ /books  → 빈 도서 목록 테이블(DataGrid)이 보이는가?
□ /loans  → 빈 대출 이력 테이블(DataGrid)이 보이는가?
□ /login  → 로그인 폼이 보이는가?
□ 비로그인 상태에서 /loans 접근 시 /login으로 이동하는가?
□ Header 네비게이션 클릭 시 URL이 바뀌는가?
```

---

## 5. STEP 7 — 도서 목록 페이지 구현 (MUI DataGrid)

### 5.1 bookStore.js — Zustand 스토어

```js
// src/store/bookStore.js
import { create } from 'zustand';
import { BookApi } from '../api/bookApi';

const bookApi = new BookApi();

export const useBookStore = create((set) => ({
  books:      [],
  loading:    false,
  pageNo:     0,
  totalPages: 0,

  fetchBooks: async ({ keyword = '', category = '', page = 0 } = {}) => {
    set({ loading: true });
    try {
      const data = await bookApi.getAll({ keyword, category, page });
      set({
        books:      data.content,
        pageNo:     data.number,
        totalPages: data.totalPages,
      });
    } catch (err) {
      console.error('도서 조회 실패:', err);
    } finally {
      set({ loading: false });
    }
  },
}));
```

### 5.2 BookSection.jsx — 페이지 컨테이너

```jsx
// src/components/book/BookSection.jsx
import { useState, useEffect } from 'react';
import Header      from '../common/Header';
import BookSearch  from './BookSearch';
import BookDataGrid from './BookDataGrid';
import LoanDialog  from './LoanDialog';
import { useBookStore }  from '../../store/bookStore';
import { useAuthStore }  from '../../store/authStore';

export default function BookSection() {
  const { books, loading, fetchBooks } = useBookStore();
  const { token } = useAuthStore();

  const [keyword,  setKeyword]  = useState('');
  const [category, setCategory] = useState('');
  const [selectedBook, setSelectedBook] = useState(null);  // 모달 대상

  // 검색 조건 변경 시 재조회
  useEffect(() => {
    fetchBooks({ keyword, category });
  }, [keyword, category]);

  const handleLoanRequest = (book) => {
    if (!token) {
      alert('로그인이 필요한 서비스입니다.');
      return;
    }
    setSelectedBook(book);
  };

  return (
    <div className="min-h-screen bg-slate-50">
      <Header />

      <main className="max-w-6xl mx-auto px-6 py-8">
        <h1 className="text-2xl font-bold text-slate-900 mb-6">도서 목록</h1>

        {/* 검색 + 카테고리 필터 */}
        <BookSearch
          keyword={keyword}
          category={category}
          onKeywordChange={setKeyword}
          onCategoryChange={setCategory}
        />

        {/* MUI DataGrid 도서 목록 */}
        <BookDataGrid
          books={books}
          loading={loading}
          onLoan={handleLoanRequest}
        />
      </main>

      {/* 대출 신청 모달 */}
      <LoanDialog
        book={selectedBook}
        open={!!selectedBook}
        onClose={() => setSelectedBook(null)}
      />
    </div>
  );
}
```

### 5.3 BookSearch.jsx — shadcn/ui Input + Select

```jsx
// src/components/book/BookSearch.jsx
import { Input }  from '@/components/ui/input';
import { Select, SelectContent, SelectItem,
         SelectTrigger, SelectValue } from '@/components/ui/select';
import { Button } from '@/components/ui/button';

const CATEGORIES = ['소설', 'IT', '역사', '기타'];

export default function BookSearch({
  keyword, category, onKeywordChange, onCategoryChange
}) {
  return (
    <div className="flex flex-wrap gap-3 mb-6">
      {/* 검색어 입력 */}
      <Input
        placeholder="제목 또는 저자를 입력하세요"
        value={keyword}
        onChange={(e) => onKeywordChange(e.target.value)}
        className="w-72"
      />

      {/* 카테고리 필터 */}
      <Select
        value={category || 'all'}
        onValueChange={(v) => onCategoryChange(v === 'all' ? '' : v)}
      >
        <SelectTrigger className="w-40">
          <SelectValue placeholder="카테고리" />
        </SelectTrigger>
        <SelectContent>
          <SelectItem value="all">전체</SelectItem>
          {CATEGORIES.map(cat => (
            <SelectItem key={cat} value={cat}>{cat}</SelectItem>
          ))}
        </SelectContent>
      </Select>

      {/* 필터 초기화 */}
      {(keyword || category) && (
        <Button
          variant="outline"
          onClick={() => { onKeywordChange(''); onCategoryChange(''); }}
        >
          초기화
        </Button>
      )}
    </div>
  );
}
```

### 5.4 BookDataGrid.jsx — MUI DataGrid 도서 목록

```jsx
// src/components/book/BookDataGrid.jsx
import { DataGrid } from '@mui/x-data-grid';
import { Badge }    from '@/components/ui/badge';
import { Button }   from '@/components/ui/button';

// ── 열(column) 정의 ────────────────────────────────────────────────────────
// renderCell — 해당 열에 React 컴포넌트를 렌더링할 때 사용
const buildColumns = (onLoan) => [
  {
    field: 'id',
    headerName: 'ID',
    width: 70,
  },
  {
    field: 'title',
    headerName: '제목',
    flex: 2,              // flex: 남은 공간을 비율로 차지
    minWidth: 150,
  },
  {
    field: 'author',
    headerName: '저자',
    flex: 1,
    minWidth: 100,
  },
  {
    field: 'categoryName',
    headerName: '카테고리',
    width: 110,
    renderCell: ({ value }) => (
      <Badge variant="secondary">{value}</Badge>
    ),
  },
  {
    field: 'stock',
    headerName: '재고',
    width: 90,
    renderCell: ({ value }) => (
      // shadcn/ui Badge로 재고 표시
      <Badge variant={value > 0 ? 'default' : 'destructive'}>
        {value > 0 ? `${value}권` : '없음'}
      </Badge>
    ),
  },
  {
    field: 'actions',
    headerName: '대출',
    width: 110,
    sortable: false,
    filterable: false,
    renderCell: ({ row }) => (
      <Button
        size="sm"
        disabled={row.stock === 0}
        onClick={() => onLoan(row)}
        className="h-7 text-xs"
      >
        {row.stock > 0 ? '대출 신청' : '대출 불가'}
      </Button>
    ),
  },
];

// ── 더미 데이터 (STEP 7-1: API 연결 전 화면 확인용) ─────────────────────────
const DUMMY_BOOKS = [
  { id: 1, title: '채식주의자',   author: '한강',   categoryName: '소설', stock: 3 },
  { id: 2, title: '클린코드',     author: '마틴',   categoryName: 'IT',   stock: 0 },
  { id: 3, title: '사피엔스',     author: '하라리', categoryName: '역사', stock: 2 },
  { id: 4, title: '토비의 스프링', author: '이일민', categoryName: 'IT',  stock: 1 },
];

export default function BookDataGrid({ books, loading, onLoan }) {
  // STEP 7-1: books가 비어있으면 더미 데이터로 화면 확인
  // STEP 7-2: useBookStore fetchBooks() 연결 후 books가 채워짐
  const rows = books.length > 0 ? books : DUMMY_BOOKS;

  return (
    <div style={{ height: 480 }}>
      <DataGrid
        rows={rows}
        columns={buildColumns(onLoan)}
        loading={loading}

        // 페이지네이션 설정
        pageSizeOptions={[10, 25, 50]}
        initialState={{
          pagination: { paginationModel: { pageSize: 10 } },
        }}

        // 열 정렬 허용 (기본값: true)
        disableColumnFilter={false}
        disableColumnMenu={false}

        // 행 클릭 시 체크박스 선택 (비활성화)
        checkboxSelection={false}
        disableRowSelectionOnClick

        // 한국어 텍스트
        localeText={{
          noRowsLabel:        '도서가 없습니다',
          footerRowSelected:  (count) => `${count}개 선택됨`,
          MuiTablePagination: {
            labelRowsPerPage: '페이지 당 행 수:',
            labelDisplayedRows: ({ from, to, count }) =>
              `${from}–${to} / 전체 ${count}`,
          },
        }}

        sx={{
          // DataGrid 기본 테두리 제거 (muiTheme에서 재정의)
          '& .MuiDataGrid-cell:focus': { outline: 'none' },
          '& .MuiDataGrid-columnHeader:focus': { outline: 'none' },
        }}
      />
    </div>
  );
}
```

---

## 6. 대출 신청 모달 (shadcn/ui Dialog)

```jsx
// src/components/book/LoanDialog.jsx
import { useState } from 'react';
import {
  Dialog, DialogContent, DialogHeader,
  DialogTitle, DialogFooter,
} from '@/components/ui/dialog';
import { Button } from '@/components/ui/button';
import { Badge }  from '@/components/ui/badge';
import { useLoanStore } from '../../store/loanStore';

// 오늘로부터 n일 후 날짜를 'YYYY-MM-DD'로 반환
const addDays = (days) => {
  const d = new Date();
  d.setDate(d.getDate() + days);
  return d.toISOString().split('T')[0];
};

export default function LoanDialog({ book, open, onClose }) {
  const { createLoan } = useLoanStore();
  const [submitting, setSubmitting] = useState(false);

  if (!book) return null;

  const dueDate = addDays(14);  // 반납 예정일: 대출 후 14일

  const handleConfirm = async () => {
    setSubmitting(true);
    try {
      await createLoan(book.id);
      alert(`"${book.title}" 대출이 완료되었습니다.\n반납 예정일: ${dueDate}`);
      onClose();
    } catch (err) {
      alert('대출 신청 중 오류가 발생했습니다.');
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <Dialog open={open} onOpenChange={onClose}>
      <DialogContent className="sm:max-w-md">
        <DialogHeader>
          <DialogTitle>대출 신청</DialogTitle>
        </DialogHeader>

        {/* 도서 정보 — 읽기 전용 */}
        <div className="space-y-3 py-2">
          <div className="flex justify-between items-center">
            <span className="text-sm text-slate-500">제목</span>
            <span className="font-medium">{book.title}</span>
          </div>
          <div className="flex justify-between items-center">
            <span className="text-sm text-slate-500">저자</span>
            <span>{book.author}</span>
          </div>
          <div className="flex justify-between items-center">
            <span className="text-sm text-slate-500">카테고리</span>
            <Badge variant="secondary">{book.categoryName}</Badge>
          </div>
          <div className="flex justify-between items-center">
            <span className="text-sm text-slate-500">대출 기간</span>
            <span>14일</span>
          </div>
          <div className="flex justify-between items-center border-t pt-3">
            <span className="text-sm text-slate-500">반납 예정일</span>
            <span className="font-semibold text-primary">{dueDate}</span>
          </div>
        </div>

        <DialogFooter className="gap-2">
          <Button variant="outline" onClick={onClose} disabled={submitting}>
            취소
          </Button>
          <Button onClick={handleConfirm} disabled={submitting}>
            {submitting ? '처리 중...' : '대출 신청'}
          </Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  );
}
```

---

## 7. 내 대출 이력 페이지 (MUI DataGrid + shadcn/ui Badge)

### 7.1 loanStore.js — Zustand 스토어

```js
// src/store/loanStore.js
import { create } from 'zustand';
import { LoanApi } from '../api/loanApi';

const loanApi = new LoanApi();

export const useLoanStore = create((set) => ({
  loans:   [],
  loading: false,

  // 내 대출 이력 조회
  fetchMyLoans: async () => {
    set({ loading: true });
    try {
      const data = await loanApi.getMy();
      set({ loans: data });
    } catch (err) {
      console.error('대출 이력 조회 실패:', err);
    } finally {
      set({ loading: false });
    }
  },

  // 대출 신청
  createLoan: async (bookId) => {
    await loanApi.create(bookId);
  },

  // 반납 처리
  returnBook: async (loanId) => {
    await loanApi.returnBook(loanId);
    // 반납 후 목록 갱신 (상태 직접 업데이트)
    set((state) => ({
      loans: state.loans.map(loan =>
        loan.id === loanId
          ? { ...loan, status: 'RETURNED', returnedAt: new Date().toISOString() }
          : loan
      ),
    }));
  },
}));
```

### 7.2 MyLoanPage.jsx — DataGrid + Badge 상태 표시

```jsx
// src/components/loan/MyLoanPage.jsx
import { useEffect } from 'react';
import { DataGrid } from '@mui/x-data-grid';
import { Badge }    from '@/components/ui/badge';
import { Button }   from '@/components/ui/button';
import Header from '../common/Header';
import { useLoanStore } from '../../store/loanStore';

// 대출 상태 → Badge 표시 설정
const STATUS_CONFIG = {
  ACTIVE:   { label: '대출중',   variant: 'default'     },
  OVERDUE:  { label: '연체',     variant: 'destructive'  },
  RETURNED: { label: '반납완료', variant: 'secondary'    },
};

// 더미 데이터 (STEP 7-1)
const DUMMY_LOANS = [
  {
    id: 1, bookTitle: '채식주의자',
    loanDate: '2026-03-26', dueDate: '2026-04-09',
    returnedAt: null, status: 'ACTIVE',
  },
  {
    id: 2, bookTitle: '클린코드',
    loanDate: '2026-02-10', dueDate: '2026-02-24',
    returnedAt: '2026-02-23', status: 'RETURNED',
  },
  {
    id: 3, bookTitle: '사피엔스',
    loanDate: '2026-03-01', dueDate: '2026-03-15',
    returnedAt: null, status: 'OVERDUE',
  },
];

export default function MyLoanPage() {
  const { loans, loading, fetchMyLoans, returnBook } = useLoanStore();

  useEffect(() => {
    fetchMyLoans();
  }, []);

  const rows = loans.length > 0 ? loans : DUMMY_LOANS;

  const columns = [
    {
      field: 'bookTitle',
      headerName: '도서명',
      flex: 2,
      minWidth: 150,
    },
    {
      field: 'loanDate',
      headerName: '대출일',
      width: 120,
    },
    {
      field: 'dueDate',
      headerName: '반납 예정일',
      width: 130,
      // 연체 시 빨간 텍스트
      renderCell: ({ row, value }) => (
        <span className={row.status === 'OVERDUE' ? 'text-red-500 font-medium' : ''}>
          {value}
        </span>
      ),
    },
    {
      field: 'returnedAt',
      headerName: '반납일',
      width: 120,
      renderCell: ({ value }) => value ?? '-',
    },
    {
      field: 'status',
      headerName: '상태',
      width: 110,
      renderCell: ({ value }) => {
        const cfg = STATUS_CONFIG[value] ?? STATUS_CONFIG.ACTIVE;
        return <Badge variant={cfg.variant}>{cfg.label}</Badge>;
      },
    },
    {
      field: 'actions',
      headerName: '처리',
      width: 110,
      sortable: false,
      renderCell: ({ row }) =>
        row.status === 'ACTIVE' || row.status === 'OVERDUE' ? (
          <Button
            size="sm"
            variant="outline"
            className="h-7 text-xs"
            onClick={() => {
              if (confirm(`"${row.bookTitle}"을 반납하시겠습니까?`)) {
                returnBook(row.id);
              }
            }}
          >
            반납
          </Button>
        ) : (
          <span className="text-slate-400 text-xs">-</span>
        ),
    },
  ];

  return (
    <div className="min-h-screen bg-slate-50">
      <Header />

      <main className="max-w-5xl mx-auto px-6 py-8">
        <h1 className="text-2xl font-bold text-slate-900 mb-6">내 대출 이력</h1>

        {/* 요약 카드 */}
        <div className="flex gap-4 mb-6">
          {[
            { label: '대출중',   count: rows.filter(l => l.status === 'ACTIVE').length,   color: 'text-blue-600'  },
            { label: '연체',     count: rows.filter(l => l.status === 'OVERDUE').length,  color: 'text-red-600'   },
            { label: '반납완료', count: rows.filter(l => l.status === 'RETURNED').length, color: 'text-slate-600' },
          ].map(({ label, count, color }) => (
            <div key={label} className="bg-white rounded-lg border px-5 py-3 shadow-sm">
              <p className="text-xs text-slate-500">{label}</p>
              <p className={`text-2xl font-bold ${color}`}>{count}</p>
            </div>
          ))}
        </div>

        {/* 대출 이력 DataGrid */}
        <div className="bg-white rounded-lg border shadow-sm" style={{ height: 420 }}>
          <DataGrid
            rows={rows}
            columns={columns}
            loading={loading}
            pageSizeOptions={[10, 25]}
            initialState={{
              pagination: { paginationModel: { pageSize: 10 } },
            }}
            disableRowSelectionOnClick
            localeText={{
              noRowsLabel: '대출 이력이 없습니다',
              MuiTablePagination: {
                labelRowsPerPage: '페이지 당:',
                labelDisplayedRows: ({ from, to, count }) =>
                  `${from}–${to} / ${count}`,
              },
            }}
            sx={{
              border: 'none',
              '& .MuiDataGrid-cell:focus': { outline: 'none' },
            }}
          />
        </div>
      </main>
    </div>
  );
}
```

---

## 8. 로그인 · 회원가입 페이지 (shadcn/ui Form)

### 8.1 LoginPage.jsx

```jsx
// src/components/auth/LoginPage.jsx
import { useState } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { Input }  from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Label }  from '@/components/ui/label';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { useAuthStore } from '../../store/authStore';

export default function LoginPage() {
  const navigate = useNavigate();
  const { login } = useAuthStore();

  const [email,    setEmail]    = useState('');
  const [password, setPassword] = useState('');
  const [error,    setError]    = useState('');
  const [loading,  setLoading]  = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');

    // 클라이언트 유효성 검사
    if (!email || !password) {
      setError('이메일과 비밀번호를 입력해주세요.');
      return;
    }

    setLoading(true);
    try {
      await login(email, password);
      navigate('/books');
    } catch {
      setError('이메일 또는 비밀번호가 올바르지 않습니다.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-slate-50">
      <Card className="w-full max-w-sm">
        <CardHeader>
          <CardTitle className="text-center text-xl">📚 도서관 로그인</CardTitle>
        </CardHeader>

        <CardContent>
          <form onSubmit={handleSubmit} className="space-y-4">
            <div className="space-y-1">
              <Label htmlFor="email">이메일</Label>
              <Input
                id="email"
                type="email"
                placeholder="example@email.com"
                value={email}
                onChange={(e) => setEmail(e.target.value)}
              />
            </div>

            <div className="space-y-1">
              <Label htmlFor="password">비밀번호</Label>
              <Input
                id="password"
                type="password"
                placeholder="비밀번호 입력"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
              />
            </div>

            {/* 오류 메시지 */}
            {error && (
              <p className="text-sm text-red-500">{error}</p>
            )}

            <Button type="submit" className="w-full" disabled={loading}>
              {loading ? '로그인 중...' : '로그인'}
            </Button>

            <p className="text-sm text-center text-slate-500">
              계정이 없으신가요?{' '}
              <Link to="/register" className="text-primary hover:underline">
                회원가입
              </Link>
            </p>
          </form>
        </CardContent>
      </Card>
    </div>
  );
}
```

### 8.2 RegisterPage.jsx

```jsx
// src/components/auth/RegisterPage.jsx
import { useState } from 'react';
import { useNavigate, Link } from 'react-router-dom';
import { Input }  from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Label }  from '@/components/ui/label';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { useAuthStore } from '../../store/authStore';

export default function RegisterPage() {
  const navigate = useNavigate();
  const { register } = useAuthStore();

  const [form, setForm] = useState({
    name: '', email: '', password: '', confirmPassword: '',
  });
  const [error,   setError]   = useState('');
  const [loading, setLoading] = useState(false);

  const handleChange = (field) => (e) =>
    setForm(prev => ({ ...prev, [field]: e.target.value }));

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError('');

    if (!form.name || !form.email || !form.password) {
      setError('모든 필드를 입력해주세요.');
      return;
    }
    if (form.password !== form.confirmPassword) {
      setError('비밀번호가 일치하지 않습니다.');
      return;
    }

    setLoading(true);
    try {
      await register({ name: form.name, email: form.email, password: form.password });
      alert('회원가입이 완료되었습니다. 로그인해주세요.');
      navigate('/login');
    } catch {
      setError('회원가입 중 오류가 발생했습니다.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-slate-50">
      <Card className="w-full max-w-sm">
        <CardHeader>
          <CardTitle className="text-center text-xl">📚 회원가입</CardTitle>
        </CardHeader>

        <CardContent>
          <form onSubmit={handleSubmit} className="space-y-4">
            {[
              { field: 'name',            label: '이름',          type: 'text',     placeholder: '홍길동' },
              { field: 'email',           label: '이메일',        type: 'email',    placeholder: 'example@email.com' },
              { field: 'password',        label: '비밀번호',      type: 'password', placeholder: '8자 이상' },
              { field: 'confirmPassword', label: '비밀번호 확인', type: 'password', placeholder: '동일하게 입력' },
            ].map(({ field, label, type, placeholder }) => (
              <div key={field} className="space-y-1">
                <Label htmlFor={field}>{label}</Label>
                <Input
                  id={field}
                  type={type}
                  placeholder={placeholder}
                  value={form[field]}
                  onChange={handleChange(field)}
                />
              </div>
            ))}

            {error && <p className="text-sm text-red-500">{error}</p>}

            <Button type="submit" className="w-full" disabled={loading}>
              {loading ? '처리 중...' : '회원가입'}
            </Button>

            <p className="text-sm text-center text-slate-500">
              이미 계정이 있으신가요?{' '}
              <Link to="/login" className="text-primary hover:underline">
                로그인
              </Link>
            </p>
          </form>
        </CardContent>
      </Card>
    </div>
  );
}
```

---

## 9. Header 및 네비게이션 (shadcn/ui)

```jsx
// src/components/common/Header.jsx
import { Link, useNavigate } from 'react-router-dom';
import { Button } from '@/components/ui/button';
import { Badge }  from '@/components/ui/badge';
import { useAuthStore } from '../../store/authStore';

export default function Header() {
  const navigate = useNavigate();
  const { email, logout } = useAuthStore();

  const handleLogout = () => {
    logout();
    navigate('/login');
  };

  return (
    <header className="bg-white border-b shadow-sm">
      <div className="max-w-6xl mx-auto px-6 h-14 flex items-center justify-between">
        {/* 로고 */}
        <Link to="/books" className="font-bold text-lg text-primary flex items-center gap-2">
          📚 도서관
        </Link>

        {/* 네비게이션 */}
        <nav className="flex items-center gap-4">
          <Link
            to="/books"
            className="text-sm text-slate-600 hover:text-primary transition-colors"
          >
            도서 목록
          </Link>
          <Link
            to="/loans"
            className="text-sm text-slate-600 hover:text-primary transition-colors"
          >
            내 대출 이력
          </Link>
        </nav>

        {/* 사용자 영역 */}
        <div className="flex items-center gap-3">
          {email ? (
            <>
              <span className="text-sm text-slate-500 hidden sm:block">{email}</span>
              <Button variant="outline" size="sm" onClick={handleLogout}>
                로그아웃
              </Button>
            </>
          ) : (
            <>
              <Button variant="ghost" size="sm" asChild>
                <Link to="/login">로그인</Link>
              </Button>
              <Button size="sm" asChild>
                <Link to="/register">회원가입</Link>
              </Button>
            </>
          )}
        </div>
      </div>
    </header>
  );
}
```

---

## 10. 파일 구조 최종 정리

```
src/
├── theme/
│   └── muiTheme.js             # MUI 테마 (색상 토큰을 Tailwind와 일치)
│
├── api/
│   ├── axiosInstance.js        # axios 공통 설정 + 인터셉터
│   ├── authApi.js              # POST /auth/login, /auth/register
│   ├── bookApi.js              # GET /api/books, GET /api/books/:id
│   └── loanApi.js              # POST /api/loans, GET /api/loans/my, PUT /api/loans/:id
│
├── store/
│   ├── authStore.js            # token, email — Zustand
│   ├── bookStore.js            # books, loading, pageNo — Zustand
│   └── loanStore.js            # loans, loading — Zustand
│
├── components/
│   ├── ui/                     # shadcn/ui 자동 생성 컴포넌트 (건드리지 않음)
│   │   ├── button.jsx
│   │   ├── input.jsx
│   │   ├── select.jsx
│   │   ├── badge.jsx
│   │   ├── dialog.jsx
│   │   ├── card.jsx
│   │   └── label.jsx
│   │
│   ├── common/
│   │   └── Header.jsx          # shadcn/ui Button + Badge
│   │
│   ├── auth/
│   │   ├── LoginPage.jsx       # shadcn/ui Card + Input + Button
│   │   └── RegisterPage.jsx    # shadcn/ui Card + Input + Button
│   │
│   ├── book/
│   │   ├── BookSection.jsx     # 도서 목록 페이지 컨테이너
│   │   ├── BookSearch.jsx      # shadcn/ui Input + Select
│   │   ├── BookDataGrid.jsx    # MUI DataGrid + shadcn/ui Badge·Button
│   │   └── LoanDialog.jsx      # shadcn/ui Dialog + Badge
│   │
│   └── loan/
│       └── MyLoanPage.jsx      # MUI DataGrid + shadcn/ui Badge·Button
│
├── App.jsx                     # React Router + ProtectedRoute
└── main.jsx                    # MUI ThemeProvider + React 루트
```

---

## 11. 자주 발생하는 문제 (FAQ)

### Q1. shadcn/ui 컴포넌트가 `@/components/ui/button` 경로로 import 안 됨

```bash
# vite.config.js에 path alias 설정이 필요합니다
npm install -D @types/node
```

```js
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

---

### Q2. MUI DataGrid와 Tailwind CSS가 충돌함

Tailwind의 preflight(reset) CSS가 MUI 스타일을 덮어쓰는 경우가 있습니다.

```css
/* index.css — Tailwind preflight를 비활성화 */
@tailwind base;   /* ← 이 줄이 MUI와 충돌할 수 있음 */
```

**해결 방법:** `@layer base` 안에서 MUI 컴포넌트에만 Tailwind 스타일을 적용하지 않도록 제외합니다.

```css
/* index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* MUI 컴포넌트에서 Tailwind box-sizing 충돌 방지 */
.MuiDataGrid-root *,
.MuiDataGrid-root *::before,
.MuiDataGrid-root *::after {
  box-sizing: border-box;
}
```

---

### Q3. DataGrid `renderCell`에서 shadcn/ui Badge가 세로 중앙 정렬이 안 됨

```jsx
// renderCell은 기본적으로 flex 컨테이너 — alignItems 설정
renderCell: ({ value }) => (
  <div className="flex items-center h-full">
    <Badge variant="secondary">{value}</Badge>
  </div>
),
```

---

### Q4. DataGrid 서버 측 페이지네이션 연결 방법

현재 구현은 클라이언트 측 페이지네이션입니다.
서버에서 페이지 단위로 데이터를 받아오려면:

```jsx
<DataGrid
  rows={books}
  columns={columns}
  rowCount={totalElements}           // 서버의 전체 행 수
  paginationMode="server"            // 서버 페이지네이션 활성화
  paginationModel={{ page: pageNo, pageSize: 10 }}
  onPaginationModelChange={({ page }) => fetchBooks({ page })}
  loading={loading}
/>
```

---

### Q5. MUI 한국어 로케일 전체 적용 방법

```jsx
// main.jsx
import { ThemeProvider, createTheme } from '@mui/material/styles';
import { koKR } from '@mui/x-data-grid/locales';  // DataGrid 한국어
import { koKR as coreKoKR } from '@mui/material/locale';

const theme = createTheme(muiTheme, koKR, coreKoKR);
```

---

> **핵심 정리**
>
> | 역할 | 라이브러리 | 주요 컴포넌트 |
> |------|-----------|--------------|
> | 버튼 · 입력 · 모달 · 배지 | shadcn/ui | Button, Input, Dialog, Badge |
> | 도서 목록 · 대출 이력 테이블 | MUI DataGrid | DataGrid |
> | 색상 통일 | muiTheme.js | `primary: '#3b82f6'` |
> | 전역 상태 | Zustand | bookStore, loanStore, authStore |
