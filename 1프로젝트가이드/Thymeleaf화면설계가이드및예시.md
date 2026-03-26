# Thymeleaf 화면 설계 및 레이아웃 가이드

## 1. Thymeleaf 화면 설계 철학

### 1.1 자연스러운 템플릿 (Natural Templates)
Thymeleaf의 핵심 철학은 "자연스러운 템플릿"입니다. 이는 템플릿 파일이 브라우저에서 직접 열어도 정상적인 HTML로 표시되어야 한다는 의미입니다.

**설계 원칙:**
- HTML 문법을 최대한 유지하면서 동적 기능 추가
- 디자이너와 개발자 간의 협업 용이성
- 프로토타입과 실제 구현 간의 간격 최소화
- SEO 친화적인 서버사이드 렌더링

### 1.2 컴포넌트 기반 설계 접근법
관리자 페이지에서는 재사용 가능한 컴포넌트 단위로 화면을 설계하는 것이 중요합니다.

**주요 컴포넌트 계층:**
- **레이아웃 계층**: 전체 페이지 구조 (헤더, 사이드바, 푸터)
- **섹션 계층**: 페이지별 주요 영역 (검색폼, 테이블, 폼)
- **프래그먼트 계층**: 재사용 가능한 작은 단위 (버튼, 모달, 알림)

## 2. 레이아웃 설계 구조

### 2.1 계층적 레이아웃 시스템

**1단계: Base Layout (기본 골격)**
```
┌─────────────────────────────────────┐
│              Header                 │
├─────────────────────────────────────┤
│ Sidebar │      Main Content         │
│         │                           │
│         │    ┌─────────────────┐    │
│         │    │   Page Content  │    │
│         │    └─────────────────┘    │
├─────────────────────────────────────┤
│              Footer                 │
└─────────────────────────────────────┘
```

**2단계: Page Layout (페이지별 구조)**
```
Main Content Area:
┌─────────────────────────────────────┐
│          Breadcrumb                 │
├─────────────────────────────────────┤
│    Page Header + Action Buttons     │
├─────────────────────────────────────┤
│         Alert Messages              │
├─────────────────────────────────────┤
│          Search/Filter              │
├─────────────────────────────────────┤
│         Content Area                │
│    (Table/Form/Dashboard)           │
├─────────────────────────────────────┤
│         Pagination                  │
└─────────────────────────────────────┘
```

### 2.2 반응형 레이아웃 전략

**데스크톱 (1200px+)**
- 3컬럼 레이아웃: 사이드바 + 메인 + 우측 정보패널
- 풀 테이블 표시
- 모든 액션 버튼 표시

**태블릿 (768px ~ 1199px)**
- 2컬럼 레이아웃: 축소된 사이드바 + 메인
- 테이블 스크롤 또는 카드 형태로 변환
- 중요한 액션 버튼만 표시

**모바일 (767px 이하)**
- 1컬럼 레이아웃: 햄버거 메뉴 + 메인
- 카드 형태의 리스트 뷰
- 드롭다운 액션 메뉴

## 3. 화면 유형별 설계 가이드

### 3.1 목록 페이지 (List View) 설계

**정보 아키텍처:**
```
1. 페이지 헤더
   - 제목 + 아이콘
   - 주요 액션 버튼 (생성, 내보내기)
   - 요약 통계 정보

2. 검색/필터 영역
   - 접이식 또는 항상 표시
   - 명확한 라벨과 플레이스홀더
   - 초기화 버튼

3. 결과 영역
   - 결과 수 표시
   - 정렬 옵션
   - 페이지 크기 선택

4. 데이터 테이블
   - 정렬 가능한 컬럼 헤더
   - 행별 액션 버튼
   - 체크박스 (대량 작업용)
   - 상태 표시 (배지, 아이콘)

5. 페이지네이션
   - 현재 페이지 강조
   - 이전/다음 버튼
   - 페이지 점프 기능
```

**UX 고려사항:**
- **스켈레톤 로딩**: 데이터 로딩 중 레이아웃 유지
- **무한 스크롤 vs 페이지네이션**: 데이터 양에 따른 선택
- **빈 상태 디자인**: 검색 결과가 없을 때의 안내
- **에러 상태**: 로딩 실패 시 복구 방법 제시

### 3.2 상세 페이지 (Detail View) 설계

**레이아웃 패턴:**
```
1. 헤더 영역
   - 제목 (주요 식별자)
   - 상태 배지
   - 액션 버튼 그룹

2. 메타 정보
   - 생성/수정 일시
   - 작성자 정보
   - 기타 시스템 정보

3. 주요 정보 섹션
   - 카드 형태의 그룹핑
   - 명확한 라벨-값 구조
   - 편집 가능 필드 표시

4. 관련 정보 탭
   - 연관 데이터
   - 활동 로그
   - 첨부 파일
```

**정보 표시 전략:**
- **중요도 기반 배치**: 가장 중요한 정보를 상단에
- **시각적 계층구조**: 제목, 소제목, 본문의 명확한 구분
- **데이터 그룹핑**: 관련 있는 정보끼리 묶어서 표시
- **액션의 접근성**: 자주 사용되는 기능을 쉽게 찾을 수 있도록

### 3.3 폼 페이지 (Form View) 설계

**폼 레이아웃 구조:**
```
1. 폼 헤더
   - 폼 제목과 목적 설명
   - 필수 필드 안내
   - 진행 상황 (다단계 폼의 경우)

2. 폼 섹션
   - 논리적 그룹별로 구분
   - 접이식 섹션 (복잡한 폼)
   - 섹션별 제목과 설명

3. 입력 필드
   - 명확한 라벨
   - 도움말 텍스트
   - 실시간 유효성 검사
   - 에러 메시지

4. 액션 영역
   - 주요 액션 (저장, 제출)
   - 보조 액션 (취소, 임시저장)
   - 위험한 액션 (삭제)
```

**폼 UX 모범 사례:**
- **필드 순서**: 논리적이고 직관적인 흐름
- **입력 도움**: 플레이스홀더, 예시, 툴팁 활용
- **에러 처리**: 인라인 에러와 요약 에러의 조합
- **진행 표시**: 긴 폼에서 현재 위치와 완료도 표시

### 3.4 대시보드 설계

**대시보드 구성 요소:**
```
1. 요약 카드 (KPI Cards)
   - 핵심 지표의 시각적 표현
   - 전기간 대비 변화량
   - 드릴다운 링크

2. 차트 영역
   - 시계열 차트
   - 비교 차트
   - 분포 차트

3. 테이블 위젯
   - 최신 활동
   - 상위/하위 항목
   - 알림 목록

4. 액션 위젯
   - 빠른 액션 버튼
   - 바로가기 링크
   - 북마크된 페이지
```

**대시보드 설계 원칙:**
- **5초 룰**: 핵심 정보를 5초 내에 파악 가능해야 함
- **계층적 정보**: 요약 → 상세 → 액션의 흐름
- **일관된 색상**: 상태와 카테고리별 색상 체계
- **적응형 레이아웃**: 화면 크기에 따른 자동 조정

## 4. 공통 UI 컴포넌트 설계

### 4.1 네비게이션 설계

**메인 네비게이션 구조:**
```
1. 글로벌 헤더
   - 로고/브랜드
   - 사용자 메뉴
   - 알림 센터
   - 검색 박스

2. 사이드 네비게이션
   - 계층적 메뉴 구조
   - 현재 위치 표시
   - 접기/펼치기 기능
   - 즐겨찾기 기능

3. 브레드크럼
   - 현재 위치 경로
   - 클릭 가능한 단계
   - 홈으로 돌아가기
```

**네비게이션 UX 가이드라인:**
- **명확한 현재 위치**: 사용자가 어디에 있는지 항상 알 수 있어야 함
- **일관된 구조**: 모든 페이지에서 동일한 네비게이션 패턴
- **접근성**: 키보드 네비게이션과 스크린 리더 지원
- **성능**: 메뉴 로딩 지연 최소화

### 4.2 데이터 테이블 설계

**테이블 구성 요소:**
```
1. 테이블 헤더
   - 정렬 가능 표시 (화살표)
   - 필터 아이콘
   - 컬럼 크기 조정
   - 체크박스 (전체 선택)

2. 테이블 바디
   - 데이터 행
   - 상태 표시 (색상, 아이콘)
   - 인라인 편집 (선택적)
   - 액션 버튼

3. 테이블 푸터
   - 페이지네이션
   - 결과 수 표시
   - 페이지 크기 선택
```

**테이블 반응형 전략:**
- **우선순위 기반 표시**: 중요한 컬럼만 모바일에서 표시
- **카드 변환**: 작은 화면에서 테이블을 카드 형태로 변환
- **스크롤 처리**: 수평 스크롤과 고정 컬럼 활용
- **액션 축약**: 드롭다운 메뉴로 액션 버튼 정리

### 4.3 모달 및 팝업 설계

**모달 사용 시나리오:**
```
1. 확인 모달
   - 삭제, 변경 등 중요한 액션 확인
   - 명확한 제목과 설명
   - 취소/확인 버튼

2. 정보 모달
   - 상세 정보 표시
   - 빠른 미리보기
   - 외부 링크 방지

3. 폼 모달
   - 간단한 생성/편집 폼
   - 복잡하지 않은 입력
   - 실시간 검증

4. 멀티스텝 모달
   - 위자드 형태의 프로세스
   - 진행 상황 표시
   - 이전/다음 네비게이션
```

**모달 UX 모범 사례:**
- **명확한 목적**: 모달을 여는 이유가 명확해야 함
- **쉬운 닫기**: ESC 키, 배경 클릭, X 버튼 제공
- **적절한 크기**: 내용에 맞는 크기 조정
- **접근성**: 포커스 관리와 키보드 네비게이션

## 5. 상태 및 피드백 디자인

