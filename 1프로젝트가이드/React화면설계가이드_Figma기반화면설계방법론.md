# Figma 라이브러리 기반 React 화면 설계 가이드
*체계적인 디자인 시스템 구축을 통한 효율적 개발 워크플로우*

> **이 문서의 위치:** `React화면설계가이드_입문자를위한화면설계시작하기.md`의 **STEP 2(와이어프레임)** 이후,
> Figma를 도입해 디자인을 더 체계적으로 발전시키고 싶을 때 참고하는 심화 가이드입니다.
>
> **프로젝트 예시:** 온라인 도서관 시스템
> - 도메인: 도서(Book), 대출(Loan), 회원(Member), 카테고리(Category)
> - 기술 스택: React + React Router + Zustand + Axios + Vite + TailwindCSS
> - UI 라이브러리: shadcn/ui 또는 MUI (선택)
>
> **입문자 가이드와의 연결:**
> ```
> 입문자 가이드 7단계            Figma 활용 시점
> ─────────────────────────────────────────────
> STEP 1  페이지 목록          → Figma 사이트맵 작성
> STEP 2  와이어프레임          → Figma 와이어프레임 (이 문서부터 해당)
> STEP 3  컴포넌트 목록        → Figma Atomic Design 구조
> STEP 4  디자인 토큰          → Figma Variables / Design Token
> STEP 5  UI 라이브러리        → Figma 컴포넌트 라이브러리 선택
> STEP 6  레이아웃 뼈대         → Figma Template → React 코드
> STEP 7  정적 → 동적 완성     → 코드 구현 단계
> ```

---

## 1. Figma 라이브러리 설계 전략

### 1.1 라이브러리 구조 설계 원칙

**계층적 컴포넌트 시스템**
- **Atoms (원자)**: 가장 기본적인 UI 요소
  - Button, Input, Icon, Typography, Color Tokens
  - 더 이상 분해할 수 없는 최소 단위
  - 모든 상위 컴포넌트의 기반이 되는 요소

- **Molecules (분자)**: 원자들의 조합
  - Search Bar (Input + Button + Icon)
  - Form Field (Label + Input + Error Message)
  - Navigation Item (Icon + Text + Badge)
  - 특정 기능을 수행하는 의미있는 조합

- **Organisms (유기체)**: 분자들의 복합체
  - Header (Logo + Navigation + User Menu)
  - Product Card (Image + Title + Price + Action Button)
  - Data Table (Header + Rows + Pagination)
  - 독립적으로 작동하는 완성된 UI 블록

- **Templates (템플릿)**: 레이아웃 구조
  - Page Layout (Header + Content + Footer)
  - Dashboard Layout (Sidebar + Main + Widget Areas)
  - Modal Layout (Overlay + Container + Actions)
  - 콘텐츠 없는 페이지 구조 정의

**Design Token 시스템**
- **Color Tokens**: Primary, Secondary, Semantic, Neutral 팔레트
- **Typography Tokens**: Font Family, Size, Weight, Line Height 체계
- **Spacing Tokens**: 4px 기반 일관된 간격 시스템
- **Border Radius Tokens**: 컴포넌트별 모서리 둥글기 정의
- **Shadow Tokens**: Elevation 시스템을 위한 그림자 레벨

> **도서관 시스템 디자인 토큰 예시 (입문자 가이드 STEP 4와 연결):**
> ```
> Primary  → #3b82f6  (파란색) — 대출 신청 버튼, 링크
> Success  → #22c55e  (초록색) — 반납 완료, 재고 있음
> Warning  → #f59e0b  (주황색) — 연체 임박 경고
> Danger   → #ef4444  (빨간색) — 연체, 재고 없음(대출불가)
> Neutral  → #64748b  (회색)   — 보조 텍스트, 테두리
> ```

### 1.2 컴포넌트 변형(Variants) 설계 전략

**상태 기반 변형**
- **Interactive States**: Default, Hover, Active, Focus, Disabled
- **Size Variants**: XS, SM, MD, LG, XL
- **Theme Variants**: Light, Dark
- **Type Variants**: Primary, Secondary, Tertiary, Destructive

**props 매핑 전략**
- Figma의 Component Properties → React Props 1:1 대응
- Boolean Properties → React Boolean Props
- Instance Swap Properties → React Children 또는 Icon Props
- Text Properties → React String Props

### 1.3 반응형 설계 체계

**Breakpoint 시스템**
- **Mobile First**: 320px부터 시작
- **Tablet**: 768px 이상
- **Desktop**: 1024px 이상
- **Large Desktop**: 1440px 이상

**Auto Layout 활용 전략**
- **Flexible Containers**: Hug Contents vs Fill Container
- **Direction Control**: Horizontal vs Vertical Stacking
- **Gap System**: 일관된 간격 시스템
- **Padding System**: 컴포넌트 내부 여백 표준화

---

## 2. 라이브러리별 설계 접근법

### 2.1 Material Design 기반 설계

**Material UI (MUI) 호환 설계**
- **Material Design 3 (M3) 원칙 준수**
  - Dynamic Color System 활용
  - Elevation을 통한 계층 표현
  - Motion과 State Layer 고려

- **Component Anatomy 매핑**
  - Container → MUI Box/Paper 컴포넌트
  - Content → Typography 컴포넌트
  - Actions → Button/IconButton 컴포넌트
  - Feedback → Alert/Snackbar 컴포넌트

- **Theme Provider 고려사항**
  - Custom Theme 토큰을 Figma Variables로 정의
  - Palette, Typography, Spacing 일관성 유지
  - Component Override 최소화를 위한 표준 준수

### 2.2 Chakra UI 기반 설계

**Simple, Modular, Accessible 철학 반영**
- **Style Props 시스템 호환**
  - Margin, Padding → m, p props 매핑
  - Color → bg, color props 매핑
  - Size → w, h props 매핑

- **Compound Components 설계**
  - Modal → Modal + ModalOverlay + ModalContent + ModalHeader...
  - Accordion → Accordion + AccordionItem + AccordionButton...
  - Menu → Menu + MenuButton + MenuList + MenuItem...

### 2.3 Ant Design 기반 설계

**Enterprise-Grade UI 고려사항**
- **Data Dense Interface 최적화**
  - 테이블, 폼, 대시보드 중심 설계
  - 정보 밀도와 가독성 균형
  - 복잡한 인터랙션 패턴 지원

