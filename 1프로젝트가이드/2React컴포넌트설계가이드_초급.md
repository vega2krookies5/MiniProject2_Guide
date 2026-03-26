# React 컴포넌트 설계 가이드
**ReactJS + ECMAScript JSX + Vite + React Router + Zustand + Axios 환경**

## 1. React 컴포넌트 설계 원칙

### 1.1 단일 책임 원칙 (Single Responsibility Principle)
- **원칙**: 하나의 컴포넌트는 하나의 명확한 책임만을 가져야 합니다
- **권고사항**: 
  - 컴포넌트가 50줄을 넘어가면 분리를 고려하세요
  - 컴포넌트 이름만 봐도 기능을 알 수 있도록 명명하세요
  - 여러 기능이 섞여있다면 각각을 별도 컴포넌트로 분리하세요

### 1.2 컴포넌트 계층 구조
- **원칙**: 명확한 부모-자식 관계를 유지하고, 깊은 중첩을 피하세요
- **권고사항**:
  - 컴포넌트 트리의 깊이는 5단계를 넘지 않도록 하세요
  - 형제 컴포넌트 간 직접 통신을 피하고, 부모를 통해 소통하세요
  - 공통 부모에서 상태를 관리하세요

### 1.3 재사용성과 확장성
- **원칙**: 컴포넌트는 재사용 가능하고 확장 가능하게 설계해야 합니다
- **권고사항**:
  - UI 컴포넌트와 비즈니스 로직 컴포넌트를 분리하세요
  - 공통 컴포넌트는 `/components/common/` 폴더에 관리하세요
  - 컴포넌트에 기본값(defaultProps)을 제공하세요

### 1.4 파일 구조 및 네이밍
```
src/
├── components/
│   ├── common/          # 재사용 가능한 공통 컴포넌트 (LoadingSpinner, ErrorMessage 등)
│   ├── layout/          # 레이아웃 컴포넌트 (Header, Footer)
│   └── ui/              # shadcn/ui 컴포넌트 (Button, Badge, Dialog 등)
├── pages/               # 페이지 컴포넌트
│   ├── BookList/        # 도서 목록 페이지
│   │   ├── index.jsx
│   │   └── components/  # 페이지 전용 (BookDataGrid, LoanDialog, CategoryFilter 등)
│   ├── MyLoan/          # 내 대출이력 페이지
│   ├── Login/           # 로그인 페이지
│   └── Register/        # 회원가입 페이지
├── api/                 # axios 인스턴스 + API 함수
│   ├── axiosInstance.js # 공통 axios 설정 + 인터셉터
│   ├── authApi.js       # 인증 API
│   ├── bookApi.js       # 도서 API
│   └── loanApi.js       # 대출 API
├── store/               # Zustand 스토어 (전역 상태 관리)
│   ├── authStore.js     # 인증 상태 (token, email)
│   ├── bookStore.js     # 도서 목록 상태 (books, loading, fetchBooks)
│   └── loanStore.js     # 대출 상태 (loans, createLoan, returnBook)
└── App.jsx              # 라우팅 + ProtectedRoute
```

## 2. React 컴포넌트 Props 설계 원칙

### 2.1 Props 명명 규칙
- **원칙**: 명확하고 일관된 props 이름을 사용하세요
- **권고사항**:
  - 불린 타입은 `is`, `has`, `can`, `should`로 시작
  - 이벤트 핸들러는 `on`으로 시작 (onClick, onSubmit)
  - 데이터 props는 명사형으로 작성
  - 함수 props는 동사형으로 작성

### 2.2 Props 최소화
- **원칙**: 필요한 최소한의 props만 전달하세요
- **권고사항**:
  - props가 5개를 넘어가면 객체로 묶어서 전달 고려
  - 불필요한 props 전달을 피하세요
  - props drilling이 3단계를 넘어가면 Context API나 Redux 사용 고려

### 2.3 Props 검증
- **원칙**: PropTypes를 사용하여 props를 검증하세요
- **권고사항**:
  - 모든 컴포넌트에 PropTypes 정의
  - 필수 props는 `isRequired` 명시
  - 기본값은 `defaultProps`로 제공