### 5.1 로딩 상태 디자인

**로딩 표시 전략:**
```
1. 글로벌 로딩
   - 전체 페이지 로딩
   - 스피너 + 로딩 메시지
   - 진행률 표시 (가능한 경우)

2. 섹션 로딩
   - 특정 영역만 로딩
   - 스켈레톤 UI
   - 컨텐츠 영역 블러 처리

3. 인라인 로딩
   - 버튼 내 스피너
   - 테이블 행 업데이트
   - 실시간 검색 결과
```

**로딩 UX 원칙:**
- **즉시 피드백**: 액션 후 즉시 로딩 상태 표시
- **예상 시간**: 가능하면 완료 예상 시간 제공
- **취소 옵션**: 긴 작업의 경우 취소 버튼 제공
- **컨텍스트 유지**: 로딩 중에도 페이지 구조 유지

### 5.2 에러 상태 디자인

**에러 표시 레벨:**
```
1. 페이지 레벨 에러
   - 404, 500 등 시스템 에러
   - 전체 페이지 대체
   - 복구 방법 안내

2. 섹션 레벨 에러
   - 데이터 로딩 실패
   - 재시도 버튼
   - 대안 제시

3. 필드 레벨 에러
   - 폼 입력 검증 에러
   - 인라인 에러 메시지
   - 수정 방법 안내
```

### 5.3 성공 및 알림 메시지

**알림 메시지 유형:**
```
1. 성공 메시지
   - 작업 완료 확인
   - 초록색 계열
   - 자동 사라짐

2. 경고 메시지
   - 주의 필요한 상황
   - 노란색 계열
   - 사용자 액션 유도

3. 정보 메시지
   - 일반적인 안내
   - 파란색 계열
   - 중요도에 따라 지속 시간 조정

4. 에러 메시지
   - 오류 발생 알림
   - 빨간색 계열
   - 수동 닫기
```

## 6. 접근성 및 사용성 가이드

### 6.1 웹 접근성 (WCAG) 준수

**핵심 접근성 원칙:**
```
1. 인식 가능성 (Perceivable)
   - 충분한 색상 대비 (4.5:1 이상)
   - 대체 텍스트 제공
   - 확대 가능한 폰트 크기

2. 운용 가능성 (Operable)
   - 키보드로만 조작 가능
   - 충분한 클릭 영역 (44px 이상)
   - 시간 제한 없음 또는 연장 가능

3. 이해 가능성 (Understandable)
   - 명확한 언어 사용
   - 일관된 네비게이션
   - 에러 메시지의 명확성

4. 견고성 (Robust)
   - 표준 HTML 마크업
   - 스크린 리더 호환성
   - 다양한 브라우저 지원
```

### 6.2 국제화 (i18n) 고려사항

**다국어 대응 설계:**
```
1. 텍스트 확장성
   - 20-30% 텍스트 길이 증가 고려
   - 유연한 레이아웃 설계
   - 폰트 크기 조정 가능

2. 문화적 고려사항
   - 색상의 문화적 의미
   - 읽기 방향 (좌→우, 우→좌)
   - 날짜/시간 형식

3. 기술적 구현
   - 메시지 키 기반 텍스트 관리
   - 동적 로케일 변경
   - 폰트 로딩 최적화
```

## 7. 성능 최적화 전략

### 7.1 렌더링 성능

**서버사이드 렌더링 최적화:**
```
1. 템플릿 캐싱
   - 컴파일된 템플릿 재사용
   - 조건부 캐싱 전략
   - 캐시 무효화 정책

2. 데이터 최적화
   - 필요한 데이터만 로드
   - 지연 로딩 전략
   - 페이지네이션 활용

3. HTML 최적화
   - 불필요한 마크업 제거
   - 인라인 스타일 최소화
   - 압축 및 최소화
```

### 7.2 클라이언트 성능

**프론트엔드 최적화:**
```
1. 리소스 로딩
   - CSS/JS 번들링
   - 이미지 최적화
   - 폰트 최적화

2. 인터랙션 성능
   - 디바운싱/스로틀링
   - 가상 스크롤링
   - 지연 로딩

3. 네트워크 최적화
   - AJAX 요청 최소화
   - 데이터 압축
   - CDN 활용
```

## 8. 테스팅 전략

### 8.1 UI/UX 테스팅

**사용성 테스팅 체크리스트:**
```
1. 네비게이션 테스트
   - 메뉴 찾기 용이성
   - 현재 위치 파악
   - 되돌아가기 기능

2. 폼 사용성 테스트
   - 필드 라벨 명확성
   - 에러 메시지 이해도
   - 자동완성 기능

3. 반응형 테스트
   - 다양한 화면 크기
   - 터치 인터페이스
   - 성능 영향도

4. 접근성 테스트
   - 키보드 네비게이션
   - 스크린 리더 호환
   - 색상 대비 검사
```

### 8.2 크로스 브라우저 테스팅

**테스트 매트릭스:**
```
데스크톱:
- Chrome (최신, 이전 버전)
- Firefox (최신, 이전 버전)
- Safari (최신)
- Edge (최신)

모바일:
- iOS Safari
- Android Chrome
- Samsung Internet

특별 고려사항:
- Internet Explorer 11 (필요시)
- 특정 기업 환경 브라우저
- 접근성 도구
```

## 9. 유지보수 및 확장성

### 9.1 컴포넌트 문서화

**문서화 체계:**
```
1. 컴포넌트 가이드
   - 사용 목적과 시나리오
   - 속성 및 옵션 설명
   - 사용 예시

2. 스타일 가이드
   - 색상 팔레트
   - 타이포그래피
   - 간격 시스템
   - 아이콘 사용법

3. 패턴 라이브러리
   - UI 패턴 모음
   - 인터랙션 가이드
   - 애니메이션 가이드
```

### 9.2 확장성 고려사항

**미래 대응 설계:**
```
1. 모듈러 구조
   - 독립적인 컴포넌트 설계
   - 의존성 최소화
   - 재사용 가능한 패턴

2. 테마 시스템
   - CSS 변수 활용
   - 다크 모드 지원
   - 브랜드 커스터마이징

3. 설정 가능한 UI
   - 사용자 맞춤 레이아웃
   - 기능 토글
   - 개인화 옵션
```

# Thymeleaf 관리자 페이지 개발 가이드 — 온라인 도서관 시스템

> **관리자 도메인:** 도서(Book) · 대출(Loan) · 회원(Member) 3개 영역
> **접근 경로:** `/admin/**` — Spring Security Stateful 세션 (JWT 불필요)
> **화면 구성:** Bootstrap 5 + Thymeleaf Layout Dialect

## 1. 프로젝트 구조

```
src/
├── main/
│   ├── java/
│   │   └── com/example/library/
│   │       ├── config/
│   │       │   ├── SecurityConfig.java        # Dual FilterChain 설정
│   │       │   └── AdminSecurityConfig.java   # /admin/** 세션 인증
│   │       ├── entity/
│   │       │   ├── Book.java
│   │       │   ├── Loan.java
│   │       │   ├── Member.java
│   │       │   └── Category.java
│   │       ├── repository/
│   │       │   ├── BookRepository.java
│   │       │   ├── LoanRepository.java
│   │       │   └── MemberRepository.java
│   │       ├── service/
│   │       │   ├── AdminBookService.java
│   │       │   ├── AdminLoanService.java
│   │       │   └── AdminMemberService.java
│   │       ├── controller/admin/
│   │       │   ├── AdminDashboardController.java  # /admin
│   │       │   ├── AdminBookController.java       # /admin/books
│   │       │   ├── AdminLoanController.java       # /admin/loans
│   │       │   └── AdminMemberController.java     # /admin/members
│   │       └── dto/
│   │           ├── BookListDto.java
│   │           ├── BookFormDto.java
│   │           ├── LoanListDto.java
│   │           └── MemberListDto.java
│   └── resources/
│       ├── templates/
│       │   ├── layout/
│       │   │   └── base.html          # 공통 레이아웃 (사이드바: 도서·대출·회원)
│       │   ├── admin/
│       │   │   ├── dashboard.html     # 대시보드 (통계 카드 4개)
│       │   │   ├── book/
│       │   │   │   ├── list.html      # 도서 목록 + 검색
│       │   │   │   ├── form.html      # 도서 등록/수정
│       │   │   │   └── detail.html    # 도서 상세
│       │   │   ├── loan/
│       │   │   │   └── list.html      # 대출 현황 (탭: 전체/대출중/연체/반납완료)
│       │   │   └── member/
│       │   │       └── list.html      # 회원 목록
│       │   └── auth/
│       │       └── login.html         # 관리자 로그인
│       ├── static/
│       │   ├── css/admin.css
│       │   └── js/admin.js
│       └── application.yml
```

## 2. Entity 설계

### 2.1 도서(Book) Entity

```java
@Entity
@Table(name = "books")
@Getter @Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Book extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "book_id")
    private Long id;

    @Column(nullable = false, unique = true, length = 13)
    private String isbn;                   // 13자리 ISBN

    @Column(nullable = false, length = 200)
    private String title;                  // 제목

    @Column(nullable = false, length = 100)
    private String author;                 // 저자

    @Column(length = 100)
    private String publisher;              // 출판사

    @Column(name = "publication_date")
    private LocalDate publicationDate;     // 출판일

    @Column(name = "total_copies")
    private Integer totalCopies = 1;       // 총 보유 수량

    @Column(name = "available_copies")
    private Integer availableCopies = 1;   // 대출 가능 수량

    @Column(columnDefinition = "TEXT")
    private String description;            // 도서 설명

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "category_id")
    private Category category;             // 카테고리 (소설/IT/역사/기타)

    @OneToMany(mappedBy = "book", fetch = FetchType.LAZY)
    private List<Loan> loans = new ArrayList<>();

    // 비즈니스 메서드
    public boolean isAvailable() {
        return availableCopies > 0;
    }

    public void decreaseAvailable() {
        if (availableCopies <= 0) throw new IllegalStateException("재고가 없습니다");
        availableCopies--;
    }

    public void increaseAvailable() {
        availableCopies++;
    }
}
```