- **Form Design System**
  - Form.Item 구조 반영
  - Validation State 시각화
  - Field Grouping과 Layout 패턴

---

## 3. 화면 설계 워크플로우

### 3.1 설계 단계별 프로세스

**1단계: 리서치 및 기획**
- **사용자 여정 매핑**: 주요 태스크 플로우 정의
- **정보 구조 설계**: 콘텐츠 우선순위와 계층 구조
- **기능 요구사항 분석**: 필요한 컴포넌트와 인터랙션 식별

**2단계: 와이어프레임 설계**
- **Layout Grid 정의**: 12컬럼 또는 16컬럼 그리드 시스템
- **Content Blocks 배치**: 주요 콘텐츠 영역 구분
- **Navigation Flow**: 페이지 간 연결과 사용자 동선

**3단계: Visual Design**
- **Design Token 적용**: 색상, 타이포그래피, 간격 체계화
- **Component Library 활용**: 기존 컴포넌트 재사용 우선
- **Brand Identity 반영**: 브랜드 가이드라인과 일치성 확보

**4단계: 프로토타이핑**
- **Interactive Prototype**: 주요 사용자 플로우 시연
- **Micro-interactions**: 세부 인터랙션과 피드백 정의
- **Error Handling**: 예외 상황과 에러 상태 설계

### 3.2 컴포넌트 문서화 전략

**Component Specification**
- **Purpose**: 컴포넌트 사용 목적과 적용 상황
- **Variants**: 모든 변형과 상태 정의
- **Props**: 속성과 기본값, 필수/선택 구분
- **Usage Guidelines**: 사용법과 제약사항
- **Accessibility Notes**: 접근성 고려사항

**Design-Dev Handoff 최적화**
- **Auto Layout 표준화**: 개발자가 이해하기 쉬운 구조
- **Naming Convention**: 일관된 네이밍으로 혼란 방지
- **Asset Export**: 최적화된 이미지와 아이콘 제공

---

## 4. 반응형 설계 전략

### 4.1 모바일 우선 설계 원칙

**Progressive Enhancement**
- **Core Functionality**: 모든 디바이스에서 작동하는 기본 기능
- **Enhanced Experience**: 큰 화면에서 추가되는 기능과 콘텐츠
- **Touch-First Interaction**: 터치 인터페이스 우선 고려

**Content Prioritization**
- **Primary Actions**: 가장 중요한 액션을 쉽게 접근 가능한 위치
- **Secondary Content**: 접기/펼치기 또는 별도 화면으로 처리
- **Progressive Disclosure**: 정보를 단계적으로 노출

### 4.2 브레이크포인트 전환 전략

**Layout Transformation**
- **Navigation Pattern**: 탭 → 사이드바 → 상단 네비게이션
- **Content Layout**: 단일 컬럼 → 다중 컬럼 → 복합 레이아웃
- **Typography Scale**: 모바일 최적화 → 데스크톱 확장

**Component Adaptation**
- **Card Layout**: 리스트 뷰 → 그리드 뷰
- **Table Design**: 카드 형태 → 전통적 테이블
- **Form Layout**: 세로 배치 → 가로 배치

---

## 5. 사용자 경험 설계 고려사항

### 5.1 인터랙션 설계 원칙

**Feedback Systems**
- **Immediate Feedback**: 사용자 액션에 대한 즉각적 반응 (0.1초 내)
- **Progress Indication**: 로딩과 진행 상황의 명확한 표시
- **Error Recovery**: 오류 발생 시 복구 방법 안내

**State Management**
- **Loading States**: 스켈레톤, 스피너, 진행률 표시
- **Empty States**: 빈 화면에 대한 안내와 액션 유도
- **Error States**: 사용자 친화적 오류 메시지와 해결 방안

### 5.2 접근성 설계 고려사항

**Universal Design**
- **Color Contrast**: WCAG 2.1 AA 레벨 대비비 준수 (4.5:1 이상)
- **Typography**: 최소 16px 본문 크기, 명확한 계층 구조
- **Touch Target**: 최소 44px × 44px 터치 영역 확보

**Assistive Technology**
- **Screen Reader**: 의미있는 레이블과 구조적 마크업
- **Keyboard Navigation**: 논리적 탭 순서와 포커스 표시
- **Voice Control**: 음성 명령으로 조작 가능한 인터페이스

---

## 6. 실제 설계 예시 — 온라인 도서관 시스템

### 6.1 도서 목록 페이지 (BookSection) 설계

**Atomic Design으로 페이지 구조 분석**
```
Header (Organism)
├── Logo (Atom)                      ← "📚 도서관" 텍스트 + 아이콘
├── Navigation (Molecule)            ← "도서 목록" | "내 대출 이력" 탭
└── UserMenu (Molecule)              ← 로그인 시: 이메일 + 로그아웃 버튼
                                        비로그인 시: 로그인 버튼

BookSearch (Organism)
├── SearchInput (Molecule)           ← 텍스트 입력 + 검색 아이콘
│   ├── Input (Atom)
│   └── IconButton (Atom)
└── CategoryFilter (Molecule)        ← 카테고리 드롭다운
    ├── Select (Atom)
    └── Label (Atom)

BookList (Organism)
├── BookListRow (Molecule) × N       ← 도서 한 행 (반복)
│   ├── BookTitle (Atom)             ← 제목 (bold)
│   ├── BookAuthor (Atom)            ← 저자
│   ├── CategoryBadge (Atom)         ← 카테고리 태그
│   ├── StockBadge (Atom)            ← 재고 수량 (0이면 빨간색)
│   └── LoanButton (Atom)            ← [대출 신청] 버튼 (재고 0이면 비활성)
└── EmptyState (Molecule)            ← 검색 결과 없을 때 안내 메시지

Pagination (Molecule)
├── PrevButton (Atom)
├── PageNumbers (Atom) × N
└── NextButton (Atom)

LoanModal (Organism)                 ← 대출 신청 클릭 시 표시
├── ModalOverlay (Atom)
├── ModalHeader (Molecule)           ← "대출 신청" + 닫기 버튼
├── BookInfo (Molecule)              ← 도서 제목/저자 (읽기 전용)
├── LoanPeriodInfo (Molecule)        ← "대출 기간: 14일, 반납 예정일: ..."
└── ModalActions (Molecule)          ← [취소] [대출 신청] 버튼
```

