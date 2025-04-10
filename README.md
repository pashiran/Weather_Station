# Weather_Station

이 프로젝트는 **기상 데이터를 수집하고 시각화하는 ESP32 기반의 기상 관측소**입니다.  
기상청 API와 에어코리아 API를 활용하여 **실시간 날씨 정보 및 대기오염 데이터를 수집**합니다.  
ESP32-C3 개발 보드를 기본으로 하며, 웹서버를 통해 사용자 입력을 처리하고, API 호출을 통해 필요한 데이터를 정리 및 저장합니다.

---

## 📌 사용 API
### 🌤 기상청 API & 🌬 에어코리아 API

## 🌤 기상청 API 정리

### **단기예보 조회서비스**
기상청 단기예보 조회 서비스는 특정 지역의 초단기 및 단기예보 데이터를 제공한다. 요청 시 특정 날짜, 시간, 좌표값을 입력하면, 해당 위치의 날씨 정보를 반환한다.

| **서비스** | **설명** | **송신 정보 (요청 시 보내는 데이터)** | **응답 정보 (수신 시 받는 데이터)** |
|------------|----------|--------------------------------|--------------------------------|
| **초단기실황조회** (`getUltraSrtNcst`) | 현재 날씨 데이터 조회 (기온, 습도, 강수량 등) | `serviceKey`: 인증키 (필수) <br> `numOfRows`: 한 페이지 결과 수 (기본값 10) <br> `pageNo`: 페이지 번호 (기본값 1) <br> `dataType`: 응답 자료 형식 (XML 또는 JSON, 기본값 XML) <br> `base_date`: 발표일자 (YYYYMMDD, 예: 20210628) <br> `base_time`: 발표시각 (HHMM, 예: 0600, 정시 기준) <br> `nx`: 예보지점 X 좌표 <br> `ny`: 예보지점 Y 좌표 | `baseDate`: 발표일자 <br> `baseTime`: 발표시각 <br> `category`: 자료구분코드 (기온, 풍속 등) <br> `obsrValue`: 실황 값 <br> `nx`: 예보지점 X 좌표 <br> `ny`: 예보지점 Y 좌표 <br> **수신 데이터 항목** <br> `T1H`: 기온 (℃) <br> `RN1`: 1시간 강수량 (mm) <br> `UUU`: 동서 바람 성분 (m/s) <br> `VVV`: 남북 바람 성분 (m/s) <br> `REH`: 습도 (%) <br> `PTY`: 강수형태 (0=없음, 1=비, 2=비/눈, 3=눈, 4=소나기) <br> `VEC`: 풍향 (deg) <br> `WSD`: 풍속 (m/s) |
| **초단기예보조회** (`getUltraSrtFcst`) | 6시간 이내 날씨 예측 | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `base_date`: 발표일자 (YYYYMMDD) <br> `base_time`: 발표시각 (HHMM, 30분 단위) <br> `nx`: 예보지점 X 좌표 <br> `ny`: 예보지점 Y 좌표 | `baseDate`: 발표일자 <br> `baseTime`: 발표시각 <br> `category`: 자료구분코드 <br> `fcstDate`: 예측일자 <br> `fcstTime`: 예측시간 <br> `fcstValue`: 예보 값 <br> **수신 데이터 항목** <br> `TMP`: 기온 (℃) <br> `POP`: 강수확률 (%) <br> `WSD`: 풍속 (m/s) <br> `PTY`: 강수형태 (0=없음, 1=비, 2=비/눈, 3=눈, 4=소나기) <br> `LGT`: 낙뢰 (단위 없음) <br> `VEC`: 풍향 (deg) <br> `UUU`: 동서 바람 성분 (m/s) <br> `VVV`: 남북 바람 성분 (m/s) <br> `REH`: 습도 (%) <br> `WAV`: 파고 (M) |
| **단기예보조회** (`getVilageFcst`) | 3일 이내 시간별 날씨 예측 | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `base_date`: 발표일자 (YYYYMMDD) <br> `base_time`: 발표시각 (HHMM, 정시 기준) <br> `nx`: 예보지점 X 좌표 <br> `ny`: 예보지점 Y 좌표 | `baseDate`, `baseTime`, `category`, `fcstDate`, `fcstTime`, `fcstValue` <br> **수신 데이터 항목** <br> `TMP`: 기온 (℃) <br> `POP`: 강수확률 (%) <br> `WSD`: 풍속 (m/s) <br> `SKY`: 하늘 상태 (1=맑음, 3=구름 많음, 4=흐림) <br> `PTY`: 강수형태 (0=없음, 1=비, 2=비/눈, 3=눈, 4=소나기) <br> `REH`: 습도 (%) <br> `SNO`: 적설량 (cm) <br> `WAV`: 파고 (M) <br> `UUU`: 동서 바람 성분 (m/s) <br> `VVV`: 남북 바람 성분 (m/s) <br> `TMN`: 일 최저기온 (℃) <br> `TMX`: 일 최고기온 (℃) <br> `PCP`: 1시간 강수량 (mm) (강수 없을 시 "강수없음"으로 표기) <br> `SNO`: 1시간 신적설 (cm) (눈이 없을 시 "적설없음"으로 표기) |
| **예보버전조회** (`getFcstVersion`) | 단기예보의 최신 수정 버전 조회 | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `ftype`: 파일구분 (ODAM=초단기실황, VSRT=초단기예보, SHRT=단기예보) <br> `basedatetime`: 발표일시분 (YYYYMMDDHHMM) | `version`: 파일버전 <br> `filetype`: 파일구분 |