### 2.2 대출(Loan) Entity

```java
@Entity
@Table(name = "loans")
@Getter @Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Loan extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "loan_id")
    private Long id;

    @Column(name = "loan_number", unique = true, nullable = false, length = 12)
    private String loanNumber;              // 대출번호 (L202505230001)

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "member_id", nullable = false)
    private Member member;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "book_id", nullable = false)
    private Book book;

    @Column(name = "loan_date", nullable = false)
    private LocalDate loanDate;             // 대출일

    @Column(name = "due_date", nullable = false)
    private LocalDate dueDate;             // 반납 예정일 (대출일 + 14일)

    @Column(name = "return_date")
    private LocalDate returnDate;           // 실제 반납일

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private LoanStatus status = LoanStatus.REQUESTED;

    @Column(name = "overdue_fee")
    private BigDecimal overdueFee = BigDecimal.ZERO;  // 연체료

    // 비즈니스 메서드
    public boolean isOverdue() {
        return status == LoanStatus.BORROWED
            && LocalDate.now().isAfter(dueDate);
    }

    public long getOverdueDays() {
        if (!isOverdue()) return 0;
        return ChronoUnit.DAYS.between(dueDate, LocalDate.now());
    }

    public BigDecimal calculateOverdueFee() {
        return BigDecimal.valueOf(getOverdueDays() * 100L);  // 1일당 100원
    }
}
```

### 2.3 공통 BaseEntity

```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
@Getter @Setter
public abstract class BaseEntity {

    @CreatedDate
    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
}
```

## 3. Repository 설계

```java
// ── 도서 Repository ─────────────────────────────────────────────────────────
@Repository
public interface BookRepository extends JpaRepository<Book, Long> {

    // 제목·저자·ISBN 복합 검색 + 카테고리 필터
    @Query("SELECT b FROM Book b LEFT JOIN b.category c WHERE " +
           "(:keyword IS NULL OR b.title LIKE %:keyword% OR b.author LIKE %:keyword% OR b.isbn LIKE %:keyword%) AND " +
           "(:categoryId IS NULL OR c.id = :categoryId)")
    Page<Book> findBySearchCriteria(
        @Param("keyword")    String keyword,
        @Param("categoryId") Long   categoryId,
        Pageable pageable
    );

    // 대출 가능 도서만 조회
    Page<Book> findByAvailableCopiesGreaterThan(int copies, Pageable pageable);

    // 통계: 전체 도서 수
    long count();

    // 통계: 대출 가능 도서 수
    long countByAvailableCopiesGreaterThan(int copies);
}

// ── 대출 Repository ─────────────────────────────────────────────────────────
@Repository
public interface LoanRepository extends JpaRepository<Loan, Long> {

    // 상태별 조회 (페이징)
    Page<Loan> findByStatus(LoanStatus status, Pageable pageable);

    // 연체 도서 조회 (대출중인데 반납예정일 초과)
    @Query("SELECT l FROM Loan l WHERE l.status = 'BORROWED' AND l.dueDate < CURRENT_DATE")
    List<Loan> findOverdueLoans();

    // 회원별 대출 이력
    Page<Loan> findByMemberId(Long memberId, Pageable pageable);

    // 도서별 현재 대출 중 수량
    long countByBookIdAndStatus(Long bookId, LoanStatus status);

    // 통계: 오늘 신청된 대출
    @Query("SELECT COUNT(l) FROM Loan l WHERE l.createdAt >= :startOfDay")
    long countTodayRequested(@Param("startOfDay") LocalDateTime startOfDay);

    // 복합 조건 검색 (관리자 대출 현황 페이지)
    @Query("SELECT l FROM Loan l JOIN l.member m JOIN l.book b WHERE " +
           "(:status IS NULL OR l.status = :status) AND " +
           "(:memberId IS NULL OR m.id = :memberId) AND " +
           "(:onlyOverdue = false OR (l.status = 'BORROWED' AND l.dueDate < CURRENT_DATE))")
    Page<Loan> findByAdminCriteria(
        @Param("status")      LoanStatus status,
        @Param("memberId")    Long memberId,
        @Param("onlyOverdue") boolean onlyOverdue,
        Pageable pageable
    );
}
```

## 4. DTO 설계

### 4.1 도서(Book) 관련 DTO

```java
// ── 목록 조회용 DTO ──────────────────────────────────────────────────────────
@Getter @Builder
@NoArgsConstructor @AllArgsConstructor
public class BookListDto {
    private Long   id;
    private String isbn;
    private String title;
    private String author;
    private String publisher;
    private String categoryName;
    private int    totalCopies;
    private int    availableCopies;
    private LocalDate publicationDate;
    private LocalDateTime createdAt;

    public static BookListDto from(Book b) {
        return BookListDto.builder()
            .id(b.getId())
            .isbn(b.getIsbn())
            .title(b.getTitle())
            .author(b.getAuthor())
            .publisher(b.getPublisher())
            .categoryName(b.getCategory() != null ? b.getCategory().getCategoryName() : "-")
            .totalCopies(b.getTotalCopies())
            .availableCopies(b.getAvailableCopies())
            .publicationDate(b.getPublicationDate())
            .createdAt(b.getCreatedAt())
            .build();
    }
}

// ── 등록/수정 폼 DTO ─────────────────────────────────────────────────────────
@Getter @Setter
@NoArgsConstructor @AllArgsConstructor
public class BookFormDto {

    @NotBlank(message = "ISBN은 필수입니다")
    @Pattern(regexp = "^\\d{13}$", message = "ISBN은 13자리 숫자입니다")
    private String isbn;

    @NotBlank(message = "제목은 필수입니다")
    @Size(max = 200, message = "제목은 200자 이내")
    private String title;

    @NotBlank(message = "저자는 필수입니다")
    private String author;

    private String publisher;

    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private LocalDate publicationDate;

    @NotNull(message = "총 수량은 필수입니다")
    @Min(value = 1, message = "최소 1권 이상")
    private Integer totalCopies;

    private Long categoryId;

    @Size(max = 1000)
    private String description;

    public Book toEntity(Category category) {
        return Book.builder()
            .isbn(isbn).title(title).author(author)
            .publisher(publisher).publicationDate(publicationDate)
            .totalCopies(totalCopies).availableCopies(totalCopies)
            .category(category).description(description)
            .build();
    }
}

// ── 검색용 DTO ───────────────────────────────────────────────────────────────
@Getter @Setter
@NoArgsConstructor @AllArgsConstructor
public class BookSearchDto {
    private String keyword;       // 제목·저자·ISBN 통합 검색
    private Long   categoryId;
    private String sortBy  = "createdAt";
    private String sortDir = "desc";
    private int    page    = 0;
    private int    size    = 10;

    public Pageable getPageable() {
        Sort sort = Sort.by(Sort.Direction.fromString(sortDir), sortBy);
        return PageRequest.of(page, size, sort);
    }
}
```

### 4.2 대출(Loan) 관련 DTO

```java
// ── 대출 목록용 DTO ──────────────────────────────────────────────────────────
@Getter @Builder
@NoArgsConstructor @AllArgsConstructor
public class LoanListDto {
    private Long       id;
    private String     loanNumber;
    private String     memberName;
    private String     memberNumber;
    private String     bookTitle;
    private String     bookIsbn;
    private LocalDate  loanDate;
    private LocalDate  dueDate;
    private LocalDate  returnDate;
    private LoanStatus status;
    private boolean    overdue;
    private long       overdueDays;
    private BigDecimal overdueFee;

    public static LoanListDto from(Loan l) {
        return LoanListDto.builder()
            .id(l.getId())
            .loanNumber(l.getLoanNumber())
            .memberName(l.getMember().getName())
            .memberNumber(l.getMember().getMemberNumber())
            .bookTitle(l.getBook().getTitle())
            .bookIsbn(l.getBook().getIsbn())
            .loanDate(l.getLoanDate())
            .dueDate(l.getDueDate())
            .returnDate(l.getReturnDate())
            .status(l.getStatus())
            .overdue(l.isOverdue())
            .overdueDays(l.getOverdueDays())
            .overdueFee(l.calculateOverdueFee())
            .build();
    }
}

// ── 대출 검색용 DTO ──────────────────────────────────────────────────────────
@Getter @Setter
@NoArgsConstructor @AllArgsConstructor
public class LoanSearchDto {
    private LoanStatus status;         // 상태 필터 (null이면 전체)
    private Long       memberId;
    private boolean    onlyOverdue = false;
    private String     sortBy      = "loanDate";
    private String     sortDir     = "desc";
    private int        page        = 0;
    private int        size        = 10;

    public Pageable getPageable() {
        Sort sort = Sort.by(Sort.Direction.fromString(sortDir), sortBy);
        return PageRequest.of(page, size, sort);
    }
}
```

## 5. Service 설계

