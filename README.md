주식 분석 플랫폼 UI 프로토타입
프로젝트 개요
이 문서는 주식 분석 플랫폼의 프론트엔드 UI 프로토타입(index.html)에 대한 기능 명세와 백엔드 API 가이드를 제공합니다. 
팀원들이 프로젝트의 전체적인 구조와 데이터 흐름을 명확하게 이해하고 원활하게 협업하는 것을 목표로 합니다.

프로토타입 실행 방법
프로젝트 폴더에서 index.html 파일을 다운로드합니다.
웹 브라우저(Chrome, Firefox 등)에서 해당 파일을 엽니다.
별도의 서버 설치나 빌드 과정 없이 바로 UI와 기본 동작을 확인할 수 있습니다.

🏗️ 프로젝트 구조
모든 UI와 기능은 하나의 index.html 파일에 구현되어 있습니다.
SPA (Single Page Application): 페이지 이동은 페이지를 새로고침하는 대신, JavaScript를 통해 필요한 부분만 동적으로 보여주는 방식으로 동작합니다.
Mock Data: 현재 모든 데이터는 index.html 내의 JavaScript Mock 데이터(가상 데이터)를 사용합니다. 실제 개발 시 이 부분을 백엔드 API 호출로 대체해야 합니다.

💻 페이지별 기능 및 API 명세
2.1. 인증 (로그인 / 회원가입)
UI 동작:
초기 화면은 로그인 폼입니다.
[계정 만들기] 링크 클릭 시 회원가입 폼으로 전환됩니다.
비밀번호 입력 시 눈(👁️) 아이콘을 클릭하여 입력값을 보거나 가릴 수 있습니다. (프론트엔드 처리)

로그인 성공 시, 인증 컨테이너는 사라지고 메인 앱 컨테이너가 나타납니다.

백엔드 API:

회원가입: POST /api/auth/signup

// Request Body
{
  "email": "user@example.com",
  "userId": "user123",
  "password": "password123"
}
// Response
{ "message": "회원가입 성공" }

로그인: POST /api/auth/login

// Request Body
{
  "userId": "user123",
  "password": "password123"
}
// Response
{
  "userInfo": { "userId": "user123", "email": "user@example.com" },
  "token": "your_jwt_token_here"
}

2.2. 홈 (#home-page)
UI 동작:

주요 지수 위젯: 금, 유가, KOSPI 지수의 최신 정보를 보여줍니다.

KOSPI 그래프 위젯: 1일/1주/1달/1년 버튼에 따라 다른 기간의 차트를 보여줍니다.

종목 위젯: '거래량 상위'와 '즐겨찾기' 토글 버튼으로 두 가지 목록을 전환하여 볼 수 있습니다.

백엔드 API:

주요 지수 데이터: GET /api/widgets/indices

// Response
{
  "goldPrice": 87500,
  "oilPrice": 1750,
  "kospi": { "value": 2850.43, "change": 12.5 }
}

KOSPI 차트 데이터: GET /api/widgets/kospi-chart?period={day|week|month|year}

Response: 차트 라이브러리가 요구하는 형식의 시계열 데이터.

종목 위젯 데이터: GET /api/widgets/stocks?type={volume|favorites}

Response: [{ "name": "삼성전자", "price": 85000, "changePercent": 1.43, "volume": 15000000 }, ...] (type에 따라 상위 5개 또는 즐겨찾기 5개)

2.3. 국내 (#domestic-page)
UI 동작:

KOSPI 전체 종목 리스트를 테이블 형태로 보여줍니다.

검색/정렬: 프론트엔드에서 실시간으로 처리합니다.

즐겨찾기: 별(⭐) 아이콘을 클릭하여 즐겨찾기를 추가/해제할 수 있습니다.

백엔드 API:

KOSPI 전체 종목 데이터: GET /api/stocks/kospi

Response: 모든 종목 데이터를 하나의 배열로 반환합니다.

// Response (Array)
[
  { "id": 1, "code": "005930", "name": "삼성전자", "price": 85000, "change": 1200, "changePercent": 1.43, "volume": 15000000, "isFavorite": false },
  ...
]

즐겨찾기 추가/해제: POST /api/favorites

// Request Body
{
  "stockId": 1,
  "isFavorite": true
}
// Response
{ "message": "처리 완료" }

2.4. AI 분석 (#ai-analysis-page)
UI 동작:

'국내' 페이지와 동일한 UI/UX를 가지며, AI가 예측한 미래 데이터를 보여줍니다.

백엔드 API:

AI 예측 데이터: GET /api/stocks/ai-predictions

Response: '국내' API와 유사한 구조이지만, 예측값을 포함합니다.

// Response (Array)
[
  { "id": 1, "code": "005930", "name": "삼성전자", "predictedPrice": 86500, "expectedChange": 1500, "expectedChangePercent": 1.76, "isFavorite": false },
  ...
]

2.5. 기타 페이지 (시장, 뉴스, 모의 투자, MY)
시장 (#market-page): GET /api/market/details (금, 유가 등의 상세 정보 및 차트 데이터)

뉴스 (#news-page): GET /api/news?query={stockName} (구현 예정)

모의 투자 (#mock-investment-page): (구현 예정)

MY (#my-page):

GET /api/user/me (현재 사용자 정보 조회)

GET /api/favorites (사용자의 전체 즐겨찾기 목록 조회)

DELETE /api/favorites/{stockId} (즐겨찾기 삭제)

=
이 문서를 기준으로 프론트엔드와 백엔드 개발을 진행합니다. API 명세에 변경이 필요할 경우, 문서를 업데이트