---

### **중기예보 조회서비스**
기상청 중기예보 조회 서비스는 3일 이후부터 최대 10일까지의 기온 및 날씨 정보를 제공한다.

| **서비스** | **설명** | **송신 정보 (요청 시 보내는 데이터)** | **응답 정보 (수신 시 받는 데이터)** |
|------------|----------|--------------------------------|--------------------------------|
| **중기전망조회** (`getMidFcst`) | 전국 또는 특정 지점의 기상 전망 (3~10일) | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `stnId`: 지점번호 <br> `tmFc`: 발표시각 (YYYYMMDDHHMM) | `wfSv`: 기상전망 (텍스트 형태) |
| **중기육상예보조회** (`getMidLandFcst`) | 육상 지역의 날씨 (3~10일) | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `regId`: 예보구역코드 <br> `tmFc`: 발표시각 | `rnSt`: 강수확률 (%) <br> `wf`: 날씨 예보 (맑음, 흐림 등) |
| **중기기온조회** (`getMidTa`) | 최고/최저 기온 (3~10일) | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `regId`: 예보구역코드 <br> `tmFc`: 발표시각 | `taMin`: 최저기온 (℃) <br> `taMax`: 최고기온 (℃) |
| **중기해상예보조회** (`getMidSeaFcst`) | 해상 지역(서해, 남해, 동해 등)의 날씨·파고 | `serviceKey`, `numOfRows`, `pageNo`, `dataType` <br> `regId`: 예보구역코드 <br> `tmFc`: 발표시각 | `wf`: 해상날씨 예보 <br> `wh`: 파고 (m) |

---

## 🌬 **에어코리아 API 정리**
한국환경공단 **에어코리아 API**는 **대기오염정보 조회서비스**를 통해 **미세먼지, 초미세먼지, 오존** 등의 정보를 실시간으로 제공한다.