```java
// ── AdminBookService ─────────────────────────────────────────────────────────
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class AdminBookService {

    private final BookRepository     bookRepository;
    private final CategoryRepository categoryRepository;

    // 도서 목록 (검색 + 페이징)
    public Page<BookListDto> getBooks(BookSearchDto searchDto) {
        return bookRepository
            .findBySearchCriteria(searchDto.getKeyword(), searchDto.getCategoryId(),
                                  searchDto.getPageable())
            .map(BookListDto::from);
    }

    // 도서 상세
    public Book getBook(Long id) {
        return bookRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("도서를 찾을 수 없습니다"));
    }

    // 도서 등록
    @Transactional
    public Long createBook(BookFormDto form) {
        if (bookRepository.findByIsbn(form.getIsbn()).isPresent())
            throw new IllegalArgumentException("이미 등록된 ISBN입니다: " + form.getIsbn());

        Category category = null;
        if (form.getCategoryId() != null)
            category = categoryRepository.findById(form.getCategoryId()).orElse(null);

        Book book = form.toEntity(category);
        return bookRepository.save(book).getId();
    }

    // 도서 수정
    @Transactional
    public void updateBook(Long id, BookFormDto form) {
        Book book = getBook(id);
        int stockDiff = form.getTotalCopies() - book.getTotalCopies();

        book.setTitle(form.getTitle());
        book.setAuthor(form.getAuthor());
        book.setPublisher(form.getPublisher());
        book.setPublicationDate(form.getPublicationDate());
        book.setTotalCopies(form.getTotalCopies());
        book.setAvailableCopies(book.getAvailableCopies() + stockDiff); // 대출 수 유지
        book.setDescription(form.getDescription());

        if (form.getCategoryId() != null)
            book.setCategory(categoryRepository.findById(form.getCategoryId()).orElse(null));
    }

    // 도서 삭제 (현재 대출 중이면 불가)
    @Transactional
    public void deleteBook(Long id) {
        Book book = getBook(id);
        if (book.getAvailableCopies() < book.getTotalCopies())
            throw new IllegalStateException("대출 중인 도서가 있어 삭제할 수 없습니다");
        bookRepository.delete(book);
    }

    // 대시보드용 통계
    public Map<String, Long> getStats() {
        Map<String, Long> stats = new HashMap<>();
        stats.put("totalBooks",     bookRepository.count());
        stats.put("availableBooks", bookRepository.countByAvailableCopiesGreaterThan(0));
        return stats;
    }
}

// ── AdminLoanService ─────────────────────────────────────────────────────────
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class AdminLoanService {

    private final LoanRepository loanRepository;

    // 대출 목록 (상태 필터 + 페이징)
    public Page<LoanListDto> getLoans(LoanSearchDto searchDto) {
        return loanRepository
            .findByAdminCriteria(searchDto.getStatus(), searchDto.getMemberId(),
                                 searchDto.isOnlyOverdue(), searchDto.getPageable())
            .map(LoanListDto::from);
    }

    // 연체 목록
    public List<LoanListDto> getOverdueLoans() {
        return loanRepository.findOverdueLoans().stream()
            .map(LoanListDto::from).toList();
    }

    // 대출 승인 (REQUESTED → BORROWED)
    @Transactional
    public void approveLoan(Long loanId) {
        Loan loan = loanRepository.findById(loanId)
            .orElseThrow(() -> new EntityNotFoundException("대출 정보를 찾을 수 없습니다"));

        if (loan.getStatus() != LoanStatus.REQUESTED)
            throw new IllegalStateException("승인 대기 상태가 아닙니다");

        loan.setStatus(LoanStatus.BORROWED);
        loan.getBook().decreaseAvailable();
    }

    // 반납 처리 (BORROWED/OVERDUE → RETURNED)
    @Transactional
    public void returnLoan(Long loanId) {
        Loan loan = loanRepository.findById(loanId)
            .orElseThrow(() -> new EntityNotFoundException("대출 정보를 찾을 수 없습니다"));

        if (loan.getStatus() != LoanStatus.BORROWED && loan.getStatus() != LoanStatus.OVERDUE)
            throw new IllegalStateException("반납할 수 없는 상태입니다");

        loan.setStatus(LoanStatus.RETURNED);
        loan.setReturnDate(LocalDate.now());
        loan.setOverdueFee(loan.calculateOverdueFee());
        loan.getBook().increaseAvailable();
    }

    // 대시보드용 통계
    public Map<String, Long> getStats() {
        Map<String, Long> stats = new HashMap<>();
        stats.put("totalBorrowed",  loanRepository.countByStatus(LoanStatus.BORROWED));
        stats.put("overdueLoans",   (long) loanRepository.findOverdueLoans().size());
        stats.put("todayRequested", loanRepository.countTodayRequested(
            LocalDate.now().atStartOfDay()));
        return stats;
    }
}
```

## 6. Controller 설계

```java
// ── AdminBookController ──────────────────────────────────────────────────────
@Controller
@RequestMapping("/admin/books")
@RequiredArgsConstructor
public class AdminBookController {

    private final AdminBookService   bookService;
    private final CategoryRepository categoryRepository;

    // 도서 목록 페이지
    @GetMapping
    public String list(@ModelAttribute BookSearchDto searchDto, Model model) {
        model.addAttribute("books",      bookService.getBooks(searchDto));
        model.addAttribute("searchDto",  searchDto);
        model.addAttribute("categories", categoryRepository.findAll());
        return "admin/book/list";
    }

    // 도서 상세 페이지
    @GetMapping("/{id}")
    public String detail(@PathVariable Long id, Model model) {
        model.addAttribute("book", bookService.getBook(id));
        return "admin/book/detail";
    }

    // 도서 등록 폼
    @GetMapping("/new")
    public String createForm(Model model) {
        model.addAttribute("bookForm",   new BookFormDto());
        model.addAttribute("categories", categoryRepository.findAll());
        return "admin/book/form";
    }

    // 도서 등록 처리
    @PostMapping("/new")
    public String create(@Valid @ModelAttribute("bookForm") BookFormDto form,
                         BindingResult errors,
                         Model model,
                         RedirectAttributes ra) {
        if (errors.hasErrors()) {
            model.addAttribute("categories", categoryRepository.findAll());
            return "admin/book/form";
        }
        try {
            Long id = bookService.createBook(form);
            ra.addFlashAttribute("message", "도서가 등록되었습니다.");
            return "redirect:/admin/books/" + id;
        } catch (IllegalArgumentException e) {
            errors.reject("duplicate", e.getMessage());
            model.addAttribute("categories", categoryRepository.findAll());
            return "admin/book/form";
        }
    }

    // 도서 수정 폼
    @GetMapping("/{id}/edit")
    public String editForm(@PathVariable Long id, Model model) {
        Book book = bookService.getBook(id);
        BookFormDto form = new BookFormDto();
        form.setIsbn(book.getIsbn());
        form.setTitle(book.getTitle());
        form.setAuthor(book.getAuthor());
        form.setPublisher(book.getPublisher());
        form.setPublicationDate(book.getPublicationDate());
        form.setTotalCopies(book.getTotalCopies());
        form.setDescription(book.getDescription());
        if (book.getCategory() != null) form.setCategoryId(book.getCategory().getId());

        model.addAttribute("bookForm",   form);
        model.addAttribute("bookId",     id);
        model.addAttribute("categories", categoryRepository.findAll());
        return "admin/book/form";
    }

    // 도서 수정 처리
    @PostMapping("/{id}/edit")
    public String update(@PathVariable Long id,
                         @Valid @ModelAttribute("bookForm") BookFormDto form,
                         BindingResult errors,
                         Model model,
                         RedirectAttributes ra) {
        if (errors.hasErrors()) {
            model.addAttribute("bookId",     id);
            model.addAttribute("categories", categoryRepository.findAll());
            return "admin/book/form";
        }
        bookService.updateBook(id, form);
        ra.addFlashAttribute("message", "도서 정보가 수정되었습니다.");
        return "redirect:/admin/books/" + id;
    }

    // 도서 삭제 (AJAX)
    @DeleteMapping("/{id}")
    @ResponseBody
    public ResponseEntity<?> delete(@PathVariable Long id) {
        try {
            bookService.deleteBook(id);
            return ResponseEntity.ok(Map.of("message", "도서가 삭제되었습니다."));
        } catch (IllegalStateException e) {
            return ResponseEntity.badRequest().body(Map.of("error", e.getMessage()));
        }
    }
}

// ── AdminLoanController ──────────────────────────────────────────────────────
@Controller
@RequestMapping("/admin/loans")
@RequiredArgsConstructor
public class AdminLoanController {

    private final AdminLoanService loanService;

    // 대출 현황 페이지
    @GetMapping
    public String list(@ModelAttribute LoanSearchDto searchDto, Model model) {
        model.addAttribute("loans",     loanService.getLoans(searchDto));
        model.addAttribute("searchDto", searchDto);
        model.addAttribute("statuses",  LoanStatus.values());
        model.addAttribute("overdueCount", loanService.getOverdueLoans().size());
        return "admin/loan/list";
    }

    // 대출 승인 (AJAX)
    @PostMapping("/{id}/approve")
    @ResponseBody
    public ResponseEntity<?> approve(@PathVariable Long id) {
        try {
            loanService.approveLoan(id);
            return ResponseEntity.ok(Map.of("message", "대출이 승인되었습니다."));
        } catch (IllegalStateException e) {
            return ResponseEntity.badRequest().body(Map.of("error", e.getMessage()));
        }
    }

    // 반납 처리 (AJAX)
    @PostMapping("/{id}/return")
    @ResponseBody
    public ResponseEntity<?> returnBook(@PathVariable Long id) {
        try {
            loanService.returnLoan(id);
            return ResponseEntity.ok(Map.of("message", "반납이 처리되었습니다."));
        } catch (IllegalStateException e) {
            return ResponseEntity.badRequest().body(Map.of("error", e.getMessage()));
        }
    }
}
```

## 7. Thymeleaf 템플릿 설계

### 7.1 공통 레이아웃 (layout/base.html)

