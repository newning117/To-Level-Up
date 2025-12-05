# Blog Crawling

# Naver Blog AB Test Data Collector

네이버 블로그 게시물 데이터를 수집해서  
제목 스타일 A/B 테스트 및 기본 지표(댓글, 공감 수 등)를 분석하기 위한 크롤링 프로젝트입니다.

> **대상 블로그**: `https://blog.naver.com/블로그 아이디 입력`  
> **목적**: 제목/문체에 따른 홈피드 노출 및 반응(댓글/공감) 차이 분석을 위한 블로그 기본 수치 수집

---

## 1. 기능 개요

이 프로젝트는 다음 정보를 자동으로 수집합니다.

- 각 게시물별
  - URL (logNo 기반)
  - 제목 (og:title 기준)
  - 업로드 날짜 (`.se_publishDate`, `span.t_date` 등)
  - 댓글 수 (`.u_cbox_count`, `.u_cbox_cnt`)
  - 공감(좋아요) 수 (`span.u_likeit_text._count.num`)

- 전체 블로그 기준
  - 총 게시물 수
  - 총 댓글 수 / 총 공감 수
  - 게시물 1건당 평균 댓글 수 / 평균 공감 수
  - 댓글/공감 수 분포(평균, 표준편차, 상위 10% 기준 등)

수집된 데이터는 `pandas.DataFrame` 또는 구글 스프레드시트에 저장하여  
AB 테스트 전·후 지표 비교에 활용할 수 있습니다.

---

## 2. 사용 기술 / 환경

- MacBook Pro M4
- Crome
- Colab
- Python 3 (코랩 런타임 유형)
- 주요 라이브러리
  - `requests`
  - `beautifulsoup4`
  - `pandas`
  - `json`, `re`, `html` (표준 라이브러리)


---

# 부록

## 브라우저 개발자 도구에서 User-Agent 확인 방법

### 1) User-Agent 확인 방법 (Chrome 기준)

1. 크롬에서 아무 페이지나 연 다음,  
   `F12` 또는 `⌥⌘I`(Option + Command + I, Mac)를 눌러 **개발자 도구**를 엽니다.
2. 상단 탭에서 **Network** 탭을 클릭합니다.
3. 페이지를 한 번 새로고침합니다. (`F5` 또는 `⌘R`)
4. 왼쪽 요청 리스트에서 아무 **문서(html)** 또는 **API 요청** 하나를 클릭합니다.
5. 오른쪽 패널에서 **Headers** 탭을 선택합니다.
6. `Request Headers` 영역에서 `user-agent` 항목을 찾습니다.

   예시:
   ```text
   user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)
               AppleWebKit/537.36 (KHTML, like Gecko)
               Chrome/142.0.0.0 Safari/537.36

  ## 쿠키(Cookie) 확인 방법 (Chrome 기준)

> ⚠️ **주의:** 쿠키 값은 사실상 로그인 세션 정보입니다.  
> 절대 깃허브 공개 저장소나 블로그에 그대로 올리면 안 됩니다.

네이버 블로그 API 호출 시 필요한 `Cookie` 값을 확인하는 방법은 다음과 같습니다.

### 1) 개발자 도구 열기

1. 크롬에서 네이버 블로그 페이지를 엽니다.  
2. `F12` 또는 `⌥⌘I`(Option + Command + I, Mac)를 눌러 **개발자 도구**를 엽니다.
3. 상단 탭에서 **Network** 탭을 클릭합니다.

### 2) 네트워크 요청 캡처하기

1. Network 탭이 열린 상태에서 페이지를 한 번 새로고침합니다. (`F5` 또는 `⌘R`)
2. 왼쪽 요청 목록에서 `blog.naver.com`으로 나가는 요청 중 하나를 선택합니다.  
   - 예: `PostView.naver`, `PostTitleListAsync.naver`, `/api/blogs/...` 등

### 3) Request Headers에서 Cookie 찾기

1. 요청을 클릭한 뒤, 오른쪽에 있는 **Headers** 탭을 클릭합니다.
2. 아래로 스크롤하여 **Request Headers** 영역을 찾습니다.
3. 항목 중에서 `cookie` 또는 `Cookie` 라인을 찾습니다.

   예시:

   ```text
   cookie: NNB=...; BA_DEVICE=...; NID_AUT=...; NID_SES=...; JSESSIONID=...
