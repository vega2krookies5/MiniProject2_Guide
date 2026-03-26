# React 컴포넌트 설계 가이드
*ReactJS + Vite + React Router + Zustand 환경 최적화*

## 1. React 컴포넌트 설계 원칙

### 1.1 단일 책임 원칙 (Single Responsibility Principle)
- **하나의 컴포넌트는 하나의 역할만 담당**해야 합니다
- 컴포넌트가 너무 많은 기능을 담당하면 분리를 고려하세요
- UI 표시, 데이터 처리, 비즈니스 로직을 명확히 구분하세요

### 1.2 컴포넌트 분류 체계
**Presentational Components (프레젠테이션 컴포넌트)**
- UI 렌더링에만 집중
- props를 통해서만 데이터를 받음
- 상태 관리는 최소화
- 재사용성이 높음

**Container Components (컨테이너 컴포넌트)**
- 데이터 fetching과 상태 관리를 담당
- Zustand store와 연결
- 비즈니스 로직 처리
- Presentational 컴포넌트에 데이터 전달

### 1.3 폴더 구조 원칙
```
src/
├── components/          # 재사용 가능한 UI 컴포넌트
│   ├── common/         # 공통 컴포넌트
│   └── ui/            # 기본 UI 컴포넌트 (Button, Input 등)
├── pages/             # 페이지 컴포넌트
├── hooks/             # 커스텀 훅
├── store/             # Zustand 스토어
├── api/               # API 모듈
└── utils/             # 유틸리티 함수
```

### 1.4 네이밍 컨벤션
- **컴포넌트명**: PascalCase 사용 (예: `BookCard`, `LoanBadge`)
- **파일명**: 컴포넌트명과 동일하게 PascalCase
- **props명**: camelCase 사용
- **이벤트 핸들러**: `handle` 접두어 사용 (예: `handleClick`, `handleSubmit`)

---

## 2. React 컴포넌트 Props 설계 원칙

### 2.1 Props 설계 기본 원칙
**명확하고 직관적인 이름 사용**
- props의 목적이 명확히 드러나는 이름을 선택하세요
- `data` 대신 `book`, `loan` 같은 구체적인 이름 사용

**Props 개수 최소화**
- 5개 이하의 props를 권장합니다
- 너무 많은 props는 객체로 묶어서 전달하세요

**Props 기본값 설정**
- `defaultProps` 또는 ES6 기본 매개변수를 활용하세요
- 예상치 못한 undefined 오류를 방지합니다

### 2.2 Props 타입별 설계 가이드
**객체 Props**
- 관련된 데이터를 하나의 객체로 묶어 전달
- prop drilling을 줄일 수 있습니다

**함수 Props (콜백)**
- `on` 접두어 사용 (예: `onSubmit`, `onClick`)
- 부모 컴포넌트에서 자식의 상태 변화를 감지할 때 사용

**Boolean Props**
- `is`, `has`, `can` 접두어 사용 (예: `isVisible`, `hasError`)
- 기본값을 false로 설정하는 것을 권장

### 2.3 Props Validation
PropTypes를 사용하여 props의 타입을 검증하세요:
```bash
npm install prop-types
```

---

## 3. Zustand 상태 관리 설계 원칙

### 3.1 상태 설계 원칙
**최소한의 상태 유지**
- 계산 가능한 값은 상태에 저장하지 않음
- 컴포넌트 로컬 상태와 글로벌 상태를 명확히 구분
- 서버 데이터는 캐싱 없이 필요할 때 다시 조회

**도메인별 스토어 분리**
- 각 도메인별로 별도의 Zustand 스토어 생성
- bookStore, loanStore, authStore 등 논리적 단위로 분리
- 스토어 간 직접 의존 금지 — 필요하면 `store.getState()` 사용

### 3.2 스토어 설계 가이드
**상태 + 액션을 한 곳에**
- Zustand는 상태와 액션을 하나의 스토어에 정의
- Redux처럼 별도 Action/Reducer 파일이 필요 없음

**비동기 액션 패턴**
```jsx
const useBookStore = create((set, get) => ({
    // 상태
    books: [],
    loading: false,
    error: null,

    // 비동기 액션
    fetchBooks: async (params) => {
        set({ loading: true, error: null });
        try {
            const result = await fetchBooksApi(params);
            set({ books: result.content, loading: false });
        } catch (err) {
            set({ error: err.message, loading: false });
            throw err;
        }
    }
}));
```