### 2.4 Props 구조화
- **원칙**: 관련된 props는 객체로 그룹화하세요
- **권고사항**:
  - 스타일 관련 props는 `style` 객체로 통합
  - 설정 관련 props는 `config` 객체로 통합
  - 이벤트 핸들러들은 `handlers` 객체로 통합 고려

## 3. Zustand 상태관리 설계 원칙

### 3.1 상태 분리 원칙
- **원칙**: 로컬 상태와 글로벌 상태를 명확히 구분하세요
- **글로벌 상태 (Zustand)**: 여러 컴포넌트에서 공유되는 상태
  - 사용자 인증 정보 (token, email)
  - API에서 가져온 데이터 (도서 목록, 대출 목록)
  - 페이징 상태 (pageNo, totalPages)
- **로컬 상태 (useState)**: 단일 컴포넌트 안에서만 사용
  - 폼 입력값 (name, email, password)
  - 모달 열림/닫힘 상태
  - 로딩 상태

### 3.2 Zustand 스토어 설계 원칙
- **원칙**: 도메인(기능) 단위로 스토어를 분리하세요
- **권고사항**:
  - `authStore.js` → 인증 상태 (token, email, login, logout)
  - `bookStore.js` → 도서 목록, 검색/필터, 페이징, fetchBooks 액션
  - `loanStore.js` → 대출이력, createLoan, fetchMyLoans, returnBook 액션
  - 스토어가 커지면 기능별로 추가 분리

### 3.3 스토어 구조 템플릿

```js
// store/[도메인]Store.js — 기본 구조
import { create } from 'zustand';
import { [도메인]Api } from '../api/[도메인]Api.js';

const api = new [도메인]Api();

export const use[도메인]Store = create((set, get) => ({
    // ── 상태 (State) ────────────────────────────────
    items: [],
    selectedItem: null,
    loading: false,
    error: null,
    // 페이징
    pageNo: 0,
    pageSize: 5,
    totalPages: 0,
    totalElements: 0,

    // ── 액션 (Actions) ──────────────────────────────
    // 목록 조회 (페이징)
    loadPage: async (pageNo = 0) => {
        set({ loading: true, error: null });
        try {
            const data = await api.getPage({ pageNo, pageSize: get().pageSize });
            set({
                items: data.content,
                pageNo: data.pageNo,
                totalPages: data.totalPages,
                totalElements: data.totalElements,
            });
        } catch (err) {
            set({ error: err.message });
        } finally {
            set({ loading: false });
        }
    },

    // 단건 생성
    create: async (itemData) => {
        const data = await api.create(itemData);
        await get().loadPage(get().pageNo);  // 목록 새로고침
        return data;
    },

    // 단건 수정
    update: async (id, itemData) => {
        const data = await api.update(id, itemData);
        await get().loadPage(get().pageNo);
        return data;
    },

    // 단건 삭제
    delete: async (id) => {
        await api.delete(id);
        await get().loadPage(get().pageNo);
    },

    // 선택 항목 관리
    selectItem: (item) => set({ selectedItem: item }),
    clearSelected: () => set({ selectedItem: null }),
}));
```

### 3.4 비동기 작업 패턴

```js
// 컴포넌트에서 스토어 사용
import { useBookStore } from '../../store/bookStore.js';
import { useLoanStore } from '../../store/loanStore.js';

export default function BookListPage() {
    const { books, loading, pageNo, fetchBooks } = useBookStore();

    useEffect(() => {
        fetchBooks({ keyword: '', category: '전체', page: 0 });  // 마운트 시 첫 페이지 로드
    }, []);

    const handleLoan = async (bookId) => {
        try {
            await loanStore.createLoan(bookId);
            toast.success('대출 신청이 완료되었습니다');
        } catch (err) {
            toast.error(err.message);  // 서버 에러 메시지 표시
        }
    };

    if (loading) return <LoadingSpinner />;

    return (/* JSX */);
}
```

## 4. 실제 코드 예시

### 4.1 컴포넌트 설계 예시

