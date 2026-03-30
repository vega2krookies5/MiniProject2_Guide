# REST API 설계서

- **팀명**: every moment
- **작성자**: 최대현
- **문서버전**: v1.1
- **작성일**: 2025-09-16
- **API 베이스 URL**: `http://localhost:8080/api`
- **포맷**: JSON (UTF-8)
- **인증**: JWT Bearer (`Authorization: Bearer <token>`)

---

## 목차

1. 공통 규약
2. 인증/권한
3. 에러 응답 규약
4. 엔드포인트 상세
5. 페이징/정렬 규약
6. 변경 이력

---

## 1) 공통 규약

- 컬렉션 복수형, 소문자-하이픈 경로.
- 메서드-의미 매핑: GET=조회, POST=생성/액션, PUT/PATCH=수정, DELETE=삭제.
- 서버시간: ISO-8601(UTC) 권장, 예: `2025-09-16T01:23:45Z`.
- 모든 쓰기 요청은 CSRF 무관(REST, JWT).

## 2) 인증/권한

- 모든 쓰기/민감정보 엔드포인트는 JWT 필요.
- 사용자 식별은 토큰의 subject/claims 기반.

## 3) 에러 응답 규약

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "title은(는) 비어 있을 수 없습니다.",
    "details": [{ "field": "title", "rejectedValue": "" }]
  },
  "timestamp": "2025-09-16T01:23:45Z",
  "path": "/api/posts"
}
```

- 공통 코드: `VALIDATION_ERROR, AUTH_REQUIRED, ACCESS_DENIED, NOT_FOUND, CONFLICT, BUSINESS_RULE, SERVER_ERROR`

---

## 4) 엔드포인트 상세

### AuthController (AuthController.java)

- **Base Path**: `/api/school/auth`

#### POST `/api/school/auth/register` — register

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: 불필요
- **Request Body**:

```json
{
  "username": "user",
  "gender": 0,
  "email": "user@example.com",
  "password": "a1234567",
  "smoking": false
}
```

**Response Type**: `BaseResponse<RegisterResponse>`
**Status Codes**:

- 201 Created: 회원 생성 성공
- 400/409: 검증 실패 또는 중복
- 500: 서버 오류
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/school/auth/register" \
  -H "Content-Type: application/json" \
  -d '{"username":"user","gender":0,"email":"user@example.com","password":"a1234567","smoking":false}'
```

#### POST `/api/school/auth/login` — login

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: 불필요
- **Request Body**:

```json
{
  "email": "user@example.com",
  "password": "a1234567"
}
```

**Response Type**: `BaseResponse<LoginResponse>`
**Status Codes**:

- 200 OK: 로그인 성공
- 400/401: 검증 실패 또는 인증 실패
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/school/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"a1234567"}'
```

#### POST `/api/school/auth/refresh` — refresh

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: 불필요 _(본문에 refreshToken 제출)_
- **Request Body**:

```json
{ "refreshToken": "eyJhbGciOi..." }
```

**Response Type**: `BaseResponse<RefreshResponse>`
**Status Codes**:

- 200 OK: 토큰 재발급 성공
- 401/403: 토큰 무효/만료
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/school/auth/refresh" \
  -H "Content-Type: application/json" \
  -d '{"refreshToken":"eyJhbGciOi..."}'
```

#### POST `/api/school/auth/logout` — logout

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: 불필요 _(정책에 따라 Bearer JWT 요구 가능)_
- **Request Body**:

```json
{
  "refreshToken": "eyJhbGciOi..."
}
```

**Response Type**: `Void` _(일반적으로 200 OK 또는 204 No Content)_
**Status Codes**:

- 200/204: 로그아웃 처리 성공
- 400/401: 요청 또는 토큰 오류
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/school/auth/logout" \
  -H "Content-Type: application/json" \
  -d '{"refreshToken":"eyJhbGciOi..."}'