### 3.3 비동기 처리 원칙
**try-catch + set 패턴**
- 비동기 작업 시작 시 `set({ loading: true })`
- 성공 시 데이터 업데이트 후 `set({ loading: false })`
- 실패 시 에러 저장 후 `throw err` (컴포넌트에서 Toast 처리)

**로딩 상태 관리**
- 각 스토어별 `loading` 상태로 로딩 UI 표시
- 사용자 경험을 위한 적절한 스켈레톤/스피너 제공

---

## 4. Axios를 활용한 HTTP 통신 설계

### 4.1 API 모듈화 원칙
**기능별 API 파일 분리**
- 도메인별로 별도의 API 파일 생성 (`bookApi.js`, `loanApi.js`)
- 공통 axios 인스턴스는 `axiosInstance.js`에서 관리

**일관된 응답 처리**
- 성공/실패 응답을 일관되게 처리
- 에러 메시지를 사용자 친화적으로 변환

### 4.2 Zustand 스토어와 Axios 연동
**표준화된 API 호출 패턴**
- 스토어 액션 내부에서 API 함수 호출
- try-catch를 사용한 에러 처리
- 적절한 에러 메시지 반환

---

## 5. React Router 라우팅 설계 원칙

### 5.1 라우팅 구조 설계
**계층적 라우팅**
- 중첩 라우팅을 활용한 레이아웃 구성
- 공통 레이아웃 컴포넌트 활용

**동적 라우팅**
- URL 파라미터를 활용한 동적 페이지
- Query Parameter를 통한 필터링

### 5.2 네비게이션 원칙
**프로그래밍 방식 네비게이션**
- `useNavigate` 훅 활용
- 조건부 리다이렉션 구현

**경로 보호**
- 인증이 필요한 라우트 보호
- 권한에 따른 접근 제어

---

## 6. 코드 예시

### 6.1 Presentational Component (도서 카드)

```jsx
// components/ui/BookCard.jsx
import PropTypes from 'prop-types';

const BookCard = ({
  book,
  onBorrow,
  onViewDetail,
  isLoading = false
}) => {
  const handleBorrow = () => onBorrow(book.id);
  const handleViewDetail = () => onViewDetail(book.id);

  if (isLoading) {
    return <div className="book-card loading">Loading...</div>;
  }

  const available = book.availableCopies > 0;

  return (
    <div className="book-card">
      <div className="book-info">
        <h3 className="book-title">{book.title}</h3>
        <p className="book-author">{book.author}</p>
        <p className="book-publisher">{book.publisher}</p>
        <span className={`badge ${available ? 'badge-success' : 'badge-error'}`}>
          {available ? `대출 가능 (${book.availableCopies}권)` : '대출 불가'}
        </span>

        <div className="book-actions">
          <button onClick={handleViewDetail} className="btn btn-secondary">
            상세 보기
          </button>
          <button
            onClick={handleBorrow}
            className="btn btn-primary"
            disabled={!available}
          >
            {available ? '대출 신청' : '재고 없음'}
          </button>
        </div>
      </div>
    </div>
  );
};

BookCard.propTypes = {
  book: PropTypes.shape({
    id: PropTypes.number.isRequired,
    title: PropTypes.string.isRequired,
    author: PropTypes.string.isRequired,
    publisher: PropTypes.string,
    availableCopies: PropTypes.number.isRequired,
  }).isRequired,
  onBorrow: PropTypes.func.isRequired,
  onViewDetail: PropTypes.func.isRequired,
  isLoading: PropTypes.bool
};

export default BookCard;
```

### 6.2 Zustand 스토어 (도서 관리)

```jsx
// store/bookStore.js
import { create } from 'zustand';
import { fetchBooksApi, fetchBookDetailApi } from '../api/bookApi.js';

export const useBookStore = create((set) => ({
    // 상태
    books: [],
    totalPages: 0,
    totalElements: 0,
    pageNo: 0,
    selectedBook: null,
    loading: false,
    error: null,

    // 도서 목록 조회
    fetchBooks: async ({ keyword = '', category = '', page = 0, size = 10 } = {}) => {
        set({ loading: true, error: null });
        try {
            const result = await fetchBooksApi({ keyword, category, page, size });
            set({
                books: result.content,
                totalPages: result.page.totalPages,
                totalElements: result.page.totalElements,
                pageNo: result.page.number,
                loading: false,
            });
        } catch (err) {
            set({ error: err.message, loading: false });
            throw err;
        }
    },

    // 도서 상세 조회
    fetchBookDetail: async (id) => {
        set({ loading: true, error: null });
        try {
            const book = await fetchBookDetailApi(id);
            set({ selectedBook: book, loading: false });
        } catch (err) {
            set({ error: err.message, loading: false });
            throw err;
        }
    },

    clearSelectedBook: () => set({ selectedBook: null }),
}));
```