#### 공통 버튼 컴포넌트
```jsx
// components/common/Button/Button.jsx
import PropTypes from 'prop-types';
import './Button.css';

const Button = ({ 
  children, 
  variant = 'primary', 
  size = 'medium', 
  disabled = false, 
  loading = false,
  onClick,
  type = 'button',
  className = '',
  ...rest 
}) => {
  const buttonClass = `
    btn 
    btn--${variant} 
    btn--${size} 
    ${disabled ? 'btn--disabled' : ''} 
    ${loading ? 'btn--loading' : ''} 
    ${className}
  `.trim();

  const handleClick = (e) => {
    if (disabled || loading) return;
    onClick?.(e);
  };

  return (
    <button
      type={type}
      className={buttonClass}
      onClick={handleClick}
      disabled={disabled || loading}
      {...rest}
    >
      {loading ? (
        <>
          <span className="btn__spinner" />
          Loading...
        </>
      ) : (
        children
      )}
    </button>
  );
};

Button.propTypes = {
  children: PropTypes.node.isRequired,
  variant: PropTypes.oneOf(['primary', 'secondary', 'danger']),
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  disabled: PropTypes.bool,
  loading: PropTypes.bool,
  onClick: PropTypes.func,
  type: PropTypes.oneOf(['button', 'submit', 'reset']),
  className: PropTypes.string,
};

export default Button;
```

#### 도서 카드 컴포넌트
```jsx
// pages/BookList/components/BookCard.jsx
import PropTypes from 'prop-types';
import { Badge } from '@/components/ui/badge';
import { Button } from '@/components/ui/button';

const BookCard = ({ book, onLoan, isLoading = false }) => {
  const { id, title, author, category, availableCopies } = book;

  const handleLoan = () => {
    onLoan?.(book);
  };

  return (
    <div className="rounded-lg border p-4 space-y-2">
      <div>
        <h3 className="font-semibold text-base">{title}</h3>
        <p className="text-sm text-neutral-500">{author}</p>
        <span className="text-xs text-neutral-400">{category}</span>
      </div>

      <div className="flex items-center justify-between">
        {/* 재고 상태 뱃지 */}
        <Badge variant={availableCopies > 0 ? 'success' : 'destructive'}>
          {availableCopies > 0 ? `●${availableCopies}권 대출 가능` : '○ 품절'}
        </Badge>

        {/* 대출 버튼 */}
        <Button
          size="sm"
          disabled={availableCopies === 0 || isLoading}
          onClick={handleLoan}
        >
          {availableCopies > 0 ? '대출 신청' : '품절'}
        </Button>
      </div>
    </div>
  );
};

BookCard.propTypes = {
  book: PropTypes.shape({
    id: PropTypes.number.isRequired,
    title: PropTypes.string.isRequired,
    author: PropTypes.string.isRequired,
    category: PropTypes.string,
    availableCopies: PropTypes.number.isRequired,
  }).isRequired,
  onLoan: PropTypes.func,
  isLoading: PropTypes.bool,
};

export default BookCard;
```

#### 도서 목록 페이지 컴포넌트 (Zustand 사용)
```jsx
// pages/BookList/index.jsx
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { useBookStore } from '../../store/bookStore.js';
import { useAuthStore } from '../../store/authStore.js';
import BookCard from './components/BookCard';
import LoanDialog from './components/LoanDialog';
import { Input } from '@/components/ui/input';
import { toast } from 'sonner';

const BookListPage = () => {
  // ── 로컬 상태
  const [keyword, setKeyword] = useState('');
  const [selectedBook, setSelectedBook] = useState(null);
  const [isDialogOpen, setIsDialogOpen] = useState(false);

  // ── Zustand 전역 상태
  const { books, loading, fetchBooks } = useBookStore();
  const { token } = useAuthStore();
  const navigate = useNavigate();

  useEffect(() => {
    fetchBooks({ keyword: '', category: '전체', page: 0 });
  }, []);

  const handleLoanClick = (book) => {
    if (!token) {
      toast.error('로그인이 필요합니다');
      navigate('/login');
      return;
    }
    setSelectedBook(book);
    setIsDialogOpen(true);
  };

  if (loading) return <div className="text-center py-10">로딩 중...</div>;

  return (
    <div className="container mx-auto py-6 space-y-4">
      <h1 className="text-2xl font-bold">📚 도서 목록</h1>

      <Input
        placeholder="도서명 또는 저자 검색..."
        value={keyword}
        onChange={(e) => setKeyword(e.target.value)}
        onKeyDown={(e) => e.key === 'Enter' && fetchBooks({ keyword, page: 0 })}
      />

      {books.length === 0 ? (
        <p className="text-center text-neutral-500 py-12">검색 결과가 없습니다.</p>
      ) : (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          {books.map(book => (
            <BookCard
              key={book.id}
              book={book}
              onLoan={handleLoanClick}
            />
          ))}
        </div>
      )}

      {selectedBook && (
        <LoanDialog
          open={isDialogOpen}
          onOpenChange={setIsDialogOpen}
          bookId={selectedBook.id}
          bookTitle={selectedBook.title}
          author={selectedBook.author}
        />
      )}
    </div>
  );
};

export default BookListPage;
```