**컴포넌트 변형(Variants) 정의**
- **LoanButton Variants**
  - State: Available (파란색), Unavailable (회색·비활성)
- **StockBadge Variants**
  - stock > 0: 초록색 숫자
  - stock == 0: 빨간색 + "대출 불가" 텍스트
- **CategoryBadge Variants**
  - 소설 / IT / 역사 / 기타 — 카테고리별 색상 구분

### 6.2 내 대출 이력 페이지 (MyLoanPage) 설계

**페이지 구조 분석**
```
Header (Organism)                    ← BookSection과 동일 Header

MyLoanList (Organism)
├── LoanStatusTabs (Molecule)        ← "전체" | "대출중" | "반납완료" 탭
├── LoanRow (Molecule) × N           ← 대출 한 행 (반복)
│   ├── BookTitle (Atom)
│   ├── LoanDateRange (Molecule)     ← "2026-03-26 ~ 2026-04-09"
│   ├── StatusBadge (Atom)           ← 대출중(파란색) / 연체(빨간색) / 반납완료(회색)
│   └── ReturnButton (Atom)          ← [반납] 버튼 (대출중일 때만 표시)
└── EmptyState (Molecule)            ← 대출 이력 없을 때 안내

Pagination (Molecule)                ← BookSection과 동일 컴포넌트 재사용
```

**StatusBadge Variants (상태 배지)**
```
ACTIVE   → "대출중"   파란색 배경
OVERDUE  → "연체"     빨간색 배경 (반납 예정일 초과)
RETURNED → "반납완료" 회색 배경
```

### 6.3 레이아웃 템플릿 — App Shell 패턴

**반응형 적응 전략**
- **Desktop (1024px+)**: 상단 Header + 메인 콘텐츠 (테이블 뷰)
- **Tablet (768px-1023px)**: Header 유지, 테이블 가로 스크롤
- **Mobile (~767px)**: 카드 형태로 전환 (테이블 → 카드 뷰)

```
App Shell (Template)
├── Header (Organism)                ← 고정 상단
└── Main Content (Template)
    └── Routes
        ├── /books  → BookSection
        └── /loans  → MyLoanPage     (로그인 필요)
```

---

## 7. 실제 코드 구현 예시

### 7.1 Design Token 시스템 구현 — 도서관 시스템

```javascript
// tokens/colors.js - Figma Variables에서 추출 (도서관 시스템)
export const colors = {
  // Primary Palette — 대출 신청 버튼, 링크, 강조 색상
  primary: {
    50: '#eff6ff',
    100: '#dbeafe',
    500: '#3b82f6',  // Main brand color (Tailwind blue-500)
    600: '#2563eb',  // Hover state
    700: '#1d4ed8',  // Active state
    900: '#1e3a8a',  // Text on light bg
  },

  // Semantic Colors — 도서관 상태 표현
  semantic: {
    success: '#22c55e',   // 반납 완료, 재고 있음 (green-500)
    warning: '#f59e0b',   // 연체 임박, 주의 (amber-500)
    error:   '#ef4444',   // 연체, 재고 없음(대출 불가) (red-500)
    info:    '#3b82f6',   // 대출중 상태 (blue-500)
  },

  // Neutral Palette — 텍스트, 배경, 테두리
  neutral: {
    0:   '#ffffff',
    50:  '#f8fafc',
    100: '#f1f5f9',
    200: '#e2e8f0',
    400: '#94a3b8',
    500: '#64748b',
    700: '#334155',
    900: '#0f172a',
  }
};

// tokens/typography.js
export const typography = {
  fontFamily: {
    primary: ['Inter', 'system-ui', 'sans-serif'],
    mono: ['JetBrains Mono', 'monospace'],
  },
  
  fontSize: {
    xs: '0.75rem',    // 12px
    sm: '0.875rem',   // 14px  
    base: '1rem',     // 16px
    lg: '1.125rem',   // 18px
    xl: '1.25rem',    // 20px
    '2xl': '1.5rem',  // 24px
    '3xl': '1.875rem', // 30px
  },
  
  fontWeight: {
    normal: '400',
    medium: '500', 
    semibold: '600',
    bold: '700',
  },
  
  lineHeight: {
    tight: '1.25',
    normal: '1.5',
    relaxed: '1.75',
  }
};

// tokens/spacing.js
export const spacing = {
  0: '0',
  1: '0.25rem',  // 4px
  2: '0.5rem',   // 8px
  3: '0.75rem',  // 12px
  4: '1rem',     // 16px
  5: '1.25rem',  // 20px
  6: '1.5rem',   // 24px
  8: '2rem',     // 32px
  10: '2.5rem',  // 40px
  12: '3rem',    // 48px
  16: '4rem',    // 64px
  20: '5rem',    // 80px
};
```

### 7.2 Atomic Design 기반 컴포넌트 구현