```html
<!DOCTYPE html>
<html lang="ko"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title th:text="${title != null ? title + ' — 도서관 관리자' : '도서관 관리자'}">도서관 관리자</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <link th:href="@{/css/admin.css}" rel="stylesheet">
    <th:block layout:fragment="css"></th:block>
</head>
<body class="bg-light">

<!-- ── 상단 네비게이션 바 ─────────────────────────────────────────────────── -->
<nav class="navbar navbar-expand-lg navbar-dark" style="background-color:#1e3a5f;">
    <div class="container-fluid">
        <a class="navbar-brand fw-bold" th:href="@{/admin}">
            <i class="fas fa-book-open me-2"></i>도서관 관리자
        </a>
        <div class="navbar-nav ms-auto">
            <span class="navbar-text text-white-50 me-3" sec:authentication="name">관리자</span>
            <a class="btn btn-sm btn-outline-light" th:href="@{/admin/logout}">
                <i class="fas fa-sign-out-alt"></i> 로그아웃
            </a>
        </div>
    </div>
</nav>

<div class="container-fluid">
    <div class="row">

        <!-- ── 사이드바 ────────────────────────────────────────────────────── -->
        <nav class="col-md-2 d-md-block bg-white sidebar shadow-sm" style="min-height:calc(100vh - 56px);">
            <div class="position-sticky pt-3">
                <ul class="nav flex-column">
                    <li class="nav-item">
                        <a class="nav-link"
                           th:href="@{/admin}"
                           th:classappend="${#request.requestURI == '/admin'} ? 'active fw-bold text-primary' : 'text-secondary'">
                            <i class="fas fa-tachometer-alt me-2"></i>대시보드
                        </a>
                    </li>

                    <li class="nav-item mt-2">
                        <small class="text-uppercase text-muted px-3 fw-bold" style="font-size:.7rem;">도서 관리</small>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link"
                           th:href="@{/admin/books}"
                           th:classappend="${#strings.startsWith(#request.requestURI,'/admin/books')} ? 'active fw-bold text-primary' : 'text-secondary'">
                            <i class="fas fa-book me-2"></i>도서 목록
                        </a>
                    </li>

                    <li class="nav-item mt-2">
                        <small class="text-uppercase text-muted px-3 fw-bold" style="font-size:.7rem;">대출 관리</small>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link"
                           th:href="@{/admin/loans}"
                           th:classappend="${#strings.startsWith(#request.requestURI,'/admin/loans')} ? 'active fw-bold text-primary' : 'text-secondary'">
                            <i class="fas fa-hand-holding-heart me-2"></i>대출 현황
                            <!-- 연체 건수 배지 -->
                            <span th:if="${overdueCount != null and overdueCount > 0}"
                                  class="badge bg-danger ms-1" th:text="${overdueCount}"></span>
                        </a>
                    </li>

                    <li class="nav-item mt-2">
                        <small class="text-uppercase text-muted px-3 fw-bold" style="font-size:.7rem;">회원 관리</small>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link"
                           th:href="@{/admin/members}"
                           th:classappend="${#strings.startsWith(#request.requestURI,'/admin/members')} ? 'active fw-bold text-primary' : 'text-secondary'">
                            <i class="fas fa-users me-2"></i>회원 목록
                        </a>
                    </li>
                </ul>
            </div>
        </nav>

        <!-- ── 메인 콘텐츠 영역 ──────────────────────────────────────────── -->
        <main class="col-md-10 ms-sm-auto px-4 py-4">

            <!-- 플래시 메시지 -->
            <div th:if="${message}" class="alert alert-success alert-dismissible fade show" role="alert">
                <i class="fas fa-check-circle me-1"></i><span th:text="${message}"></span>
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
            <div th:if="${error}" class="alert alert-danger alert-dismissible fade show" role="alert">
                <i class="fas fa-exclamation-circle me-1"></i><span th:text="${error}"></span>
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>

            <!-- 페이지별 콘텐츠 삽입 지점 -->
            <div layout:fragment="content"></div>

        </main>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script th:src="@{/js/admin.js}"></script>
<th:block layout:fragment="script"></th:block>
</body>
</html>
```

### 7.2 도서 목록 페이지 (admin/book/list.html)

```html
<!DOCTYPE html>
<html lang="ko"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/base}">

<th:block layout:fragment="css">
<style>
    .search-panel { background:#f8f9fa; border-radius:.5rem; padding:1.25rem; margin-bottom:1.5rem; }
    .stock-badge  { font-size:.8em; }
    .table-actions { white-space:nowrap; }
</style>
</th:block>

<div layout:fragment="content">

    <!-- 페이지 헤더 -->
    <div class="d-flex justify-content-between align-items-center mb-4">
        <h4 class="mb-0"><i class="fas fa-book me-2"></i>도서 목록</h4>
        <a th:href="@{/admin/books/new}" class="btn btn-primary btn-sm">
            <i class="fas fa-plus me-1"></i>도서 등록
        </a>
    </div>

    <!-- 검색 폼 -->
    <form th:action="@{/admin/books}" method="get" th:object="${searchDto}" class="search-panel">
        <div class="row g-3 align-items-end">
            <div class="col-md-4">
                <label class="form-label form-label-sm">검색 (제목·저자·ISBN)</label>
                <input type="text" class="form-control form-control-sm"
                       th:field="*{keyword}" placeholder="검색어를 입력하세요">
            </div>
            <div class="col-md-3">
                <label class="form-label form-label-sm">카테고리</label>
                <select class="form-select form-select-sm" th:field="*{categoryId}">
                    <option value="">전체</option>
                    <option th:each="cat : ${categories}"
                            th:value="${cat.id}"
                            th:text="${cat.categoryName}"></option>
                </select>
            </div>
            <div class="col-md-auto">
                <button type="submit" class="btn btn-outline-primary btn-sm">
                    <i class="fas fa-search"></i> 검색
                </button>
                <a th:href="@{/admin/books}" class="btn btn-outline-secondary btn-sm ms-1">
                    <i class="fas fa-redo"></i> 초기화
                </a>
            </div>
        </div>
        <input type="hidden" th:field="*{sortBy}">
        <input type="hidden" th:field="*{sortDir}">
        <input type="hidden" th:field="*{page}">
        <input type="hidden" th:field="*{size}">
    </form>

    <!-- 결과 카운트 -->
    <p class="text-muted small mb-2">
        총 <strong th:text="${books.totalElements}">0</strong>권
        (<strong th:text="${books.number + 1}">1</strong> / <strong th:text="${books.totalPages}">1</strong> 페이지)
    </p>

    <!-- 도서 목록 테이블 -->
    <div class="card shadow-sm">
        <div class="table-responsive">
            <table class="table table-hover align-middle mb-0">
                <thead class="table-dark">
                    <tr>
                        <th>ISBN</th>
                        <th>제목</th>
                        <th>저자</th>
                        <th>카테고리</th>
                        <th class="text-center">총 수량</th>
                        <th class="text-center">대출 가능</th>
                        <th>등록일</th>
                        <th>관리</th>
                    </tr>
                </thead>
                <tbody>
                    <tr th:each="book : ${books.content}">
                        <td class="text-monospace small" th:text="${book.isbn}"></td>
                        <td>
                            <a th:href="@{/admin/books/{id}(id=${book.id})}"
                               class="fw-bold text-decoration-none" th:text="${book.title}"></a>
                        </td>
                        <td th:text="${book.author}"></td>
                        <td>
                            <span class="badge bg-secondary" th:text="${book.categoryName}"></span>
                        </td>
                        <td class="text-center" th:text="${book.totalCopies}"></td>
                        <td class="text-center">
                            <!-- 재고에 따라 배지 색상 변경 -->
                            <span class="badge stock-badge"
                                  th:classappend="${book.availableCopies > 0 ? 'bg-success' : 'bg-danger'}"
                                  th:text="${book.availableCopies > 0 ? book.availableCopies + '권' : '없음'}"></span>
                        </td>
                        <td th:text="${#temporals.format(book.createdAt, 'yyyy-MM-dd')}"></td>
                        <td class="table-actions">
                            <div class="btn-group btn-group-sm">
                                <a th:href="@{/admin/books/{id}/edit(id=${book.id})}"
                                   class="btn btn-outline-warning" title="수정">
                                    <i class="fas fa-edit"></i>
                                </a>
                                <button type="button" class="btn btn-outline-danger" title="삭제"
                                        th:onclick="|deleteBook(${book.id}, '${book.title}')|">
                                    <i class="fas fa-trash"></i>
                                </button>
                            </div>
                        </td>
                    </tr>
                    <tr th:if="${books.content.empty}">
                        <td colspan="8" class="text-center text-muted py-5">
                            <i class="fas fa-search fa-2x mb-2 d-block"></i>검색 결과가 없습니다.
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>

    <!-- 페이지네이션 -->
    <nav class="mt-3 d-flex justify-content-center" th:if="${books.totalPages > 1}">
        <ul class="pagination pagination-sm">
            <li class="page-item" th:classappend="${!books.hasPrevious()} ? 'disabled'">
                <a class="page-link"
                   th:href="${books.hasPrevious()} ? @{/admin/books(keyword=${searchDto.keyword}, categoryId=${searchDto.categoryId}, page=${books.number - 1})} : '#'">
                    <i class="fas fa-chevron-left"></i>
                </a>
            </li>
            <li class="page-item"
                th:each="p : ${#numbers.sequence(T(java.lang.Math).max(0, books.number - 2), T(java.lang.Math).min(books.totalPages - 1, books.number + 2))}"
                th:classappend="${p == books.number} ? 'active'">
                <a class="page-link"
                   th:href="@{/admin/books(keyword=${searchDto.keyword}, categoryId=${searchDto.categoryId}, page=${p})}"
                   th:text="${p + 1}"></a>
            </li>
            <li class="page-item" th:classappend="${!books.hasNext()} ? 'disabled'">
                <a class="page-link"
                   th:href="${books.hasNext()} ? @{/admin/books(keyword=${searchDto.keyword}, categoryId=${searchDto.categoryId}, page=${books.number + 1})} : '#'">
                    <i class="fas fa-chevron-right"></i>
                </a>
            </li>
        </ul>
    </nav>
</div>

<th:block layout:fragment="script">
<script>
function deleteBook(bookId, title) {
    if (!confirm(`"${title}" 도서를 삭제하시겠습니까?\n현재 대출 중인 도서는 삭제할 수 없습니다.`)) return;
    fetch(`/admin/books/${bookId}`, {
        method: 'DELETE',
        headers: { 'X-Requested-With': 'XMLHttpRequest' }
    })
    .then(r => r.json())
    .then(data => {
        if (data.message) { alert(data.message); location.reload(); }
        else               { alert('오류: ' + data.error); }
    });
}
</script>
</th:block>
```

