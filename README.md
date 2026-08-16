# 아너링크 (honorlink.li)

프랜차이즈 가맹점 관리 시스템 **아너링크** 랜딩 페이지.
정적 파일만으로 동작하며 빌드 과정이 없습니다. 파일을 그대로 올리면 됩니다.

```
index.html          홈 (제품 소개 + 36개월 비용 계산기 + 적합도 진단 + FAQ)
contact/index.html  문의 (텔레그램 + QR)
favicon.svg         파비콘
robots.txt          크롤러 허용 + 사이트맵 위치
sitemap.xml         색인 대상 URL 2개
CNAME               GitHub Pages 커스텀 도메인 (honorlink.li)
.nojekyll           GitHub 가 파일을 Jekyll 로 가공하지 않게 함
```

---

## 배포 순서

### 1. GitHub 저장소에 올리기

저장소를 하나 만들고 위 파일을 **루트에** 넣습니다. 폴더 안에 넣으면 안 됩니다.

```
저장소루트/
├── index.html
├── contact/index.html
├── favicon.svg
├── robots.txt
├── sitemap.xml
├── CNAME
└── .nojekyll
```

`.nojekyll` 은 이름이 점(`.`)으로 시작해서 파일 탐색기에서 안 보일 수 있습니다.
빠지면 일부 파일이 배포에서 제외될 수 있으니 반드시 함께 올리세요.

### 2. GitHub Pages 켜기

저장소 → **Settings → Pages**

- Source: `Deploy from a branch`
- Branch: `main` / 폴더 `/ (root)`
- Save

몇 분 뒤 `https://<계정명>.github.io/<저장소명>/` 으로 먼저 뜹니다.

### 3. 오렌지웹사이트에서 DNS 설정

도메인 관리 화면에서 **DNS 레코드**를 아래처럼 넣습니다.
(현재 `honorlink.li` 는 오렌지웹사이트 서버 `82.221.143.1` 을 가리키고 있으므로 **기존 A 레코드를 지우고** 새로 넣어야 합니다)

**A 레코드 4개** — 호스트는 `@` 또는 비워둠

| 타입 | 호스트 | 값 |
|---|---|---|
| A | @ | `185.199.108.153` |
| A | @ | `185.199.109.153` |
| A | @ | `185.199.110.153` |
| A | @ | `185.199.111.153` |

**AAAA 레코드 4개** (IPv6, 넣으면 좋음)

| 타입 | 호스트 | 값 |
|---|---|---|
| AAAA | @ | `2606:50c0:8000::153` |
| AAAA | @ | `2606:50c0:8001::153` |
| AAAA | @ | `2606:50c0:8002::153` |
| AAAA | @ | `2606:50c0:8003::153` |

**CNAME 레코드 1개** (www 로 들어와도 되게)

| 타입 | 호스트 | 값 |
|---|---|---|
| CNAME | www | `<계정명>.github.io.` |

> 위 IP 는 GitHub 공식 문서에서 확인한 값입니다.
> 나중에 바뀔 수 있으니 문제가 생기면 GitHub Pages 커스텀 도메인 문서를 다시 확인하세요.

### 4. 도메인 연결 확인

DNS 반영은 보통 10분~2시간, 길면 하루가 걸립니다.

저장소 → **Settings → Pages → Custom domain** 에 `honorlink.li` 가 들어가 있고
초록색 체크가 뜨는지 확인합니다.

그 다음 **Enforce HTTPS** 를 반드시 켜세요. (인증서 발급까지 몇 분 더 걸립니다)

### 5. Google Search Console 등록

1. [Search Console](https://search.google.com/search-console) → 속성 추가 → `https://honorlink.li/`
2. 소유권 확인 방법 중 **HTML 태그** 선택
3. 받은 `<meta name="google-site-verification" ...>` 값을 `index.html` 의 주석 처리된 자리에 넣고 주석 해제
   (파일에서 `google-site-verification` 검색)
4. 다시 올리고 확인 누르기
5. 확인되면 **Sitemaps** 메뉴에서 `sitemap.xml` 제출

---

## 배포 후 점검 (한 줄씩 복사해서 실행)

```bash
# 압축이 되고 있는지 (GitHub Pages 는 자동으로 해줍니다)
curl -sI -H 'Accept-Encoding: br, gzip' https://honorlink.li/ | grep -i content-encoding

# http 로 들어가도 https 로 넘어가는지
curl -sI http://honorlink.li/ | head -3

# 문의 페이지가 열리는지
curl -sI https://honorlink.li/contact/ | head -1

# 사이트맵 / robots
curl -s https://honorlink.li/robots.txt
curl -s https://honorlink.li/sitemap.xml
```

---

## 알아두실 것

**GitHub Pages 는 커스텀 HTTP 헤더를 지원하지 않습니다.**
그래서 HSTS 같은 보안 헤더를 직접 넣을 수 없습니다. 대신 이렇게 대응했습니다.

- 보안 정책(CSP)은 HTML 안에 `<meta http-equiv>` 로 넣어 두었습니다.
- http → https 전환은 GitHub 의 **Enforce HTTPS** 가 처리합니다.
- 압축은 GitHub 이 자동으로 합니다 (실측 74% 감소).

HSTS 까지 필요해지면 앞단에 Cloudflare(무료)를 붙이면 됩니다. 지금은 없어도 동작에 문제 없습니다.

---

## 아직 안 만든 것 (없어도 사이트는 정상 동작)

| 파일 | 용도 |
|---|---|
| `images/og.png` | 카톡·메신저에 링크 붙일 때 나오는 썸네일 (1200×630) |
| `images/logo.png` | 구글 지식패널용 로고 (정사각 512px 이상) |
| `images/article-16x9.png` (+ 4x3, 1x1) | 구글 Article 구조화 데이터용. 로고 말고 **실제 제품 화면 캡처**를 쓸 것 |

만들어서 `images/` 폴더에 넣기만 하면 됩니다. HTML 은 이미 이 경로를 가리키고 있습니다.

## 내용 수정 시 주의

- `sitemap.xml` 의 `<lastmod>` 는 **내용을 실제로 고쳤을 때만** 날짜를 바꾸세요.
- 계산기 기본 금액은 `index.html` 안 `SCALE` 객체에 있습니다. 화면에서 사용자가 직접 수정할 수 있는 **예시값**입니다.
- 텔레그램 아이디를 바꾸면 문의 페이지의 **QR 코드도 다시 만들어야** 합니다. QR 안에 주소가 그림으로 박혀 있습니다.