### 4.2 Zustand 스토어 설계 예시

#### 도서 스토어 (bookStore.js)

```jsx
// store/bookStore.js
import { create } from 'zustand';
import { fetchBooksApi } from '../api/bookApi.js';

export const useBookStore = create((set, get) => ({

    // ── 상태 ──────────────────────────────────────────────────
    books: [],
    loading: false,
    error: null,
    pageNo: 0,
    pageSize: 10,
    totalPages: 0,
    totalElements: 0,

    // ── 도서 목록 조회 (검색 + 필터 + 페이징) ─────────────────
    fetchBooks: async ({ keyword = '', category = '전체', page = 0 } = {}) => {
        set({ loading: true, error: null });
        try {
            const data = await fetchBooksApi({
                keyword, category,
                page,
                size: get().pageSize,
            });
            set({
                books: data.content,
                pageNo: data.page.number,
                totalPages: data.page.totalPages,
                totalElements: data.page.totalElements,
            });
        } catch (err) {
            set({ error: err.message });
            throw err;  // 컴포넌트에서 catch 가능하도록 re-throw
        } finally {
            set({ loading: false });
        }
    },

    // ── 페이지 변경 ────────────────────────────────────────────
    changePage: (page) => {
        const { keyword = '', category = '전체' } = get();
        get().fetchBooks({ keyword, category, page });
    },
}));
```

#### 대출 스토어 (loanStore.js)

```jsx
// store/loanStore.js
import { create } from 'zustand';
import { getMyLoansApi, createLoanApi, returnBookApi } from '../api/loanApi.js';

export const useLoanStore = create((set, get) => ({

    // ── 상태 ──────────────────────────────────────────────────
    loans: [],
    loading: false,
    error: null,

    // ── 내 대출이력 조회 ────────────────────────────────────────
    fetchMyLoans: async () => {
        set({ loading: true, error: null });
        try {
            const data = await getMyLoansApi();
            set({ loans: data });
        } catch (err) {
            set({ error: err.message });
        } finally {
            set({ loading: false });
        }
    },

    // ── 대출 신청 ──────────────────────────────────────────────
    createLoan: async (bookId) => {
        const data = await createLoanApi(bookId);
        return data;  // { loanId, dueDate, status }
    },

    // ── 반납 신청 ──────────────────────────────────────────────
    returnBook: async (loanId) => {
        await returnBookApi(loanId);
        await get().fetchMyLoans();  // 목록 새로고침
    },
}));
```

#### 인증 스토어 (authStore.js)

```jsx
// store/authStore.js
import { create } from 'zustand';
import { AuthApi } from '../api/authApi.js';

const authApi = new AuthApi();

export const TOKEN_KEY = 'auth_token';
export const EMAIL_KEY = 'auth_email';

export const useAuthStore = create((set) => ({

    // localStorage에서 초기값 복원 → 새로고침 후 로그인 유지
    token: localStorage.getItem(TOKEN_KEY) ?? null,
    email: localStorage.getItem(EMAIL_KEY) ?? null,

    // 로그인: 서버 요청 → localStorage 저장 → 스토어 업데이트
    login: async (email, password) => {
        const token = await authApi.login(email, password);
        localStorage.setItem(TOKEN_KEY, token);
        localStorage.setItem(EMAIL_KEY, email);
        set({ token, email });
    },

    // 회원가입
    register: async (userData) => {
        await authApi.register(userData);
    },

    // 로그아웃: localStorage 삭제 → 스토어 초기화
    logout: () => {
        localStorage.removeItem(TOKEN_KEY);
        localStorage.removeItem(EMAIL_KEY);
        set({ token: null, email: null });
    },
}));
```