### 7.3 도서 등록/수정 폼 (admin/book/form.html)

```html
<!DOCTYPE html>
<html lang="ko"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/base}">

<div layout:fragment="content">
    <div class="row justify-content-center">
        <div class="col-lg-8">

            <div class="d-flex justify-content-between align-items-center mb-4">
                <h4 th:text="${bookId == null ? '도서 등록' : '도서 수정'}"></h4>
                <a th:href="@{/admin/books}" class="btn btn-secondary btn-sm">
                    <i class="fas fa-arrow-left me-1"></i>목록으로
                </a>
            </div>

            <div class="card shadow-sm">
                <div class="card-body">
                    <form th:action="${bookId == null ? '/admin/books/new' : '/admin/books/' + bookId + '/edit'}"
                          method="post"
                          th:object="${bookForm}"
                          novalidate>

                        <!-- 전역 오류 -->
                        <div th:if="${#fields.hasGlobalErrors()}" class="alert alert-danger">
                            <li th:each="e : ${#fields.globalErrors()}" th:text="${e}"></li>
                        </div>

                        <div class="row g-3">
                            <!-- ISBN (등록시에만 편집) -->
                            <div class="col-md-6">
                                <label class="form-label">ISBN <span class="text-danger">*</span></label>
                                <input type="text" class="form-control"
                                       th:field="*{isbn}"
                                       th:classappend="${#fields.hasErrors('isbn')} ? 'is-invalid'"
                                       th:readonly="${bookId != null}"
                                       placeholder="13자리 숫자 (예: 9788932917245)">
                                <div class="invalid-feedback" th:errors="*{isbn}"></div>
                            </div>
                            <!-- 카테고리 -->
                            <div class="col-md-6">
                                <label class="form-label">카테고리</label>
                                <select class="form-select" th:field="*{categoryId}">
                                    <option value="">선택 안함</option>
                                    <option th:each="cat : ${categories}"
                                            th:value="${cat.id}"
                                            th:text="${cat.categoryName}"></option>
                                </select>
                            </div>
                            <!-- 제목 -->
                            <div class="col-md-8">
                                <label class="form-label">제목 <span class="text-danger">*</span></label>
                                <input type="text" class="form-control"
                                       th:field="*{title}"
                                       th:classappend="${#fields.hasErrors('title')} ? 'is-invalid'"
                                       placeholder="도서 제목">
                                <div class="invalid-feedback" th:errors="*{title}"></div>
                            </div>
                            <!-- 총 수량 -->
                            <div class="col-md-4">
                                <label class="form-label">총 수량 <span class="text-danger">*</span></label>
                                <input type="number" class="form-control"
                                       th:field="*{totalCopies}"
                                       th:classappend="${#fields.hasErrors('totalCopies')} ? 'is-invalid'"
                                       min="1">
                                <div class="invalid-feedback" th:errors="*{totalCopies}"></div>
                            </div>
                            <!-- 저자 -->
                            <div class="col-md-6">
                                <label class="form-label">저자 <span class="text-danger">*</span></label>
                                <input type="text" class="form-control"
                                       th:field="*{author}"
                                       th:classappend="${#fields.hasErrors('author')} ? 'is-invalid'"
                                       placeholder="저자명">
                                <div class="invalid-feedback" th:errors="*{author}"></div>
                            </div>
                            <!-- 출판사 -->
                            <div class="col-md-6">
                                <label class="form-label">출판사</label>
                                <input type="text" class="form-control" th:field="*{publisher}" placeholder="출판사">
                            </div>
                            <!-- 출판일 -->
                            <div class="col-md-4">
                                <label class="form-label">출판일</label>
                                <input type="date" class="form-control" th:field="*{publicationDate}">
                            </div>
                            <!-- 도서 설명 -->
                            <div class="col-12">
                                <label class="form-label">도서 설명</label>
                                <textarea class="form-control" th:field="*{description}" rows="3"
                                          placeholder="도서 소개 (최대 1000자)"></textarea>
                            </div>
                        </div>

                        <hr class="my-4">
                        <div class="d-flex justify-content-end gap-2">
                            <a th:href="@{/admin/books}" class="btn btn-secondary">
                                <i class="fas fa-times me-1"></i>취소
                            </a>
                            <button type="submit" class="btn btn-primary">
                                <i th:class="${bookId == null ? 'fas fa-plus' : 'fas fa-save'}" class="me-1"></i>
                                <span th:text="${bookId == null ? '도서 등록' : '수정 저장'}"></span>
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
```

### 7.4 대출 현황 페이지 (admin/loan/list.html)

```html
<!DOCTYPE html>
<html lang="ko"
      xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/base}">

<th:block layout:fragment="css">
<style>
    .overdue-row { background-color: #fff3cd !important; }
    .tab-active  { border-bottom: 3px solid #0d6efd; font-weight:600; }
</style>
</th:block>

<div layout:fragment="content">

    <div class="d-flex justify-content-between align-items-center mb-4">
        <h4 class="mb-0"><i class="fas fa-hand-holding-heart me-2"></i>대출 현황</h4>
        <!-- 연체 건수 경고 배지 -->
        <span th:if="${overdueCount > 0}" class="badge bg-danger fs-6">
            <i class="fas fa-exclamation-triangle me-1"></i>연체 <span th:text="${overdueCount}"></span>건
        </span>
    </div>

    <!-- 상태 탭 필터 -->
    <ul class="nav nav-tabs mb-4">
        <li class="nav-item">
            <a class="nav-link" th:href="@{/admin/loans}"
               th:classappend="${searchDto.status == null and !searchDto.onlyOverdue} ? 'active tab-active'">
               전체
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" th:href="@{/admin/loans(status='REQUESTED')}"
               th:classappend="${searchDto.status?.name() == 'REQUESTED'} ? 'active tab-active'">
               승인 대기
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" th:href="@{/admin/loans(status='BORROWED')}"
               th:classappend="${searchDto.status?.name() == 'BORROWED' and !searchDto.onlyOverdue} ? 'active tab-active'">
               대출중
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link text-danger" th:href="@{/admin/loans(onlyOverdue=true)}"
               th:classappend="${searchDto.onlyOverdue} ? 'active tab-active'">
               <i class="fas fa-exclamation-triangle me-1"></i>연체
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" th:href="@{/admin/loans(status='RETURNED')}"
               th:classappend="${searchDto.status?.name() == 'RETURNED'} ? 'active tab-active'">
               반납완료
            </a>
        </li>
    </ul>

    <!-- 결과 카운트 -->
    <p class="text-muted small mb-2">
        총 <strong th:text="${loans.totalElements}">0</strong>건
        (<strong th:text="${loans.number + 1}">1</strong> / <strong th:text="${loans.totalPages}">1</strong> 페이지)
    </p>

    <!-- 대출 목록 테이블 -->
    <div class="card shadow-sm">
        <div class="table-responsive">
            <table class="table table-hover align-middle mb-0">
                <thead class="table-dark">
                    <tr>
                        <th>대출번호</th>
                        <th>회원명</th>
                        <th>도서명</th>
                        <th>대출일</th>
                        <th>반납예정일</th>
                        <th>반납일</th>
                        <th class="text-center">상태</th>
                        <th class="text-center">연체일/연체료</th>
                        <th>처리</th>
                    </tr>
                </thead>
                <tbody>
                    <tr th:each="loan : ${loans.content}"
                        th:classappend="${loan.overdue} ? 'overdue-row'">
                        <td class="small text-monospace" th:text="${loan.loanNumber}"></td>
                        <td>
                            <div th:text="${loan.memberName}"></div>
                            <small class="text-muted" th:text="${loan.memberNumber}"></small>
                        </td>
                        <td th:text="${loan.bookTitle}"></td>
                        <td th:text="${loan.loanDate}"></td>
                        <td>
                            <!-- 연체면 반납예정일을 빨간색으로 -->
                            <span th:text="${loan.dueDate}"
                                  th:classappend="${loan.overdue} ? 'text-danger fw-bold'"></span>
                        </td>
                        <td th:text="${loan.returnDate ?: '-'}"></td>
                        <td class="text-center">
                            <span class="badge"
                                  th:classappend="${loan.status.name() == 'BORROWED'  ? 'bg-primary'   :
                                                   loan.status.name() == 'OVERDUE'   ? 'bg-danger'    :
                                                   loan.status.name() == 'RETURNED'  ? 'bg-success'   :
                                                   loan.status.name() == 'REQUESTED' ? 'bg-warning text-dark' : 'bg-secondary'}"
                                  th:text="${loan.status.displayName}"></span>
                        </td>
                        <td class="text-center">
                            <span th:if="${loan.overdue}" class="text-danger small">
                                <th:block th:text="${loan.overdueDays}"></th:block>일
                                / <th:block th:text="${#numbers.formatInteger(loan.overdueFee,1,'COMMA')}"></th:block>원
                            </span>
                            <span th:unless="${loan.overdue}">-</span>
                        </td>
                        <td>
                            <!-- 승인 대기 → [승인] 버튼 -->
                            <button th:if="${loan.status.name() == 'REQUESTED'}"
                                    class="btn btn-sm btn-success"
                                    th:onclick="|approveLoan(${loan.id})|">
                                <i class="fas fa-check me-1"></i>승인
                            </button>
                            <!-- 대출중·연체 → [반납] 버튼 -->
                            <button th:if="${loan.status.name() == 'BORROWED' or loan.status.name() == 'OVERDUE'}"
                                    class="btn btn-sm btn-outline-primary"
                                    th:onclick="|returnLoan(${loan.id}, '${loan.bookTitle}')|">
                                <i class="fas fa-undo me-1"></i>반납
                            </button>
                            <span th:if="${loan.status.name() == 'RETURNED'}" class="text-muted small">완료</span>
                        </td>
                    </tr>
                    <tr th:if="${loans.content.empty}">
                        <td colspan="9" class="text-center text-muted py-5">
                            <i class="fas fa-inbox fa-2x mb-2 d-block"></i>해당하는 대출 내역이 없습니다.
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>

    <!-- 페이지네이션 -->
    <nav class="mt-3 d-flex justify-content-center" th:if="${loans.totalPages > 1}">
        <ul class="pagination pagination-sm">
            <li class="page-item" th:classappend="${!loans.hasPrevious()} ? 'disabled'">
                <a class="page-link"
                   th:href="${loans.hasPrevious()} ? @{/admin/loans(status=${searchDto.status}, onlyOverdue=${searchDto.onlyOverdue}, page=${loans.number - 1})} : '#'">
                    <i class="fas fa-chevron-left"></i>
                </a>
            </li>
            <li class="page-item"
                th:each="p : ${#numbers.sequence(T(java.lang.Math).max(0, loans.number - 2), T(java.lang.Math).min(loans.totalPages - 1, loans.number + 2))}"
                th:classappend="${p == loans.number} ? 'active'">
                <a class="page-link"
                   th:href="@{/admin/loans(status=${searchDto.status}, onlyOverdue=${searchDto.onlyOverdue}, page=${p})}"
                   th:text="${p + 1}"></a>
            </li>
            <li class="page-item" th:classappend="${!loans.hasNext()} ? 'disabled'">
                <a class="page-link"
                   th:href="${loans.hasNext()} ? @{/admin/loans(status=${searchDto.status}, onlyOverdue=${searchDto.onlyOverdue}, page=${loans.number + 1})} : '#'">
                    <i class="fas fa-chevron-right"></i>
                </a>
            </li>
        </ul>
    </nav>

</div>

<th:block layout:fragment="script">
<script>
function approveLoan(loanId) {
    if (!confirm('해당 대출을 승인하시겠습니까?')) return;
    fetch(`/admin/loans/${loanId}/approve`, {
        method: 'POST', headers: { 'X-Requested-With': 'XMLHttpRequest' }
    })
    .then(r => r.json())
    .then(data => {
        alert(data.message || data.error);
        location.reload();
    });
}

function returnLoan(loanId, title) {
    if (!confirm(`"${title}" 도서의 반납을 처리하시겠습니까?`)) return;
    fetch(`/admin/loans/${loanId}/return`, {
        method: 'POST', headers: { 'X-Requested-With': 'XMLHttpRequest' }
    })
    .then(r => r.json())
    .then(data => {
        alert(data.message || data.error);
        location.reload();
    });
}
</script>
</th:block>
```