```jsx
// components/atoms/Button/Button.jsx
import React from 'react';
import PropTypes from 'prop-types';
import { colors, spacing } from '../../../tokens';
import './Button.css';

const Button = ({
  children,
  variant = 'primary',
  size = 'medium',
  disabled = false,
  loading = false,
  icon,
  onClick,
  type = 'button',
  fullWidth = false,
  ...rest
}) => {
  const getButtonStyles = () => {
    const baseStyles = {
      fontFamily: 'Inter, system-ui, sans-serif',
      fontWeight: '500',
      borderRadius: '0.5rem',
      border: 'none',
      cursor: disabled || loading ? 'not-allowed' : 'pointer',
      transition: 'all 0.2s ease-in-out',
      display: 'inline-flex',
      alignItems: 'center',
      justifyContent: 'center',
      gap: spacing[2],
      width: fullWidth ? '100%' : 'auto',
      opacity: disabled ? 0.6 : 1,
    };

    // Size variants (Figma Variants → CSS)
    const sizeStyles = {
      small: {
        padding: `${spacing[2]} ${spacing[3]}`,
        fontSize: '0.875rem',
        lineHeight: '1.25',
      },
      medium: {
        padding: `${spacing[3]} ${spacing[4]}`,
        fontSize: '1rem', 
        lineHeight: '1.5',
      },
      large: {
        padding: `${spacing[4]} ${spacing[6]}`,
        fontSize: '1.125rem',
        lineHeight: '1.5',
      },
    };

    // Variant styles (Figma Component Properties → CSS)
    const variantStyles = {
      primary: {
        backgroundColor: colors.primary[500],
        color: colors.neutral[0],
        '&:hover': {
          backgroundColor: colors.primary[600],
        },
        '&:active': {
          backgroundColor: colors.primary[700],
        },
      },
      secondary: {
        backgroundColor: colors.neutral[100],
        color: colors.neutral[900],
        border: `1px solid ${colors.neutral[200]}`,
        '&:hover': {
          backgroundColor: colors.neutral[200],
        },
      },
      outline: {
        backgroundColor: 'transparent',
        color: colors.primary[500],
        border: `1px solid ${colors.primary[500]}`,
        '&:hover': {
          backgroundColor: colors.primary[50],
        },
      },
      ghost: {
        backgroundColor: 'transparent',
        color: colors.primary[500],
        '&:hover': {
          backgroundColor: colors.primary[50],
        },
      },
      destructive: {
        backgroundColor: colors.semantic.error,
        color: colors.neutral[0],
        '&:hover': {
          backgroundColor: '#dc2626',
        },
      },
    };

    return {
      ...baseStyles,
      ...sizeStyles[size],
      ...variantStyles[variant],
    };
  };

  return (
    <button
      type={type}
      style={getButtonStyles()}
      onClick={onClick}
      disabled={disabled || loading}
      className={`button button--${variant} button--${size} ${fullWidth ? 'button--full-width' : ''}`}
      {...rest}
    >
      {loading && (
        <div className="button__spinner" />
      )}
      {icon && !loading && (
        <span className="button__icon">{icon}</span>
      )}
      <span className="button__text">{children}</span>
    </button>
  );
};

Button.propTypes = {
  children: PropTypes.node.isRequired,
  variant: PropTypes.oneOf(['primary', 'secondary', 'outline', 'ghost', 'destructive']),
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  disabled: PropTypes.bool,
  loading: PropTypes.bool,
  icon: PropTypes.node,
  onClick: PropTypes.func,
  type: PropTypes.oneOf(['button', 'submit', 'reset']),
  fullWidth: PropTypes.bool,
};

export default Button;
```

### 7.3 Molecule 컴포넌트 - Form Field

```jsx
// components/molecules/FormField/FormField.jsx
import React from 'react';
import PropTypes from 'prop-types';
import { colors, spacing, typography } from '../../../tokens';

const FormField = ({
  label,
  children,
  error,
  helperText,
  required = false,
  disabled = false,
  layout = 'vertical', // vertical, horizontal
}) => {
  const fieldId = React.useId();

  const getLabelStyles = () => ({
    display: 'block',
    marginBottom: layout === 'vertical' ? spacing[2] : 0,
    marginRight: layout === 'horizontal' ? spacing[4] : 0,
    fontSize: typography.fontSize.sm,
    fontWeight: typography.fontWeight.medium,
    color: disabled ? colors.neutral[400] : colors.neutral[700],
    minWidth: layout === 'horizontal' ? '120px' : 'auto',
  });

  const getContainerStyles = () => ({
    display: 'flex',
    flexDirection: layout === 'vertical' ? 'column' : 'row',
    alignItems: layout === 'horizontal' ? 'flex-start' : 'stretch',
    marginBottom: spacing[4],
  });

  const getInputContainerStyles = () => ({
    flex: layout === 'horizontal' ? 1 : 'none',
    display: 'flex',
    flexDirection: 'column',
  });

  const getHelperTextStyles = (isError = false) => ({
    marginTop: spacing[1],
    fontSize: typography.fontSize.xs,
    color: isError ? colors.semantic.error : colors.neutral[500],
    lineHeight: typography.lineHeight.normal,
  });

  return (
    <div style={getContainerStyles()}>
      {label && (
        <label htmlFor={fieldId} style={getLabelStyles()}>
          {label}
          {required && (
            <span style={{ color: colors.semantic.error, marginLeft: spacing[1] }}>
              *
            </span>
          )}
        </label>
      )}
      
      <div style={getInputContainerStyles()}>
        {React.cloneElement(children, {
          id: fieldId,
          disabled,
          'aria-invalid': !!error,
          'aria-describedby': error ? `${fieldId}-error` : helperText ? `${fieldId}-helper` : undefined,
        })}
        
        {error && (
          <div
            id={`${fieldId}-error`}
            role="alert"
            style={getHelperTextStyles(true)}
          >
            {error}
          </div>
        )}
        
        {helperText && !error && (
          <div
            id={`${fieldId}-helper`}
            style={getHelperTextStyles(false)}
          >
            {helperText}
          </div>
        )}
      </div>
    </div>
  );
};

FormField.propTypes = {
  label: PropTypes.string,
  children: PropTypes.element.isRequired,
  error: PropTypes.string,
  helperText: PropTypes.string,
  required: PropTypes.bool,
  disabled: PropTypes.bool,
  layout: PropTypes.oneOf(['vertical', 'horizontal']),
};

export default FormField;
```

### 7.4 Organism 컴포넌트 - BookListRow (도서 목록 행)

도서관 시스템의 도서 한 행을 표현하는 Molecule/Organism 컴포넌트입니다.
Figma의 `BookListRow` 컴포넌트가 이 코드로 1:1 매핑됩니다.