**Redux vs Zustand 비교:**

| 항목 | Redux(RTK) | Zustand |
|------|-----------|---------|
| 설치 | `@reduxjs/toolkit react-redux` | `zustand` |
| 코드량 | 많음 (slice, actions, extraReducers) | 적음 (create 하나로 완성) |
| Provider | `<Provider store={store}>` 필요 | 불필요 |
| 비동기 | `createAsyncThunk` | 일반 `async` 함수 |
| 학습 곡선 | 높음 | 낮음 |
| 적합 규모 | 대규모 앱 | 중소규모 앱 |

### 4.3 API 함수 예시 (이 프로젝트 패턴)

```jsx
// api/axiosInstance.js — 공통 axios 인스턴스 + 인터셉터
import axios from 'axios';

const axiosInstance = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL,  // .env 파일에서 읽음
    headers: { 'Content-Type': 'application/json' },
});

// 요청 인터셉터: JWT 토큰 자동 첨부
axiosInstance.interceptors.request.use(
    config => {
        const token = localStorage.getItem('auth_token');
        if (token) config.headers.Authorization = `Bearer ${token}`;
        return config;
    },
    error => Promise.reject(error)
);

// 응답 인터셉터: 에러 메시지 변환 + 401 자동 로그아웃
axiosInstance.interceptors.response.use(
    response => response,
    error => {
        if (error.response?.status === 401) {
            localStorage.removeItem('auth_token');
            localStorage.removeItem('auth_email');
            window.location.href = '/login';
            return Promise.reject(error);
        }
        const serverMessage = error.response?.data?.message;
        if (serverMessage) error.message = serverMessage;
        return Promise.reject(error);
    }
);

export default axiosInstance;
```

```jsx
// api/bookApi.js — 도서 API 함수
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
// api/loanApi.js — 대출/반납 API 함수
import axios from './axiosInstance.js';

// 내 대출 목록 조회
export const fetchMyLoansApi = async () => {
    const { data } = await axios.get('/api/loans/my');
    return data.data;  // Loan 배열
};

// 대출 신청
export const createLoanApi = async (bookId) => {
    const { data } = await axios.post('/api/loans', { bookId });
    return data.data;  // 생성된 Loan
};

// 반납 신청
export const returnBookApi = async (loanId) => {
    const { data } = await axios.patch(`/api/loans/${loanId}/return`);
    return data.data;  // 업데이트된 Loan
};
```

### 4.4 커스텀 훅 예시 (Zustand 기반)

Zustand를 사용하면 별도의 커스텀 훅 없이 스토어를 직접 사용할 수 있습니다.
복잡한 로직이 있을 때만 커스텀 훅으로 분리하세요.

```jsx
// 간단한 경우 — 커스텀 훅 없이 스토어 직접 사용
import { useBookStore } from '../../store/bookStore.js';

export default function BookListPage({ showToast }) {
    const { books, loading, pageNo, totalPages, fetchBooks }
        = useBookStore();

    useEffect(() => { fetchBooks({ page: 0 }); }, []);

    const handleBorrow = async (bookId) => {
        try {
            await useLoanStore.getState().createLoan(bookId);
            showToast('대출 신청이 완료되었습니다.');
        } catch (err) {
            showToast(err.message, true);
        }
    };
    // ...
}
```