### 6.3 API 모듈

```jsx
// api/bookApi.js
import axios from './axiosInstance.js';

// 도서 목록 조회 (검색 + 카테고리 + 페이징)
export const fetchBooksApi = async ({ keyword = '', category = '', page = 0, size = 10 }) => {
    const params = new URLSearchParams({ keyword, category, page, size });
    const { data } = await axios.get(`/api/books?${params}`);
    return data.data;  // { content, page: { number, totalPages, totalElements } }
};

// 도서 상세 조회
export const fetchBookDetailApi = async (id) => {
    try {
        const { data } = await axios.get(`/api/books/${id}`);
        return data.data;
    } catch (err) {
        if (err.response?.status === 404) return null;
        throw err;
    }
};
```

```jsx
// api/loanApi.js
import axios from './axiosInstance.js';

// 내 대출 목록 조회
export const fetchMyLoansApi = async () => {
    const { data } = await axios.get('/api/loans/my');
    return data.data;
};

// 대출 신청
export const createLoanApi = async (bookId) => {
    const { data } = await axios.post('/api/loans', { bookId });
    return data.data;
};

// 반납 신청
export const returnBookApi = async (loanId) => {
    const { data } = await axios.patch(`/api/loans/${loanId}/return`);
    return data.data;
};
```

### 6.4 Container Component (도서 목록 페이지)

```jsx
// pages/BookListPage.jsx
import { useEffect, useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useBookStore } from '../store/bookStore.js';
import { useLoanStore } from '../store/loanStore.js';
import BookCard from '../components/ui/BookCard.jsx';

const BookListPage = ({ showToast }) => {
    const navigate = useNavigate();
    const [keyword, setKeyword] = useState('');
    const [category, setCategory] = useState('');

    const { books, loading, error, pageNo, totalPages, fetchBooks } = useBookStore();
    const { createLoan } = useLoanStore();

    // 초기 데이터 로드
    useEffect(() => {
        fetchBooks({ page: 0 });
    }, []);

    // 검색 핸들러
    const handleSearch = () => {
        fetchBooks({ keyword, category, page: 0 });
    };

    // 페이지 변경 핸들러
    const handlePageChange = (newPage) => {
        fetchBooks({ keyword, category, page: newPage });
        window.scrollTo({ top: 0, behavior: 'smooth' });
    };

    // 대출 신청 핸들러
    const handleBorrow = async (bookId) => {
        try {
            await createLoan(bookId);
            showToast('대출 신청이 완료되었습니다.');
            fetchBooks({ keyword, category, page: pageNo }); // 재고 갱신
        } catch (err) {
            showToast(err.message, true);
        }
    };

    // 상세 보기 핸들러
    const handleViewDetail = (bookId) => {
        navigate(`/books/${bookId}`);
    };

    if (loading && books.length === 0) {
        return <div className="loading">도서를 불러오는 중...</div>;
    }

    if (error) {
        return <div className="error">{error}</div>;
    }

    return (
        <div className="book-list-page">
            <h1>도서 목록</h1>

            {/* 검색 필터 */}
            <div className="search-bar">
                <input
                    type="text"
                    placeholder="제목 또는 저자 검색"
                    value={keyword}
                    onChange={e => setKeyword(e.target.value)}
                />
                <select value={category} onChange={e => setCategory(e.target.value)}>
                    <option value="">전체 카테고리</option>
                    <option value="소설">소설</option>
                    <option value="IT">IT</option>
                    <option value="역사">역사</option>
                    <option value="과학">과학</option>
                </select>
                <button onClick={handleSearch}>검색</button>
            </div>

            {/* 도서 목록 */}
            <div className="book-grid">
                {books.map(book => (
                    <BookCard
                        key={book.id}
                        book={book}
                        onBorrow={handleBorrow}
                        onViewDetail={handleViewDetail}
                        isLoading={loading}
                    />
                ))}
            </div>

            {books.length === 0 && !loading && (
                <div className="no-books">조건에 맞는 도서가 없습니다.</div>
            )}

            {/* 페이지네이션 */}
            <div className="pagination">
                <button disabled={pageNo === 0} onClick={() => handlePageChange(pageNo - 1)}>
                    이전
                </button>
                <span>{pageNo + 1} / {totalPages}</span>
                <button disabled={pageNo + 1 >= totalPages} onClick={() => handlePageChange(pageNo + 1)}>
                    다음
                </button>
            </div>
        </div>
    );
};

export default BookListPage;
```