## 8. Enum 클래스 정의

```java
// 대출 상태
public enum LoanStatus {
    REQUESTED("승인 대기"),   // 회원이 신청, 관리자 승인 전
    APPROVED ("승인 완료"),   // 관리자 승인, 아직 수령 전
    BORROWED ("대출중"),      // 도서 수령 완료
    RETURNED ("반납완료"),    // 반납 처리 완료
    OVERDUE  ("연체"),        // 반납예정일 초과
    CANCELLED("취소");        // 관리자 또는 회원이 취소

    private final String displayName;
    LoanStatus(String displayName) { this.displayName = displayName; }
    public String getDisplayName()  { return displayName; }
}

// 회원 상태
public enum MemberStatus {
    ACTIVE   ("활성"),     // 정상 이용 중
    SUSPENDED("정지"),     // 연체료 미납 등으로 대출 정지
    WITHDRAWN("탈퇴");     // 회원 탈퇴

    private final String displayName;
    MemberStatus(String displayName) { this.displayName = displayName; }
    public String getDisplayName()   { return displayName; }
}
```

## 9. 설정 파일

### 9.1 application.yml

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/library_db?useSSL=false&characterEncoding=UTF-8&serverTimezone=Asia/Seoul
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: 1234

  jpa:
    hibernate:
      ddl-auto: validate          # Flyway가 스키마를 관리하므로 validate 사용
    show-sql: true
    properties:
      hibernate:
        format_sql: true
        default_batch_fetch_size: 100

  # Flyway DB 마이그레이션
  flyway:
    enabled: true
    locations: classpath:db/migration
    baseline-on-migrate: true

  thymeleaf:
    cache: false                  # 개발 중에는 false, 운영에서는 true
    prefix: classpath:/templates/
    suffix: .html

  # 관리자 세션 설정
  session:
    timeout: 30m                  # 관리자 세션 30분

server:
  port: 8080

# 도서관 비즈니스 규칙 설정 (커스텀 프로퍼티)
library:
  loan:
    max-period-days: 14           # 기본 대출 기간
    max-per-member:  5            # 회원당 최대 동시 대출 수
    overdue-fee-per-day: 100      # 연체료 (1일당 원)
  admin:
    login-url: /admin/login
    logout-url: /admin/logout
```

### 9.2 관리자 페이지 URL 구조 요약

| URL | 메서드 | 설명 |
|-----|--------|------|
| `GET  /admin` | GET | 대시보드 (통계 카드 4개) |
| `GET  /admin/books` | GET | 도서 목록 (검색·페이징) |
| `GET  /admin/books/new` | GET | 도서 등록 폼 |
| `POST /admin/books/new` | POST | 도서 등록 처리 |
| `GET  /admin/books/{id}` | GET | 도서 상세 |
| `GET  /admin/books/{id}/edit` | GET | 도서 수정 폼 |
| `POST /admin/books/{id}/edit` | POST | 도서 수정 처리 |
| `DELETE /admin/books/{id}` | DELETE (AJAX) | 도서 삭제 |
| `GET  /admin/loans` | GET | 대출 현황 (탭 필터: 전체/승인대기/대출중/연체/반납완료) |
| `POST /admin/loans/{id}/approve` | POST (AJAX) | 대출 승인 |
| `POST /admin/loans/{id}/return` | POST (AJAX) | 반납 처리 |
| `GET  /admin/members` | GET | 회원 목록 |
  
logging:
  level:
    org.hibernate.SQL: DEBUG
    org.hibernate.type.descriptor.sql.BasicBinder: TRACE
```

### 9.2 JPA 설정 클래스

```java
@Configuration
@EnableJpaAuditing
public class JpaConfig {
    
    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> {
            // 실제 구현에서는 SecurityContext에서 현재 사용자 정보를 가져와야 함
            return Optional.of("system");
        };
    }
}
```

## 10. CSS 스타일 (static/css/admin.css)

```css
/* 관리자 페이지 공통 스타일 */
body {
    background-color: #f8f9fa;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

.navbar-brand {
    font-weight: 600;
}

.card {
    border-radius: 8px;
    margin-bottom: 1.5rem;
}

.btn {
    border-radius: 6px;
    font-weight: 500;
}

.table th {
    font-weight: 600;
    border-bottom: 2px solid #dee2e6;
}

.badge {
    font-size: 0.75rem;
    font-weight: 500;
}

.pagination .page-link {
    border-radius: 6px;
    margin: 0 2px;
    border: 1px solid #dee2e6;
}

.pagination .page-item.active .page-link {
    background-color: #0d6efd;
    border-color: #0d6efd;
}

/* 검색 폼 스타일 */
.search-form {
    background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
    border: 1px solid #dee2e6;
}

/* 테이블 반응형 */
@media (max-width: 768px) {
    .table-responsive {
        font-size: 0.875rem;
    }
    
    .btn-group-sm .btn {
        padding: 0.125rem 0.5rem;
        font-size: 0.75rem;
    }
}

/* 폼 스타일 */
.form-control:focus,
.form-select:focus {
    border-color: #86b7fe;
    box-shadow: 0 0 0 0.25rem rgba(13, 110, 253, 0.25);
}

.required {
    color: #dc3545;
}

/* 알림 스타일 */
.alert {
    border-radius: 8px;
    border: none;
}

.alert-success {
    background-color: #d1e7dd;
    color: #0a3622;
}

.alert-danger {
    background-color: #f8d7da;
    color: #58151c;
}

/* 로딩 스피너 */
.spinner-border-sm {
    width: 1rem;
    height: 1rem;
}

/* 커스텀 배지 색상 */
.bg-info {
    background-color: #0dcaf0 !important;
}

.bg-warning {
    background-color: #ffc107 !important;
    color: #000 !important;
}
```

## 11. JavaScript 공통 함수 (static/js/admin.js)