```jsx
// components/organisms/BookListRow/BookListRow.jsx
import React from 'react';
import PropTypes from 'prop-types';
import Button from '../../atoms/Button/Button';
import { colors, spacing } from '../../../tokens';

// StockBadge — Atom: 재고 수량 표시 (0이면 빨간색)
const StockBadge = ({ stock }) => (
  <span style={{
    fontWeight: stock === 0 ? '700' : '400',
    color: stock === 0 ? colors.semantic.error : colors.neutral[700],
    fontSize: '0.875rem',
  }}>
    {stock === 0 ? '없음' : stock}
  </span>
);

// CategoryBadge — Atom: 카테고리 태그
const CategoryBadge = ({ name }) => (
  <span style={{
    display: 'inline-block',
    padding: `${spacing[1]} ${spacing[2]}`,
    borderRadius: '9999px',
    fontSize: '0.75rem',
    fontWeight: '500',
    backgroundColor: colors.primary[50],
    color: colors.primary[700],
    border: `1px solid ${colors.primary[100]}`,
  }}>
    {name}
  </span>
);

// BookListRow — Molecule: 도서 목록 한 행
const BookListRow = ({
  book,
  onLoan,
}) => {
  const getCellStyles = (flex = 1) => ({
    flex,
    padding: `${spacing[3]} ${spacing[4]}`,
    display: 'flex',
    alignItems: 'center',
  });

  const getRowStyles = () => ({
    display: 'flex',
    borderBottom: `1px solid ${colors.neutral[200]}`,
    transition: 'background-color 0.15s',
    '&:hover': {
      backgroundColor: colors.neutral[50],
    },
  });

  return (
    <div style={getRowStyles()}>
      {/* 제목 */}
      <div style={{ ...getCellStyles(3), fontWeight: '600', color: colors.neutral[900] }}>
        {book.title}
      </div>

      {/* 저자 */}
      <div style={{ ...getCellStyles(2), color: colors.neutral[700] }}>
        {book.author}
      </div>

      {/* 카테고리 */}
      <div style={getCellStyles(1.5)}>
        <CategoryBadge name={book.categoryName} />
      </div>

      {/* 재고 */}
      <div style={{ ...getCellStyles(1), justifyContent: 'center' }}>
        <StockBadge stock={book.stock} />
      </div>

      {/* 대출 버튼 */}
      <div style={{ ...getCellStyles(1.5), justifyContent: 'center' }}>
        {book.stock > 0 ? (
          <Button
            variant="primary"
            size="small"
            onClick={() => onLoan(book)}
          >
            대출 신청
          </Button>
        ) : (
          <span style={{ fontSize: '0.875rem', color: colors.neutral[400] }}>
            대출 불가
          </span>
        )}
      </div>
    </div>
  );
};

BookListRow.propTypes = {
  book: PropTypes.shape({
    id: PropTypes.number.isRequired,
    title: PropTypes.string.isRequired,
    author: PropTypes.string.isRequired,
    categoryName: PropTypes.string.isRequired,
    stock: PropTypes.number.isRequired,
  }).isRequired,
  onLoan: PropTypes.func.isRequired,
};

export default BookListRow;
```

**LoanModal — Organism: 대출 신청 모달**

```jsx
// components/organisms/LoanModal/LoanModal.jsx
import React from 'react';
import PropTypes from 'prop-types';
import Button from '../../atoms/Button/Button';
import { colors, spacing } from '../../../tokens';

const LoanModal = ({ book, onConfirm, onClose }) => {
  if (!book) return null;

  // 반납 예정일 계산 (오늘 + 14일)
  const dueDate = new Date();
  dueDate.setDate(dueDate.getDate() + 14);
  const dueDateStr = dueDate.toLocaleDateString('ko-KR', {
    year: 'numeric', month: '2-digit', day: '2-digit'
  });

  const overlayStyles = {
    position: 'fixed', inset: 0,
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
    display: 'flex', alignItems: 'center', justifyContent: 'center',
    zIndex: 1000,
  };

  const modalStyles = {
    backgroundColor: colors.neutral[0],
    borderRadius: '0.75rem',
    padding: spacing[6],
    width: '400px',
    maxWidth: '90vw',
    boxShadow: '0 20px 40px rgba(0, 0, 0, 0.15)',
  };

  return (
    <div style={overlayStyles} onClick={onClose}>
      <div style={modalStyles} onClick={e => e.stopPropagation()}>
        {/* 헤더 */}
        <h2 style={{ margin: 0, marginBottom: spacing[4], fontSize: '1.25rem', fontWeight: '700' }}>
          대출 신청
        </h2>

        {/* 도서 정보 (읽기 전용) */}
        <div style={{
          backgroundColor: colors.neutral[50],
          borderRadius: '0.5rem',
          padding: spacing[4],
          marginBottom: spacing[4],
        }}>
          <div style={{ marginBottom: spacing[2] }}>
            <span style={{ fontSize: '0.75rem', color: colors.neutral[500] }}>제목</span>
            <p style={{ margin: 0, fontWeight: '600', color: colors.neutral[900] }}>{book.title}</p>
          </div>
          <div>
            <span style={{ fontSize: '0.75rem', color: colors.neutral[500] }}>저자</span>
            <p style={{ margin: 0, color: colors.neutral[700] }}>{book.author}</p>
          </div>
        </div>

        {/* 대출 기간 안내 */}
        <div style={{
          borderLeft: `3px solid ${colors.primary[500]}`,
          paddingLeft: spacing[3],
          marginBottom: spacing[6],
          color: colors.neutral[700],
          fontSize: '0.875rem',
        }}>
          <p style={{ margin: 0 }}>대출 기간: <strong>14일</strong></p>
          <p style={{ margin: 0, marginTop: spacing[1] }}>
            반납 예정일: <strong>{dueDateStr}</strong>
          </p>
        </div>

        {/* 액션 버튼 */}
        <div style={{ display: 'flex', gap: spacing[3], justifyContent: 'flex-end' }}>
          <Button variant="secondary" size="medium" onClick={onClose}>
            취소
          </Button>
          <Button variant="primary" size="medium" onClick={() => onConfirm(book.id)}>
            대출 신청
          </Button>
        </div>
      </div>
    </div>
  );
};

LoanModal.propTypes = {
  book: PropTypes.shape({
    id: PropTypes.number.isRequired,
    title: PropTypes.string.isRequired,
    author: PropTypes.string.isRequired,
  }),
  onConfirm: PropTypes.func.isRequired,
  onClose: PropTypes.func.isRequired,
};

export default LoanModal;
```

### 7.5 Template 컴포넌트 - Page Layout