```

### UserController (UserController.java)

- **Base Path**: `/api/school`

#### GET `/api/school/user` — me

- **Produces**: application/json
- **Auth**: Bearer JWT
  **Response Type**: `BaseResponse<UserDTO>`
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/school/user" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

#### PUT `/api/school/user` — updateUsername

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT
- **Request Body**:

```json
{ "username": "new_username" }
```

**Response Type**: `BaseResponse<UserDTO>`
**Status Codes**:

- 200 OK: 수정 성공
- 400/401/403: 검증/인증/권한 오류
  **cURL 예시**:

```bash
curl -X PUT "http://localhost:8080/api/school/user" \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"username":"new_username"}'
```

#### GET `/api/school/user/status` — getMyStatus

- **Produces**: application/json
- **Auth**: Bearer JWT
  **Response Type**: `BaseResponse<Map<String, Object>>`
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/school/user/status" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

### CommentController (CommentController.java)

- **Base Path**: `/api/comments`

#### POST `/api/comments/{postId}` — addComment

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT
  **Path Params**:
- `postId` (Long) — required
  **Request Body (예시)**:

```json
{
  "id": 0,
  "content": "string",
  "authorId": 0,
  "authorName": "string",
  "createdAt": {
    "$ref": "LocalDateTime"
  }
}
```

**Response Type**: `Map<String, Long>`
**Status Codes**:

- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/comments/{postId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json" \
-d '{"example": "data"}'
```

#### GET `/api/comments/{postId}` — getComments

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `postId` (Long) — required
  **Response Type**: `List<CommentItem>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/comments/{postId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### DELETE `/api/comments/{id}` — deleteComment

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `id` (Long) — required
  **Response Type**: `ResponseEntity<Void>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X DELETE "http://localhost:8080/api/comments/{id}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### PATCH `/api/comments/{id}` — updateComment

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `id` (Long) — required
  **Request Body (예시)**:

```json
{
  "id": 0,
  "content": "string",
  "authorId": 0,
  "authorName": "string",
  "createdAt": {
    "$ref": "LocalDateTime"
  }
}
```

**Response Type**: `CommentItem`
**Status Codes**:

- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X PATCH "http://localhost:8080/api/comments/{id}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json" \
-d '{"example": "data"}'
```

### PostController (PostController.java)

- **Base Path**: `/api/posts`

#### GET `/api/posts/{id}` — getPost

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `id` (Long) — required
  **Response Type**: `PostDetail`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/posts/{id}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### DELETE `/api/posts/{id}` — deletePost

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `id` (Long) — required
  **Response Type**: `ResponseEntity<Void>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X DELETE "http://localhost:8080/api/posts/{id}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### PATCH `/api/posts/{id}` — updatePost

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `id` (Long) — required
  **Request Body (예시)**:

```json
{
  "id": 0,
  "category": "string",
  "title": "string",
  "content": "string",
  "createdAt": {
    "$ref": "LocalDateTime"
  },
  "updatedAt": {
    "$ref": "LocalDateTime"
  },
  "authorId": 0,
  "authorName": "string",
  "comments": []
}
```

**Response Type**: `PostDetail`
**Status Codes**:

- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X PATCH "http://localhost:8080/api/posts/{id}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json" \
-d '{"example": "data"}'
```

### BoardLogController (BoardLogController.java)

- **Base Path**: `/api/board-logs`

#### GET `/api/board-logs/user/{userId}` — getLogsByUser

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
  **Response Type**: `List<BoardLogEntity>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/board-logs/user/{userId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### GET `/api/board-logs/type/{targetType}` — getLogsByTargetType

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `targetType` (String) — required
  **Response Type**: `List<BoardLogEntity>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/board-logs/type/{targetType}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

### ChatRestController (ChatRestController.java)

- **Base Path**: `/api/chat`

#### POST `/api/chat/rooms` — createOrGetRoom

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Request Body (예시)**:

```json
{
  "opponentUserId": 0
}
```

**Response Type**: `RoomRes`
**Status Codes**:

- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/chat/rooms" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json" \
-d '{"example": "data"}'
```

#### GET `/api/chat/rooms/{roomId}/messages` — history

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `roomId` (Long) — required
  **Query Params**:
- `page` (int) — optional
- `size` (int) — optional
  **Response Type**: `Page<MsgRes>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/chat/rooms/{roomId}/messages" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### GET `/api/chat/rooms` — getRooms

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Response Type**: `List<RoomRes>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/chat/rooms" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

### MatchController (MatchController.java)

- **Base Path**: `/api/match`

#### POST `/api/match/propose` — proposeMatch

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT
  **Request Body (예시)**:

```json
{
  "matchId": 0,
  "proposerId": 0,
  "targetUserId": 0,
  "user1Score": 0,
  "user2Score": 0,
  "similarityScore": 0.0,
  "status": "string",
  "message": "string"
}
```

**Response Type**: `ResponseEntity<MatchResponseDTO>`
**Status Codes**:

- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/match/propose" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json" \
-d '{"example": "data"}'
```

#### POST `/api/match/accept/{matchId}` — acceptMatch

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `matchId` (Long) — required
  **Response Type**: `ResponseEntity<MatchResponseDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/match/accept/{matchId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### POST `/api/match/reject/{matchId}` — rejectMatch

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `matchId` (Long) — required
  **Response Type**: `ResponseEntity<MatchResponseDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/match/reject/{matchId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### POST `/api/match/get-rejected-match-users/{matchId}` — getRejectedMatchUsers

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `matchId` (Long) — required
  **Response Type**: `ResponseEntity<List<Long>>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/match/get-rejected-match-users/{matchId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### POST `/api/match/swap/{matchId}` — swapMatch

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `matchId` (Long) — required
  **Response Type**: `ResponseEntity<MatchResponseDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/match/swap/{matchId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### POST `/api/match/propose-new/{proposerId}/{targetUserId}` — proposeNewMatch

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `proposerId` (Long) — required
- `targetUserId` (Long) — required
  **Response Type**: `ResponseEntity<MatchResponseDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/match/propose-new/{proposerId}/{targetUserId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

### MatchRecommendationController (MatchRecommendationController.java)

- **Base Path**: `/api/match/recommendation`

#### GET `/api/match/recommendation/list/{userId}` — getMatchingRecommendationsList

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
  **Response Type**: `ResponseEntity<List<MatchRecommendationDTO>>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/match/recommendation/list/{userId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

### MatchResultController (MatchResultController.java)

- **Base Path**: `/api/match/result`

#### GET `/api/match/result/{userId}` — getSelfMatchResult

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
  **Response Type**: `ResponseEntity<List<MatchResultDTO>>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/match/result/{userId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### GET `/api/match/result/status/{userId}/{matchUserId}` — getMatchStatusResult

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
- `matchUserId` (Long) — required
  **Response Type**: `ResponseEntity<List<MatchResultDTO>>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/match/result/status/{userId}/{matchUserId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

#### GET `/api/match/result/result/{userId}/{matchUserId}` — getMatchResult

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
- `matchUserId` (Long) — required
  **Response Type**: `ResponseEntity<MatchResultDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/match/result/result/{userId}/{matchUserId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

### PreferenceController (PreferenceController.java)

- **Base Path**: `/api/preferences`

#### POST `/api/preferences/calculate/{userId}` — calculateAndSavePreferences

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
  **Response Type**: `ResponseEntity<PreferenceResponseDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/preferences/calculate/{userId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

### SurveyController (SurveyController.java)

- **Base Path**: `/api/survey`

#### POST `/api/survey/submit/{userId}` — submitSurvey

- **Consumes**: application/json
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
  **Request Body (예시)**:

```json
{
  "id": 0,
  "userId": 0,
  "sleepTime": 0,
  "cleanliness": 0,
  "noiseSensitivity": 0,
  "height": 0,
  "roomTemp": 0
}
```

**Response Type**: `ResponseEntity<SurveyResult>`
**Status Codes**:

- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X POST "http://localhost:8080/api/survey/submit/{userId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json" \
-d '{"example": "data"}'
```

#### GET `/api/survey/{userId}` — getSurveyResult

- **Consumes**: N/A
- **Produces**: application/json
- **Auth**: Bearer JWT (권장)
  **Path Params**:
- `userId` (Long) — required
  **Response Type**: `ResponseEntity<SurveyResultResponseDTO>`
  **Status Codes**:
- 200 OK: 성공 처리
- 201 Created: 생성 성공 (해당되는 경우)
- 204 No Content: 삭제 성공 (본문 없음)
- 400/401/403/404/409/422/500: 오류 상황
  **cURL 예시**:

```bash
curl -X GET "http://localhost:8080/api/survey/{userId}" \
-H "Authorization: Bearer <JWT_TOKEN>" \
-H "Content-Type: application/json"
```

---

## 5) 페이징/정렬 규약

- `page` (0-base), `size` (기본 20, 최대 100), `sort`(예: `createdAt,desc`)
- 응답 예시

```json
{
  "content": [{ "id": 1 }],
  "page": { "number": 0, "size": 20, "totalElements": 123, "totalPages": 7 }
}
```

## 6) 변경 이력

- v1.1 (상세판, 2025-09-16): 컨트롤러 소스 기반 자동 추출 + 상세 파라미터/샘플 추가
- v1.0 (요약판): 1차 초안