### **대기오염정보 조회서비스**
| **서비스** | **설명** | **송신 정보 (요청 시 보내는 데이터)** | **응답 정보 (수신 시 받는 데이터)** |
|------------|----------|--------------------------------|--------------------------------|
| **시도별 실시간 대기오염 정보 조회** (`getCtprvnRltmMesureDnsty`) | 특정 시도의 미세먼지, 초미세먼지 등 실시간 대기오염 정보 조회 | `serviceKey`: 인증키 <br> `numOfRows`: 한 페이지 결과 수 <br> `pageNo`: 페이지 번호 <br> `returnType`: 응답형식 (XML/JSON) <br> `sidoName`: 시도명 (예: 서울, 부산, 경기) <br> `ver`: API 버전 (기본값 1.0) | `dataTime`: 측정일시 <br> `stationName`: 측정소명 <br> `pm10Value`: 미세먼지 농도 (㎍/㎥) <br> `pm25Value`: 초미세먼지 농도 (㎍/㎥) <br> `o3Value`: 오존 농도 (ppm) <br> `no2Value`: 이산화질소 농도 (ppm) <br> `so2Value`: 아황산가스 농도 (ppm) <br> `coValue`: 일산화탄소 농도 (ppm) |
| **미세먼지 예보 조회** (`getMinuDustFrcstDspth`) | 오늘, 내일, 모레의 미세먼지 예보 제공 | `serviceKey`, `returnType`, `numOfRows`, `pageNo` | `dataTime`: 발표시간 <br> `informData`: 예보일자 <br> `informCause`: 발생 원인 <br> `informOverall`: 전반적 설명 <br> `informGrade`: 지역별 미세먼지 예보 등급 |
| **고농도 초미세먼지 예보 조회** (`getMinuDustFrcstDspth50Over`) | 초미세먼지(PM2.5) 50㎍/㎥ 초과 여부 제공 | `serviceKey`, `returnType`, `numOfRows`, `pageNo` | `dataTime`: 발표 시간 (예: 2024-03-18 05시) <br> `informData`: 예보일자 (예: 2024-03-18) <br> `seoul50Over`: 서울 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `busan50Over`: 부산 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `daegu50Over`: 대구 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `incheon50Over`: 인천 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `gwangju50Over`: 광주 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `daejeon50Over`: 대전 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `ulsan50Over`: 울산 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `gyeonggi50Over`: 경기 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `gangwon50Over`: 강원 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `chungbuk50Over`: 충북 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `chungnam50Over`: 충남 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `jeonbuk50Over`: 전북 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `jeonnam50Over`: 전남 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `gyeongbuk50Over`: 경북 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `gyeongnam50Over`: 경남 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `jeju50Over`: 제주 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> `sejong50Over`: 세종 초미세먼지 50㎍/㎥ 초과 여부 (O/null) <br> **추가 수신 데이터 항목** <br> `informOverall`: 예보 개요 <br> `informCause`: 발생 원인 <br> `actionKnack`: 행동 요령 <br> `imageUrl1`: 예보 이미지 URL 1 <br> `imageUrl2`: 예보 이미지 URL 2 |
| **미세먼지 경보 발령 정보 조회** (`getUlfptcaAlarmInfo`) | PM10/PM2.5에 대한 경보 발령/해제 내역 조회 | `serviceKey`, `returnType`, `numOfRows`, `pageNo`, `year` | `dataDate`: 발령일 <br> `districtName`: 지역명 <br> `itemCode`: 항목명 (PM10, PM2.5) <br> `issueGbn`: 경보 단계 (주의보, 경보) <br> `issueTime`: 발령시간 <br> `issueVal`: 발령 농도 <br> `clearTime`: 해제시간 <br> `clearVal`: 해제 농도 |
| **오존 및 황사 발생 정보 조회** (`getOzAdvsryOccrrncInfo`, `getYlwsndAdvsryOccrrncInfo`) | 특정 연도의 오존 또는 황사 주의보 발령 내역 조회 | `serviceKey`, `returnType`, `numOfRows`, `pageNo`, `year` | `dataDate`: 발령일 <br> `districtName`: 지역명 <br> `issueTime`: 발령시간 <br> `issueVal`: 발령 농도 <br> `clearTime`: 해제시간 <br> `clearVal`: 해제 농도 <br> `issueLvl`: 발령 단계 (1=주의보, 2=경보, 3=중대경보) |

---

## 📌 주요 기능
이 프로젝트에서는 ESP32-C3의 기능을 이용하여 웹서버를 구축하고, 사용자가 **현재 시·군·구·동 정보를 입력**하여 기상청 및 에어코리아 API에서 필요한 데이터를 불러옵니다.  

### 💡 **주요 동작 과정**
1. 웹서버에서 사용자가 **위치 정보를 입력**.
2. ESP32-C3가 입력된 정보를 **API에 맞게 변환**하여 호출.
3. API로부터 받은 기상 및 공기질 데이터를 정리하여 저장.
4. 향후 **디스플레이 출력**을 통해 날씨 및 공기질 정보를 표시할 예정.

---

## 📌 추가 예정 기능 및 차후 개선목표
다음과 같은 기능을 추가하고 개선할 계획입니다. (우선순위에 따른 리스트 아님)

1. **웹서버에 Wi-Fi 리스트 표시**  
   - 사용자가 웹페이지에서 **와이파이를 선택**하고 비밀번호를 입력하면 자동으로 연결되도록 설정.

2. **API 키 입력 기능 추가**  
   - 현재는 API 키를 코드에 직접 입력해야 하지만, 이를 웹서버에서 **사용자가 직접 입력**할 수 있도록 변경.

3. **웹페이지 생성 후 접속하여 위치정보 세팅**  
   - 사용자가 웹페이지를 통해 **위치 정보를 설정**할 수 있도록 기능 추가.

4. **시도정보 및 위치정보 등의 주소 DB 생성**  
   - **시도 및 위치 정보**를 저장하고 관리할 수 있는 데이터베이스 생성.

5. **미세먼지 응답 실행**  
   - **미세먼지 데이터를 수신**하고 처리하는 기능 추가.

6. **디스플레이 면적에 따라 표시정보 정리**  
   - **디스플레이 크기에 맞게** 정보를 정리하여 표시.

7. **예보개황 등을 위해 디스플레이에 한글표시가 가능하도록 연구** (희망사항)  
   - **디스플레이에 한글을 표시**할 수 있는 방법 연구.


---

## 🛠 기여 및 참고
- **기상청 API**: [공공데이터포털 기상청 API](https://www.data.go.kr)  
- **에어코리아 API**: [한국환경공단 에어코리아 API](http://apiweb.airkorea.or.kr)  

**기여자**: ChatGPT, CoPilot