```jsx
// components/templates/PageLayout/PageLayout.jsx
import React from 'react';
import PropTypes from 'prop-types';
import Header from '../../organisms/Header/Header';
import Footer from '../../organisms/Footer/Footer';
import Sidebar from '../../organisms/Sidebar/Sidebar';
import { colors, spacing } from '../../../tokens';

const PageLayout = ({
  children,
  variant = 'default', // default, sidebar, full-width
  sidebarContent,
  headerProps = {},
  footerProps = {},
  containerMaxWidth = '1280px',
}) => {
  const getLayoutStyles = () => ({
    minHeight: '100vh',
    display: 'flex',
    flexDirection: 'column',
    backgroundColor: colors.neutral[50],
  });

  const getMainStyles = () => ({
    flex: 1,
    display: 'flex',
    flexDirection: variant === 'sidebar' ? 'row' : 'column',
  });

  const getContentStyles = () => ({
    flex: 1,
    padding: variant === 'full-width' ? 0 : spacing[6],
    maxWidth: variant === 'full-width' ? '100%' : containerMaxWidth,
    margin: variant === 'full-width' ? 0 : '0 auto',
    width: '100%',
    '@media (max-width: 768px)': {
      padding: spacing[4],
    },
  });

  const getSidebarStyles = () => ({
    width: '280px',
    backgroundColor: colors.neutral[0],
    borderRight: `1px solid ${colors.neutral[200]}`,
    '@media (max-width: 1024px)': {
      display: 'none', // 모바일에서 사이드바 숨김
    },
  });

  return (
    <div style={getLayoutStyles()}>
      <Header {...headerProps} />
      
      <main style={getMainStyles()}>
        {variant === 'sidebar' && sidebarContent && (
          <aside style={getSidebarStyles()}>
            {sidebarContent}
          </aside>
        )}
        
        <div style={getContentStyles()}>
          {children}
        </div>
      </main>
      
      <Footer {...footerProps} />
    </div>
  );
};

PageLayout.propTypes = {
  children: PropTypes.node.isRequired,
  variant: PropTypes.oneOf(['default', 'sidebar', 'full-width']),
  sidebarContent: PropTypes.node,
  headerProps: PropTypes.object,
  footerProps: PropTypes.object,
  containerMaxWidth: PropTypes.string,
};

export default PageLayout;
```

### 7.6 반응형 Grid 시스템 구현

```jsx
// components/layout/Grid/Grid.jsx
import React from 'react';
import PropTypes from 'prop-types';
import { spacing } from '../../../tokens';

const Grid = ({
  children,
  columns = { xs: 1, sm: 2, md: 3, lg: 4, xl: 4 },
  gap = 6,
  className = '',
  ...rest
}) => {
  const getGridStyles = () => ({
    display: 'grid',
    gap: spacing[gap],
    gridTemplateColumns: `repeat(${columns.xs}, 1fr)`,
    width: '100%',
    
    // 반응형 그리드 컬럼
    '@media (min-width: 640px)': {
      gridTemplateColumns: `repeat(${columns.sm || columns.xs}, 1fr)`,
    },
    '@media (min-width: 768px)': {
      gridTemplateColumns: `repeat(${columns.md || columns.sm || columns.xs}, 1fr)`,
    },
    '@media (min-width: 1024px)': {
      gridTemplateColumns: `repeat(${columns.lg || columns.md || columns.sm || columns.xs}, 1fr)`,
    },
    '@media (min-width: 1280px)': {
      gridTemplateColumns: `repeat(${columns.xl || columns.lg || columns.md || columns.sm || columns.xs}, 1fr)`,
    },
  });

  return (
    <div
      style={getGridStyles()}
      className={`grid ${className}`}
      {...rest}
    >
      {children}
    </div>
  );
};

const GridItem = ({
  children,
  span = { xs: 1, sm: 1, md: 1, lg: 1, xl: 1 },
  className = '',
  ...rest
}) => {
  const getItemStyles = () => ({
    gridColumn: `span ${span.xs}`,
    
    '@media (min-width: 640px)': {
      gridColumn: `span ${span.sm || span.xs}`,
    },
    '@media (min-width: 768px)': {
      gridColumn: `span ${span.md || span.sm || span.xs}`,
    },
    '@media (min-width: 1024px)': {
      gridColumn: `span ${span.lg || span.md || span.sm || span.xs}`,
    },
    '@media (min-width: 1280px)': {
      gridColumn: `span ${span.xl || span.lg || span.md || span.sm || span.xs}`,
    },
  });

  return (
    <div
      style={getItemStyles()}
      className={`grid-item ${className}`}
      {...rest}
    >
      {children}
    </div>
  );
};

Grid.propTypes = {
  children: PropTypes.node.isRequired,
  columns: PropTypes.shape({
    xs: PropTypes.number,
    sm: PropTypes.number,
    md: PropTypes.number,
    lg: PropTypes.number,
    xl: PropTypes.number,
  }),
  gap: PropTypes.number,
  className: PropTypes.string,
};

GridItem.propTypes = {
  children: PropTypes.node.isRequired,
  span: PropTypes.shape({
    xs: PropTypes.number,
    sm: PropTypes.number,
    md: PropTypes.number,
    lg: PropTypes.number,
    xl: PropTypes.number,
  }),
  className: PropTypes.string,
};

export { Grid, GridItem };
```

### 7.7 실제 페이지 구현 - 도서 목록 페이지

> **입문자를위한화면설계시작하기 STEP 7** 연결 — 정적 더미 데이터 → Zustand 스토어 → 실제 API 순으로 단계적으로 발전시키는 예시입니다.

