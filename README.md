# 한국투자증권 Open Trading API 및 Postman API 플랫폼을 사용한 국내주식 모의투자

---

## 목차

1. [사전 준비](#사전-준비)
2. [인증](#인증)
3. [[국내주식] 기본시세](#국내주식-기본시세)
4. [[국내주식] 주문/계좌](#국내주식-주문계좌)

---

## 1. 사전 준비

### a. 한국투자증권 Open API 신청

[한국투자증권 Open API 포털](https://apiportal.koreainvestment.com)에서 모의투자 앱 키를 발급받습니다.

### b. Postman 환경 변수 설정

컬렉션 실행 전, `모의Env` 환경에서 아래 변수들을 설정합니다.

| 변수명 | 설명 | 예시 |
|---|---|---|
| `VTS` | 모의투자 API Base URL | `https://openapivts.koreainvestment.com:29443` |
| `VTS_APPKEY` | 모의투자 앱키 (App Key) | `계정 설정에서 확인` |
| `VTS_APPSECRET` | 모의투자 앱 시크릿 (App Secret) | `계정 설정에서 확인` |
| `VTS_TOKEN` | OAuth 액세스 토큰 | `자동 발급` |
| `VTS_HASH` | HashKey (POST 요청 시 필요) | `자동 발급` |
| `CANO` | 모의투자 계좌번호 앞 8자리 | `계정 설정에서 확인` |
| `CANO_T` | 계좌 상품 코드 (Account Product Code) | `01` |

> **보안 주의점:** `VTS_APPKEY` 및 `VTS_APPSECRET`은 Postman의 **Secret** 타입 변수로 저장하여 노출을 방지합니다.

> **CANO_T:** `01`은 모의투자 계좌를 의미합니다.

---

## 2. 인증

국내주식 API를 호출하기 전에 **OAuth 토큰을 먼저 발급**받습니다.

### 토큰 발급 절차

1. 컬렉션 내 **[OAuth]** 폴더의 토큰 발급 요청을 실행
2. 발급된 `access_token`이 `VTS_TOKEN` 환경 변수에 자동으로 저장
3. 이후 모든 국내주식 API 요청에 해당 토큰이 자동으로 사용됨

```
POST {{VTS}}/oauth2/tokenP
Content-Type: application/json

{
  "grant_type": "client_credentials",
  "appkey": "{{VTS_APPKEY}}",
  "appsecret": "{{VTS_APPSECRET}}"
}
```

> 토큰 유효 기간은 **24시간**으로, 만료 시 재발급이 필요합니다.

### HashKey 발급 (POST 요청 시 필요)

주문 API 등 POST 요청을 사용할 때는 `VTS_HASH` 값도 필요하기 때문에,
**[OAuth]** 폴더의 HashKey 발급 요청을 실행하여 `VTS_HASH`를 설정합니다.

---

## [국내주식] 기본시세

주식 현재가, 체결 내역, 호가, 투자자 동향 등 다양한 시세 데이터를 조회하는 API 모음입니다.  
모든 요청은 `GET` 방식이며, `{{VTS}}`를 Base URL로 사용합니다.

### 공통 Query Parameter

| 파라미터 | 설명 | 예시 |
|---|---|---|
| `fid_cond_mrkt_div_code` | 시장 구분 코드 | `J` (주식), `U` (업종), `W` (ELW) |
| `fid_input_iscd` | 종목 코드 | `005930` (삼성전자) |

---

### API 목록

| # | 요청 이름 | Method | Endpoint | 설명 |
|---|---|---|---|---|
| 1 | V_주식현재가 시세 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-price` | 특정 종목의 현재가 시세 조회 |
| 2 | V_주식현재가 체결(최근30건) | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-ccnl` | 최근 30건 체결 내역 조회 |
| 3 | V_주식현재가 일자별 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-daily-price` | 일자별 주가 조회 |
| 4 | V_주식현재가 호가 예상체결 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-asking-price-exp-ccn` | 호가 및 예상 체결가 조회 |
| 5 | V_주식현재가 투자자 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-investor` | 투자자별 매매동향 조회 |
| 6 | V_주식현재가 회원사 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-member` | 회원사별 매매동향 조회 |
| 7 | V_주식현재가 당일시간대별체결 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-time-itemconclusion` | 당일 시간대별 체결 조회 |
| 8 | V_국내주식기간별시세(일/주/월/년) | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-daily-itemchartprice` | 기간별 시세 (일/주/월/년) 조회 |
| 9 | V_주식현재가 시간외 일자별주가 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-daily-overtimeprice` | 시간외 일자별 주가 조회 |
| 10 | V_주식현재가 시간외 시간별체결 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-time-overtimeconclusion` | 시간외 시간별 체결 조회 |
| 11 | V_국내주식업종기간별시세(일/주/월/년) | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-daily-indexchartprice` | 업종 기간별 시세 조회 |
| 12 | V_주식당일분봉조회 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-time-itemchartprice` | 당일 분봉(Minute Candle) 데이터 조회 |
| 13 | V_주식현재가 ELW현재가 시세 | `GET` | `/uapi/domestic-stock/v1/quotations/inquire-elw-price` | ELW 현재가 시세 조회 |

---

### 상세 설명

#### 1. V_주식현재가 시세
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-price
```
특정 종목의 현재가 및 등락률, 거래량 등 기본 시세 정보를 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `fid_cond_mrkt_div_code` | `J` | 주식 시장 구분 |
| `fid_input_iscd` | `005930` | 종목 코드 (예: 삼성전자) |

---

#### 2. V_주식현재가 체결(최근30건)
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-ccnl
```
해당 종목의 최근 30건 체결 내역(체결 시간, 체결가, 체결량)을 조회합니다.

---

#### 3. V_주식현재가 일자별
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-daily-price
```
일자별 주가(시가, 고가, 저가, 종가, 거래량)를 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `fid_period_div_code` | `D` | 기간 구분 (D: 일, W: 주, M: 월) |
| `fid_org_adj_prc` | `1` | 수정주가 여부 (0: 수정주가, 1: 원주가) |

---

#### 4. V_주식현재가 호가 예상체결
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-asking-price-exp-ccn
```
매수/매도 호가 10단계 및 예상 체결가를 조회합니다.

---

#### 5. V_주식현재가 투자자
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-investor
```
개인, 외국인, 기관 등 투자자 유형별 매매동향을 조회합니다.

---

#### 6. V_주식현재가 회원사
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-member
```
증권사(회원사)별 매수/매도 동향을 조회합니다.

---

#### 7. V_주식현재가 당일시간대별체결
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-time-itemconclusion
```
당일 특정 시간 이후의 시간대별 체결 데이터를 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `fid_input_hour_1` | `155000` | 조회 시작 시간 (HHMMSS 형식, 예: 오후 3시 50분) |

---

#### 8. V_국내주식기간별시세(일/주/월/년)
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-daily-itemchartprice
```
지정한 기간 동안의 일/주/월/년 단위 시세 데이터를 조회합니다. 차트 데이터 구성에 활용할 수 있습니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `fid_input_date_1` | `20240101` | 조회 시작일 (YYYYMMDD) |
| `fid_input_date_2` | `20241231` | 조회 종료일 (YYYYMMDD) |
| `fid_period_div_code` | `D` | 기간 구분 (D: 일, W: 주, M: 월, Y: 년) |

---

#### 9. V_주식현재가 시간외 일자별주가
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-daily-overtimeprice
```
시간외 거래(장 전/후)의 일자별 주가를 조회합니다.

---

#### 10. V_주식현재가 시간외 시간별체결
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-time-overtimeconclusion
```
시간외 거래의 시간별 체결 내역을 조회합니다.

---

#### 11. V_국내주식업종기간별시세(일/주/월/년)
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-daily-indexchartprice
```
KOSPI, KOSDAQ 등 업종 지수의 기간별 시세를 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `fid_cond_mrkt_div_code` | `U` | 업종 시장 구분 |
| `fid_input_iscd` | `0001` | 업종 코드 (0001: KOSPI, 1001: KOSDAQ) |

---

#### 12. V_주식당일분봉조회
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-time-itemchartprice
```
당일 분봉(Minute Candle) 데이터를 조회합니다. 단기 차트 분석에 활용할 수 있습니다.

---

#### 13. V_주식현재가 ELW현재가 시세
```
GET {{VTS}}/uapi/domestic-stock/v1/quotations/inquire-elw-price
```
ELW(Equity Linked Warrant) 종목의 현재가 시세를 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `fid_cond_mrkt_div_code` | `W` | ELW 시장 구분 |

---

## [국내주식] 주문/계좌

주식 주문 제출, 정정/취소, 잔고 및 체결 내역 조회 등 계좌 관련 API 모음입니다.  
**POST 요청**은 `VTS_HASH` 값이 필요하며, 모든 요청에 `VTS_TOKEN` 인증이 필요합니다.

### API 목록

| # | 요청 이름 | Method | Endpoint | 설명 |
|---|---|---|---|---|
| 1 | V_주식주문(현금) | `POST` | `/uapi/domestic-stock/v1/trading/order-cash` | 현금 주식 매수/매도 주문 |
| 2 | V_주식주문(정정취소) | `POST` | `/uapi/domestic-stock/v1/trading/order-rvsecncl` | 기존 주문 정정 또는 취소 |
| 3 | V_매수가능조회 | `GET` | `/uapi/domestic-stock/v1/trading/inquire-psbl-order` | 매수 가능 금액 조회 |
| 4 | V_주식잔고조회 | `GET` | `/uapi/domestic-stock/v1/trading/inquire-balance` | 주식 잔고 조회 |
| 5 | V_주식일별주문체결조회 | `GET` | `/uapi/domestic-stock/v1/trading/inquire-daily-ccld` | 일별 주문 체결 내역 조회 |

---

### 상세 설명

#### 1. V_주식주문(현금)
```
POST {{VTS}}/uapi/domestic-stock/v1/trading/order-cash
```
현금으로 주식을 매수하거나 매도하는 주문을 제출합니다.

**Request Body 예시 (매수):**
```json
{
  "CANO": "{{CANO}}",
  "ACNT_PRDT_CD": "{{CANO_T}}",
  "PDNO": "005930",
  "ORD_DVSN": "00",
  "ORD_QTY": "10",
  "ORD_UNPR": "75000"
}
```

| 필드 | 설명 | 예시 |
|---|---|---|
| `CANO` | 계좌번호 앞 8자리 | `{{CANO}}` |
| `ACNT_PRDT_CD` | 계좌 상품 코드 | `{{CANO_T}}` |
| `PDNO` | 종목 코드 | `005930` |
| `ORD_DVSN` | 주문 구분 (00: 지정가, 01: 시장가) | `00` |
| `ORD_QTY` | 주문 수량 | `10` |
| `ORD_UNPR` | 주문 단가 (시장가 주문 시 `0`) | `75000` |

> **tr_id 헤더:** 매수 시 `VTTC0802U`, 매도 시 `VTTC0801U`를 사용합니다.

---

#### 2. V_주식주문(정정취소)
```
POST {{VTS}}/uapi/domestic-stock/v1/trading/order-rvsecncl
```
이미 제출된 주문을 정정하거나 취소합니다.

**Request Body 예시 (취소):**
```json
{
  "CANO": "{{CANO}}",
  "ACNT_PRDT_CD": "{{CANO_T}}",
  "KRX_FWDG_ORD_ORGNO": "",
  "ORGN_ODNO": "0000123456",
  "ORD_DVSN": "00",
  "RVSE_CNCL_DVSN_CD": "02",
  "ORD_QTY": "0",
  "ORD_UNPR": "0",
  "QTY_ALL_ORD_YN": "Y"
}
```

| 필드 | 설명 | 예시 |
|---|---|---|
| `ORGN_ODNO` | 원주문 번호 | `0000123456` |
| `RVSE_CNCL_DVSN_CD` | 정정/취소 구분 (01: 정정, 02: 취소) | `02` |
| `QTY_ALL_ORD_YN` | 전량 주문 여부 | `Y` |

> **tr_id 헤더:** `VTTC0803U`를 사용합니다.

---

#### 3. V_매수가능조회
```
GET {{VTS}}/uapi/domestic-stock/v1/trading/inquire-psbl-order
```
특정 종목을 특정 가격에 매수할 수 있는 최대 수량 및 가능 금액을 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `CANO` | `{{CANO}}` | 계좌번호 앞 8자리 |
| `ACNT_PRDT_CD` | `{{CANO_T}}` | 계좌 상품 코드 |
| `PDNO` | `005930` | 종목 코드 |
| `ORD_UNPR` | `55000` | 주문 단가 |
| `ORD_DVSN` | `00` | 주문 구분 |
| `CMA_EVLU_AMT_ICLD_YN` | `Y` | CMA 평가금액 포함 여부 |
| `OVRS_ICLD_YN` | `N` | 해외 포함 여부 |

---

#### 4. V_주식잔고조회
```
GET {{VTS}}/uapi/domestic-stock/v1/trading/inquire-balance
```
현재 보유 중인 주식 잔고 및 평가 손익을 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `CANO` | `{{CANO}}` | 계좌번호 앞 8자리 |
| `ACNT_PRDT_CD` | `{{CANO_T}}` | 계좌 상품 코드 |
| `AFHR_FLPR_YN` | `N` | 시간외 단일가 여부 |
| `INQR_DVSN` | `02` | 조회 구분 |
| `UNPR_DVSN` | `01` | 단가 구분 |
| `FUND_STTL_ICLD_YN` | `N` | 펀드 결제 포함 여부 |
| `FNCG_AMT_AUTO_RDPT_YN` | `N` | 융자금액 자동 상환 여부 |
| `PRCS_DVSN` | `00` | 처리 구분 |

---

#### 5. V_주식일별주문체결조회
```
GET {{VTS}}/uapi/domestic-stock/v1/trading/inquire-daily-ccld
```
지정한 기간 동안의 일별 주문 체결 내역을 조회합니다.

**주요 Query Parameters:**
| 파라미터 | 값 | 설명 |
|---|---|---|
| `CANO` | `{{CANO}}` | 계좌번호 앞 8자리 |
| `ACNT_PRDT_CD` | `{{CANO_T}}` | 계좌 상품 코드 |
| `INQR_STRT_DT` | `20240101` | 조회 시작일 (YYYYMMDD) |
| `INQR_END_DT` | `20241231` | 조회 종료일 (YYYYMMDD) |
| `SLL_BUY_DVSN_CD` | `00` | 매도/매수 구분 (00: 전체, 01: 매도, 02: 매수) |
| `INQR_DVSN` | `00` | 조회 구분 |
| `PDNO` | _(선택)_ | 특정 종목 코드 필터 |
| `CTX_AREA_FK100` | _(페이징)_ | 연속 조회 키 |
| `CTX_AREA_NK100` | _(페이징)_ | 연속 조회 키 |

---