### 6.5 라우터 설정

```jsx
// App.jsx — Zustand 기반, Provider 불필요
import { useState } from 'react';
import { Routes, Route, NavLink, Navigate, useNavigate } from 'react-router-dom';
import { useAuthStore } from './store/authStore.js';

import Toast          from './components/common/Toast.jsx';
import BookListPage   from './pages/BookListPage.jsx';
import BookDetailPage from './pages/BookDetailPage.jsx';
import LoanListPage   from './pages/LoanListPage.jsx';
import LoginPage      from './components/auth/LoginPage.jsx';
import RegisterPage   from './components/auth/RegisterPage.jsx';

function ProtectedRoute({ children }) {
    const { token } = useAuthStore();
    if (!token) return <Navigate to="/login" replace />;
    return children;
}

function App() {
    const [toast, setToast] = useState({ message: '', type: 'success', visible: false });
    const showToast = (message, isError = false) => {
        setToast({ message, type: isError ? 'error' : 'success', visible: true });
        setTimeout(() => setToast(prev => ({ ...prev, visible: false })), 3000);
    };

    const { token, email, logout } = useAuthStore();
    const navigate = useNavigate();

    const handleLogout = () => { logout(); navigate('/login'); };

    return (
        <div>
            <Toast {...toast} />
            {token && (
                <nav>
                    <NavLink to="/books">도서 목록</NavLink>
                    <NavLink to="/loans">내 대출</NavLink>
                    <span>{email}</span>
                    <button onClick={handleLogout}>로그아웃</button>
                </nav>
            )}

            <Routes>
                <Route path="/login"    element={<LoginPage    showToast={showToast} />} />
                <Route path="/register" element={<RegisterPage showToast={showToast} />} />
                <Route path="/" element={
                    <ProtectedRoute><Navigate to="/books" replace /></ProtectedRoute>
                } />
                <Route path="/books" element={
                    <ProtectedRoute><BookListPage showToast={showToast} /></ProtectedRoute>
                } />
                <Route path="/books/:id" element={
                    <ProtectedRoute><BookDetailPage showToast={showToast} /></ProtectedRoute>
                } />
                <Route path="/loans" element={
                    <ProtectedRoute><LoanListPage showToast={showToast} /></ProtectedRoute>
                } />
            </Routes>
        </div>
    );
}

export default App;
```

> **Redux와의 차이:** `<Provider store={store}>` 래핑이 필요 없습니다.
> Zustand 스토어는 어디서나 `useBookStore()`, `useLoanStore()` 훅으로 바로 사용합니다.

### 6.6 커스텀 훅 예시

```jsx
// hooks/useBooks.js — 도서 검색 + 페이징 복합 훅
import { useState, useCallback } from 'react';
import { useBookStore } from '../store/bookStore.js';

const useBooks = () => {
    const [keyword, setKeyword] = useState('');
    const [category, setCategory] = useState('');

    const { books, loading, error, pageNo, totalPages, fetchBooks } = useBookStore();

    // 검색 실행 (항상 첫 페이지부터)
    const search = useCallback(() => {
        fetchBooks({ keyword, category, page: 0 });
    }, [keyword, category, fetchBooks]);

    // 페이지 변경
    const changePage = useCallback((page) => {
        fetchBooks({ keyword, category, page });
    }, [keyword, category, fetchBooks]);

    return {
        keyword, setKeyword,
        category, setCategory,
        books, loading, error,
        pageNo, totalPages,
        search,
        changePage,
    };
};

export default useBooks;
```

---

## 추가 권장사항

### 성능 최적화
- React.memo를 활용한 불필요한 리렌더링 방지
- useCallback과 useMemo를 적절히 활용
- 큰 리스트는 가상화(virtualization) 고려

### 코드 품질
- ESLint와 Prettier 설정으로 일관된 코드 스타일 유지
- PropTypes를 통한 타입 검증
- 의미있는 컴포넌트명과 변수명 사용

### 사용자 경험
- 로딩 상태와 에러 상태를 명확히 표시
- 적절한 피드백과 알림 제공
- 접근성(Accessibility) 고려한 마크업