```jsx
// 복잡한 로직이 있을 때 — 커스텀 훅으로 분리
// hooks/useLoanActions.js
import { useLoanStore } from '../store/loanStore.js';

export const useLoanActions = (showToast) => {
    const { createLoan, returnBook, fetchMyLoans }
        = useLoanStore();

    const handleBorrow = async (bookId) => {
        try {
            await createLoan(bookId);
            showToast('대출 신청이 완료되었습니다.');
            await fetchMyLoans();
        } catch (err) {
            showToast(err.message, true);
            throw err;  // 폼에서 오류 처리가 필요하면 re-throw
        }
    };

    const handleReturn = async (loanId) => {
        try {
            await returnBook(loanId);
            showToast('반납이 완료되었습니다.');
            await fetchMyLoans();
        } catch (err) {
            showToast(err.message, true);
            throw err;
        }
    };

    return { handleBorrow, handleReturn };
};
```

### 4.5 라우터 설정 예시 (ProtectedRoute 포함)

```jsx
// App.jsx — Zustand 기반, Provider 불필요
import { useState } from 'react';
import { Routes, Route, NavLink, Navigate, useNavigate } from 'react-router-dom';
import { useAuthStore } from './store/authStore.js';

import Toast         from './components/common/Toast.jsx';
import BookListPage  from './pages/BookListPage.jsx';
import LoanListPage  from './pages/LoanListPage.jsx';
import LoginPage     from './components/auth/LoginPage.jsx';
import RegisterPage  from './components/auth/RegisterPage.jsx';

// 토큰 없으면 /login으로 보내는 보호막
function ProtectedRoute({ children }) {
    const { token } = useAuthStore();
    if (!token) return <Navigate to="/login" replace />;
    return children;
}

export default function App() {
    const [toast, setToast] = useState({ message: '', type: 'success', visible: false });

    const showToast = (message, isError = false) => {
        setToast({ message, type: isError ? 'error' : 'success', visible: true });
        setTimeout(() => setToast(prev => ({ ...prev, visible: false })), 3000);
    };

    const { token, email, logout } = useAuthStore();
    const navigate = useNavigate();

    const handleLogout = () => {
        logout();
        navigate('/login');
    };

    return (
        <div>
            <Toast {...toast} />

            {/* 로그인 상태일 때만 네비게이션 표시 */}
            {token && (
                <nav>
                    <NavLink to="/books">도서 목록</NavLink>
                    <NavLink to="/loans">내 대출</NavLink>
                    <span>{email}</span>
                    <button onClick={handleLogout}>로그아웃</button>
                </nav>
            )}

            <Routes>
                {/* 공개 경로 */}
                <Route path="/login"    element={<LoginPage    showToast={showToast} />} />
                <Route path="/register" element={<RegisterPage showToast={showToast} />} />

                {/* 보호 경로 — 토큰 없으면 /login으로 이동 */}
                <Route path="/" element={
                    <ProtectedRoute><Navigate to="/books" replace /></ProtectedRoute>
                } />
                <Route path="/books" element={
                    <ProtectedRoute><BookListPage showToast={showToast} /></ProtectedRoute>
                } />
                <Route path="/loans" element={
                    <ProtectedRoute><LoanListPage showToast={showToast} /></ProtectedRoute>
                } />
            </Routes>
        </div>
    );
}
```

> **Redux와의 차이:** `<Provider store={store}>` 래핑이 필요 없습니다.
> Zustand 스토어는 어디서나 `useBookStore()`, `useLoanStore()` 훅으로 바로 사용합니다.

## 5. 추가 권장사항

### 5.1 성능 최적화
- `React.memo`를 사용하여 불필요한 리렌더링 방지
- `useCallback`과 `useMemo`를 적절히 활용
- 큰 리스트에는 가상화 라이브러리 사용 고려

### 5.2 에러 처리
- Error Boundary를 사용하여 컴포넌트 에러 처리
- try-catch를 사용하여 비동기 작업 에러 처리
- 사용자 친화적인 에러 메시지 제공

### 5.3 접근성
- 적절한 ARIA 속성 사용
- 키보드 네비게이션 지원
- 시맨틱 HTML 요소 사용

### 5.4 코드 품질
- ESLint와 Prettier를 사용한 코드 스타일 통일
- 컴포넌트별 PropTypes 정의
- 의미있는 변수명과 함수명 사용

이 가이드를 따라 개발하시면 유지보수하기 쉽고 확장 가능한 React 애플리케이션을 만들 수 있습니다.