```jsx
// pages/BookListPage/BookListPage.jsx
import React, { useState, useEffect } from 'react';
import { useSearchParams } from 'react-router-dom';
import PageLayout from '../../components/templates/PageLayout/PageLayout';
import BookListRow from '../../components/organisms/BookListRow/BookListRow';
import LoanModal from '../../components/organisms/LoanModal/LoanModal';
import Button from '../../components/atoms/Button/Button';
import FormField from '../../components/molecules/FormField/FormField';
import { colors, spacing } from '../../tokens';
import { useBookStore } from '../../store/bookStore';
import { useAuthStore } from '../../store/authStore';

// ── STEP 7-1: 정적 더미 데이터 ────────────────────────────────────────────
// 처음에는 아래 DUMMY_BOOKS만 사용해서 레이아웃이 맞는지 확인합니다.
const DUMMY_BOOKS = [
  { id: 1, title: '채식주의자',  author: '한강',   categoryName: '소설', stock: 3 },
  { id: 2, title: '클린코드',    author: '마틴',   categoryName: 'IT',   stock: 0 },
  { id: 3, title: '사피엔스',    author: '하라리', categoryName: '역사', stock: 2 },
  { id: 4, title: '토비의 스프링', author: '이일민', categoryName: 'IT', stock: 1 },
];

const CATEGORIES = ['전체', '소설', 'IT', '역사', '기타'];

const BookListPage = () => {
  const [searchParams, setSearchParams] = useSearchParams();

  // ── STEP 7-2: Zustand 스토어 연결 ─────────────────────────────────────
  // DUMMY_BOOKS 대신 스토어의 books를 사용합니다.
  const { books, loading, pageNo, totalPages, fetchBooks } = useBookStore();
  const { token } = useAuthStore();

  // 로컬 상태 (검색 조건 · 모달)
  const [searchKeyword, setSearchKeyword] = useState(
    searchParams.get('keyword') || ''
  );
  const [selectedCategory, setSelectedCategory] = useState(
    searchParams.get('category') || '전체'
  );
  const [selectedBook, setSelectedBook] = useState(null);   // 대출 모달 대상
  const [isLoanModalOpen, setIsLoanModalOpen] = useState(false);

  // ── STEP 7-3: 실제 API 호출 ────────────────────────────────────────────
  // fetchBooks()가 내부적으로 bookApi.getAll()을 호출합니다.
  useEffect(() => {
    fetchBooks({
      keyword: searchKeyword,
      category: selectedCategory === '전체' ? '' : selectedCategory,
      page: 0,
    });
  }, [searchKeyword, selectedCategory]);

  // URL 동기화 (검색 조건을 브라우저 주소창에 반영)
  const updateSearchParams = (keyword, category) => {
    const params = {};
    if (keyword)              params.keyword  = keyword;
    if (category !== '전체') params.category = category;
    setSearchParams(params);
  };

  const handleKeywordChange = (value) => {
    setSearchKeyword(value);
    updateSearchParams(value, selectedCategory);
  };

  const handleCategoryChange = (value) => {
    setSelectedCategory(value);
    updateSearchParams(searchKeyword, value);
  };

  // 대출 신청 버튼 → 로그인 여부 확인 후 모달 오픈
  const handleLoanRequest = (book) => {
    if (!token) {
      alert('로그인이 필요한 서비스입니다.');
      return;
    }
    setSelectedBook(book);
    setIsLoanModalOpen(true);
  };

  // 사이드바: 검색 + 카테고리 필터
  const sidebarContent = (
    <div style={{ padding: spacing[6] }}>
      <h3 style={{
        margin: `0 0 ${spacing[6]} 0`,
        fontSize: '1.25rem',
        fontWeight: '600',
        color: colors.neutral[900],
      }}>
        도서 검색
      </h3>

      {/* 제목 / 저자 검색 */}
      <FormField label="제목 · 저자 검색">
        <input
          type="text"
          placeholder="검색어를 입력하세요"
          value={searchKeyword}
          onChange={(e) => handleKeywordChange(e.target.value)}
          style={{
            width: '100%',
            padding: `${spacing[3]} ${spacing[4]}`,
            border: `1px solid ${colors.neutral[300]}`,
            borderRadius: '0.5rem',
            fontSize: '1rem',
          }}
        />
      </FormField>

      {/* 카테고리 필터 */}
      <FormField label="카테고리">
        <select
          value={selectedCategory}
          onChange={(e) => handleCategoryChange(e.target.value)}
          style={{
            width: '100%',
            padding: `${spacing[3]} ${spacing[4]}`,
            border: `1px solid ${colors.neutral[300]}`,
            borderRadius: '0.5rem',
            fontSize: '1rem',
          }}
        >
          {CATEGORIES.map(cat => (
            <option key={cat} value={cat}>{cat}</option>
          ))}
        </select>
      </FormField>

      {/* 필터 초기화 */}
      <Button
        variant="outline"
        fullWidth
        onClick={() => {
          setSearchKeyword('');
          setSelectedCategory('전체');
          setSearchParams({});
        }}
        style={{ marginTop: spacing[4] }}
      >
        필터 초기화
      </Button>
    </div>
  );

  // 메인 콘텐츠: 도서 목록 테이블
  const mainContent = (
    <div>
      {/* 페이지 헤더 */}
      <div style={{
        display: 'flex',
        justifyContent: 'space-between',
        alignItems: 'center',
        marginBottom: spacing[6],
      }}>
        <h1 style={{
          margin: 0,
          fontSize: '1.75rem',
          fontWeight: '700',
          color: colors.neutral[900],
        }}>
          도서 목록
        </h1>
        <p style={{ margin: 0, color: colors.neutral[500] }}>
          {books.length}권
        </p>
      </div>

      {/* 도서 테이블 */}
      {loading ? (
        <div style={{
          display: 'flex',
          justifyContent: 'center',
          alignItems: 'center',
          height: '300px',
          color: colors.neutral[500],
        }}>
          도서를 불러오는 중...
        </div>
      ) : books.length === 0 ? (
        <div style={{
          textAlign: 'center',
          padding: spacing[12],
          color: colors.neutral[500],
        }}>
          <p>검색 결과가 없습니다. 다른 검색어를 입력해보세요.</p>
        </div>
      ) : (
        <table style={{ width: '100%', borderCollapse: 'collapse' }}>
          <thead>
            <tr style={{ borderBottom: `2px solid ${colors.neutral[200]}` }}>
              {['제목', '저자', '카테고리', '재고', '대출'].map(col => (
                <th key={col} style={{
                  padding: `${spacing[3]} ${spacing[4]}`,
                  textAlign: 'left',
                  fontSize: '0.875rem',
                  fontWeight: '600',
                  color: colors.neutral[600],
                }}>
                  {col}
                </th>
              ))}
            </tr>
          </thead>
          <tbody>
            {books.map(book => (
              <BookListRow
                key={book.id}
                book={book}
                onLoan={handleLoanRequest}
              />
            ))}
          </tbody>
        </table>
      )}

      {/* 페이지네이션 */}
      {totalPages > 1 && (
        <div style={{
          display: 'flex',
          justifyContent: 'center',
          gap: spacing[2],
          marginTop: spacing[8],
        }}>
          {Array.from({ length: totalPages }, (_, i) => (
            <Button
              key={i}
              variant={pageNo === i ? 'primary' : 'outline'}
              size="small"
              onClick={() => fetchBooks({
                keyword: searchKeyword,
                category: selectedCategory === '전체' ? '' : selectedCategory,
                page: i,
              })}
            >
              {i + 1}
            </Button>
          ))}
        </div>
      )}
    </div>
  );

  return (
    <>
      <PageLayout
        variant="sidebar"
        sidebarContent={sidebarContent}
      >
        {mainContent}
      </PageLayout>

      {/* 대출 신청 모달 — selectedBook이 있을 때만 렌더링 */}
      {isLoanModalOpen && selectedBook && (
        <LoanModal
          book={selectedBook}
          onConfirm={() => {
            setIsLoanModalOpen(false);
            setSelectedBook(null);
          }}
          onClose={() => {
            setIsLoanModalOpen(false);
            setSelectedBook(null);
          }}
        />
      )}
    </>
  );
};

export default BookListPage;
```