```javascript
// 관리자 페이지 공통 JavaScript

// 전역 유틸리티 함수
const AdminUtils = {
    // 확인 대화상자
    confirm: function(message, callback) {
        if (confirm(message)) {
            if (typeof callback === 'function') {
                callback();
            }
            return true;
        }
        return false;
    },
    
    // AJAX 요청 공통 함수
    request: function(url, options = {}) {
        const defaultOptions = {
            headers: {
                'Content-Type': 'application/json',
                'X-Requested-With': 'XMLHttpRequest'
            }
        };
        
        return fetch(url, { ...defaultOptions, ...options })
            .then(response => {
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                return response.json();
            });
    },
    
    // 알림 메시지 표시
    showAlert: function(message, type = 'info') {
        const alertHtml = `
            <div class="alert alert-${type} alert-dismissible fade show" role="alert">
                <i class="fas fa-${type === 'success' ? 'check-circle' : type === 'danger' ? 'exclamation-circle' : 'info-circle'}"></i>
                ${message}
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
        `;
        
        const container = document.querySelector('.container-fluid');
        if (container) {
            container.insertAdjacentHTML('afterbegin', alertHtml);
            
            // 5초 후 자동 제거
            setTimeout(() => {
                const alert = container.querySelector('.alert');
                if (alert) {
                    alert.remove();
                }
            }, 5000);
        }
    },
    
    // 로딩 상태 표시
    showLoading: function(element, show = true) {
        if (show) {
            element.disabled = true;
            const originalText = element.textContent;
            element.dataset.originalText = originalText;
            element.innerHTML = '<span class="spinner-border spinner-border-sm me-2"></span>처리중...';
        } else {
            element.disabled = false;
            element.textContent = element.dataset.originalText || '확인';
        }
    },
    
    // 날짜 포맷팅
    formatDate: function(dateString, format = 'YYYY-MM-DD HH:mm') {
        const date = new Date(dateString);
        const year = date.getFullYear();
        const month = String(date.getMonth() + 1).padStart(2, '0');
        const day = String(date.getDate()).padStart(2, '0');
        const hours = String(date.getHours()).padStart(2, '0');
        const minutes = String(date.getMinutes()).padStart(2, '0');
        
        return format
            .replace('YYYY', year)
            .replace('MM', month)
            .replace('DD', day)
            .replace('HH', hours)
            .replace('mm', minutes);
    }
};

// 페이지 로드 시 초기화
document.addEventListener('DOMContentLoaded', function() {
    // 툴팁 초기화
    const tooltipTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="tooltip"]'));
    tooltipTriggerList.map(function (tooltipTriggerEl) {
        return new bootstrap.Tooltip(tooltipTriggerEl);
    });
    
    // 모든 테이블에 반응형 클래스 추가
    document.querySelectorAll('table').forEach(table => {
        if (!table.closest('.table-responsive')) {
            table.classList.add('table-responsive');
        }
    });
    
    // 폼 유효성 검사 스타일 적용
    document.querySelectorAll('.needs-validation').forEach(form => {
        form.addEventListener('submit', function(e) {
            if (!form.checkValidity()) {
                e.preventDefault();
                e.stopPropagation();
            }
            form.classList.add('was-validated');
        });
    });
});
```

## 12. 추가 기능 구현 예시

### 12.1 엑셀 다운로드 기능

```java
// Controller에 추가
@GetMapping("/export")
public void exportUsers(
        @ModelAttribute UserSearchDto searchDto,
        HttpServletResponse response) throws IOException {
    
    response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
    response.setHeader("Content-Disposition", "attachment; filename=users.xlsx");
    
    List<UserListDto> users = userService.getAllUsers(searchDto);
    
    try (Workbook workbook = new XSSFWorkbook()) {
        Sheet sheet = workbook.createSheet("사용자 목록");
        
        // 헤더 생성
        Row headerRow = sheet.createRow(0);
        String[] headers = {"ID", "사용자명", "이메일", "전화번호", "역할", "상태", "가입일"};
        for (int i = 0; i < headers.length; i++) {
            Cell cell = headerRow.createCell(i);
            cell.setCellValue(headers[i]);
        }
        
        // 데이터 생성
        int rowNum = 1;
        for (UserListDto user : users) {
            Row row = sheet.createRow(rowNum++);
            row.createCell(0).setCellValue(user.getId());
            row.createCell(1).setCellValue(user.getUsername());
            row.createCell(2).setCellValue(user.getEmail());
            row.createCell(3).setCellValue(user.getPhone());
            row.createCell(4).setCellValue(user.getRole().getDisplayName());
            row.createCell(5).setCellValue(user.getStatus().getDisplayName());
            row.createCell(6).setCellValue(user.getCreatedAt().toString());
        }
        
        // 컬럼 크기 자동 조정
        for (int i = 0; i < headers.length; i++) {
            sheet.autoSizeColumn(i);
        }
        
        workbook.write(response.getOutputStream());
    }
}
```

### 12.2 대량 작업 기능

```javascript
// 체크박스 전체 선택/해제
function toggleAllCheckboxes(source) {
    const checkboxes = document.querySelectorAll('input[name="selectedIds"]');
    checkboxes.forEach(checkbox => {
        checkbox.checked = source.checked;
    });
    updateBulkActionButtons();
}

// 선택된 항목 수에 따른 버튼 상태 업데이트
function updateBulkActionButtons() {
    const selectedCount = document.querySelectorAll('input[name="selectedIds"]:checked').length;
    const bulkButtons = document.querySelector('.bulk-actions');
    
    if (selectedCount > 0) {
        bulkButtons.style.display = 'block';
        document.getElementById('selectedCount').textContent = selectedCount;
    } else {
        bulkButtons.style.display = 'none';
    }
}

// 대량 삭제
function bulkDelete() {
    const selectedIds = Array.from(document.querySelectorAll('input[name="selectedIds"]:checked'))
        .map(checkbox => checkbox.value);
    
    if (selectedIds.length === 0) {
        alert('삭제할 항목을 선택해주세요.');
        return;
    }
    
    if (confirm(`선택된 ${selectedIds.length}개의 사용자를 삭제하시겠습니까?`)) {
        AdminUtils.request('/admin/users/bulk-delete', {
            method: 'POST',
            body: JSON.stringify({ ids: selectedIds })
        })
        .then(data => {
            AdminUtils.showAlert(data.message, 'success');
            location.reload();
        })
        .catch(error => {
            AdminUtils.showAlert('삭제 중 오류가 발생했습니다.', 'danger');
        });
    }
}
```

## 13. 보안 고려사항

### 13.1 Spring Security 설정 (선택사항)

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers("/css/**", "/js/**", "/images/**").permitAll()
                .anyRequest().authenticated()
            )
            .formLogin(form -> form
                .loginPage("/login")
                .defaultSuccessUrl("/admin")
                .permitAll()
            )
            .logout(logout -> logout
                .logoutUrl("/logout")
                .logoutSuccessUrl("/login")
                .permitAll()
            )
            .csrf(csrf -> csrf
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            );
            
        return http.build();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 13.2 입력 검증 및 XSS 방지

```java
// XSS 방지를 위한 HTML 이스케이프 유틸리티
@Component
public class SecurityUtils {
    
    private static final PolicyFactory POLICY = Sanitizers.FORMATTING.and(Sanitizers.LINKS);
    
    public static String sanitizeHtml(String input) {
        if (input == null) return null;
        return POLICY.sanitize(input);
    }
    
    public static String escapeHtml(String input) {
        if (input == null) return null;
        return StringEscapeUtils.escapeHtml4(input);
    }
}
```

## 14. 테스트 코드 예시

### 14.1 Service 테스트

```java
@SpringBootTest
@Transactional
class UserServiceTest {
    
    @Autowired
    private UserService userService;
    
    @Autowired
    private UserRepository userRepository;
    
    @Test
    @DisplayName("사용자 생성 테스트")
    void createUser() {
        // given
        UserFormDto userForm = new UserFormDto();
        userForm.setUsername("testuser");
        userForm.setPassword("password123");
        userForm.setEmail("test@example.com");
        userForm.setRole(UserRole.USER);
        userForm.setStatus(UserStatus.ACTIVE);
        
        // when
        Long userId = userService.createUser(userForm);
        
        // then
        assertThat(userId).isNotNull();
        Optional<User> savedUser = userRepository.findById(userId);
        assertThat(savedUser).isPresent();
        assertThat(savedUser.get().getUsername()).isEqualTo("testuser");
    }
    
    @Test
    @DisplayName("중복 사용자명 검증 테스트")
    void createUserWithDuplicateUsername() {
        // given
        User existingUser = User.builder()
                .username("duplicate")
                .password("password")
                .email("existing@example.com")
                .role(UserRole.USER)
                .status(UserStatus.ACTIVE)
                .build();
        userRepository.save(existingUser);
        
        UserFormDto userForm = new UserFormDto();
        userForm.setUsername("duplicate");
        userForm.setPassword("password123");
        userForm.setEmail("new@example.com");
        userForm.setRole(UserRole.USER);
        userForm.setStatus(UserStatus.ACTIVE);
        
        // when & then
        assertThatThrownBy(() -> userService.createUser(userForm))
                .isInstanceOf(DuplicateKeyException.class)
                .hasMessageContaining("이미 존재하는 사용자명입니다");
    }
}
```

### 14.2 Controller 테스트

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private UserService userService;
    
    @Test
    @DisplayName("사용자 목록 페이지 테스트")
    void userListPage() throws Exception {
        // given
        List<UserListDto> users = Arrays.asList(
                UserListDto.builder().id(1L).username("user1").build(),
                UserListDto.builder().id(2L).username("user2").build()
        );
        Page<UserListDto> userPage = new PageImpl<>(users);
        
        when(userService.getUsers(any(UserSearchDto.class))).thenReturn(userPage);
        
        // when & then
        mockMvc.perform(get("/admin/users"))
                .andExpect(status().isOk())
                .andExpect(view().name("admin/users/list"))
                .andExpect(model().attributeExists("users"))
                .andExpect(model().attribute("users", userPage));
    }
}
```

## 15. 배포 및 운영 가이드

### 15.1 프로덕션 설정 (application-prod.yml)

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/admin_db
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    driver-class-name: com.mysql.cj.jdbc.Driver
    
  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect
        
  thymeleaf:
    cache: true
    
logging:
  level:
    com.example.admin: INFO
    org.hibernate.SQL: WARN
    
server:
  port: 8080
  compression:
    enabled: true
    mime-types: text/html,text/xml,text/plain,text/css,text/javascript,application/javascript,application/json
```

### 15.2 Docker 설정

```dockerfile
FROM openjdk:17-jdk-slim

VOLUME /tmp

COPY target/admin-app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app.jar", "--spring.profiles.active=prod"]
```