> **Figma → 코드 연결 포인트**
>
> | Figma 컴포넌트 | React 컴포넌트 | Props |
> |---|---|---|
> | BookRow (Organism) | `BookListRow` | `book`, `onLoan` |
> | StockBadge (Atom) | `StockBadge` (내부) | `stock` |
> | CategoryBadge (Atom) | `CategoryBadge` (내부) | `name` |
> | LoanButton (Atom) | `LoanButton` (내부) | `disabled={stock===0}` |
> | LoanModal (Organism) | `LoanModal` | `book`, `onConfirm`, `onClose` |
> | CategoryFilter (Molecule) | `<select>` (사이드바) | `value`, `onChange` |

### 7.8 Figma Dev Mode 연동 최적화

```jsx
// utils/figmaTokens.js - Figma Variables를 JavaScript로 동기화
// Figma 변수 패널의 값을 그대로 붙여넣으세요. (도서관 시스템 디자인 토큰 기준)
export const figmaTokens = {
  // Figma에서 추출한 실제 토큰 값들
  colors: {
    'color/primary/500': '#3b82f6',   // 도서관 Primary — 대출 신청 버튼, 링크
    'color/success/500': '#22c55e',   // 재고 있음 배지
    'color/warning/500': '#f59e0b',   // 연체 경고
    'color/danger/500':  '#ef4444',   // 재고 없음, 삭제
    'color/neutral/500': '#64748b',   // 보조 텍스트
    'color/neutral/0':   '#ffffff',
    'color/neutral/900': '#0f172a',
    // ... 기타 색상 토큰들
  },
  
  spacing: {
    'spacing/xs': '4px',
    'spacing/sm': '8px',
    'spacing/md': '16px',
    // ... 기타 간격 토큰들
  },
  
  typography: {
    'typography/heading/large': {
      fontFamily: 'Inter',
      fontSize: '24px',
      fontWeight: '600',
      lineHeight: '32px',
    },
    // ... 기타 타이포그래피 토큰들
  },
};

// Figma 토큰을 CSS Custom Properties로 변환
export const generateCSSCustomProperties = () => {
  const properties = [];
  
  Object.entries(figmaTokens.colors).forEach(([key, value]) => {
    const cssVar = `--${key.replace(/\//g, '-')}`;
    properties.push(`${cssVar}: ${value};`);
  });
  
  Object.entries(figmaTokens.spacing).forEach(([key, value]) => {
    const cssVar = `--${key.replace(/\//g, '-')}`;
    properties.push(`${cssVar}: ${value};`);
  });
  
  return `:root {\n  ${properties.join('\n  ')}\n}`;
};
```

---

## 8. 성능 최적화 및 접근성 고려사항

### 8.1 이미지 최적화 전략

```jsx
// components/atoms/OptimizedImage/OptimizedImage.jsx
import React, { useState } from 'react';
import PropTypes from 'prop-types';

const OptimizedImage = ({
  src,
  alt,
  width,
  height,
  sizes = '(max-width: 768px) 100vw, (max-width: 1024px) 50vw, 25vw',
  placeholder = 'blur',
  priority = false,
  ...rest
}) => {
  const [isLoaded, setIsLoaded] = useState(false);
  const [hasError, setHasError] = useState(false);

  // WebP 지원 여부에 따른 이미지 포맷 선택
  const getOptimizedSrc = (originalSrc) => {
    if (typeof window !== 'undefined') {
      const canvas = document.createElement('canvas');
      const supportsWebP = canvas.toDataURL('image/webp').indexOf('data:image/webp') === 0;
      
      if (supportsWebP && originalSrc.includes('picsum.photos')) {
        return `${originalSrc}&format=webp`;
      }
    }
    return originalSrc;
  };

  const handleLoad = () => {
    setIsLoaded(true);
  };

  const handleError = () => {
    setHasError(true);
  };

  if (hasError) {
    return (
      <div
        style={{
          width,
          height,
          backgroundColor: '#f3f4f6',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          color: '#6b7280',
          fontSize: '14px',
        }}
        {...rest}
      >
        이미지를 불러올 수 없습니다
      </div>
    );
  }

  return (
    <div style={{ position: 'relative', width, height }}>
      {/* 플레이스홀더 */}
      {!isLoaded && placeholder === 'blur' && (
        <div
          style={{
            position: 'absolute',
            top: 0,
            left: 0,
            right: 0,
            bottom: 0,
            backgroundColor: '#f3f4f6',
            background: 'linear-gradient(90deg, #f3f4f6 25%, #e5e7eb 50%, #f3f4f6 75%)',
            backgroundSize: '200% 100%',
            animation: 'shimmer 2s infinite linear',
          }}
        />
      )}
      
      <img
        src={getOptimizedSrc(src)}
        alt={alt}
        width={width}
        height={height}
        sizes={sizes}
        loading={priority ? 'eager' : 'lazy'}
        onLoad={handleLoad}
        onError={handleError}
        style={{
          width: '100%',
          height: '100%',
          objectFit: 'cover',
          opacity: isLoaded ? 1 : 0,
          transition: 'opacity 0.3s ease-in-out',
        }}
        {...rest}
      />
    </div>
  );
};

OptimizedImage.propTypes = {
  src: PropTypes.string.isRequired,
  alt: PropTypes.string.isRequired,
  width: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
  height: PropTypes.oneOfType([PropTypes.string, PropTypes.number]),
  sizes: PropTypes.string,
  placeholder: PropTypes.oneOf(['blur', 'empty']),
  priority: PropTypes.bool,
};

export default OptimizedImage;
```

이 가이드를 통해 Figma 라이브러리를 기반으로 한 체계적인 React 화면 설계가 가능합니다. 핵심은 Figma의 Design Token 시스템을 React 코드와 1:1로 매핑하여 일관성 있는 디자인 시스템을 구축하는 것입니다.