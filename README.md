# codyssey-m1-1

## 데이터 출처

본 저작물은 기상청에서 공공누리 제0유형으로 개방한 「종관기상관측(ASOS) 일자료」와 「관측지점정보」를 이용하였으며,
기상자료개방포털(data.kma.go.kr)에서 무료로 내려받으실 수 있습니다.

| 자료 | 지점 | 기간 | 경로 |
|---|---|---|---|
| 종관기상관측(ASOS) 일자료 | 서울(108), 대구(143), 추풍령(135) | 1960-01-01 ~ 2026-09-13 | 데이터 → 기상관측 → 지상 → 종관기상관측(ASOS) → 자료 |
| 관측지점정보 | 위 3개 지점 | 지점 이력 전체 | 데이터 → 메타데이터 → 지점정보 → 관측지점정보 |

`data/raw/`는 원자료 그대로이며, `data/processed/asos_clean.csv`는 codysseus42가 결측·이상치를 처리해 재가공한 자료입니다.
가공 내용은 `01_preprocess.ipynb`에 기록되어 있습니다. (수집일: 2026-09-15)

## 실행 환경

| 구분 | 버전 | 용도 |
|---|---|---|
| Python | 3.14 | |
| pandas | 3.0.6 | CSV 읽기·통합, 결측 처리, 여름·10년 단위 집계 |
| numpy | | 선형 추세(최소제곱 직선), 지표 계산 |
| matplotlib | 3.11.2 | 모든 그래프 |
| jupyter, ipykernel | 1.1.1 | 노트북 실행 |

표준 라이브러리 `glob`(원본 CSV 파일 목록), `platform`(운영체제별 한글 폰트 선택)을 함께 사용한다.

## 실행 방법

    git clone <저장소 주소>
    cd codyssey-m1-1
    python -m venv .venv
    source .venv/bin/activate        # Windows: .venv\Scripts\activate
    pip install -r requirements.txt
    jupyter notebook

아래 순서로 노트북을 **위에서부터 끝까지** 실행한다 (Kernel → Restart & Run All).

1. `01_preprocess.ipynb` — `data/raw/`의 원본을 통합·검증·정제해 `data/processed/asos_clean.csv`를 만든다.
2. `02_analysis.ipynb` — 정제된 CSV를 읽어 Q1~Q3 분석을 하고, 그림을 `imagedata/`에 저장한다.

`data/processed/asos_clean.csv`가 저장소에 포함되어 있으므로 2번만 단독으로 실행해도 된다.

**한글 폰트:** macOS는 AppleGothic, Windows는 맑은 고딕을 자동으로 쓴다. Linux에서는 나눔고딕을 설치해야 그래프의 한글이 깨지지 않는다.

## 데이터 재사용 — 다른 관측 지점으로 분석하기

1. 기상자료개방포털 → 데이터 → 기상관측 → 지상 → 종관기상관측(ASOS) → 자료 → 일자료에서 원하는 지점을 고른다. 한 번에 최대 10년치까지 받을 수 있다.
2. 받은 파일을 `data/raw/<지점이름>/<지점이름>_<시작연도>.csv` 형식으로 저장한다. 예: `data/raw/busan/busan_1970.csv`
   시작 연도를 네 자리로 쓰면 파일 이름 순서가 시간 순서와 같아져서, `sorted(glob(...))`가 파일을 올바른 순서로 읽는다.
3. `01_preprocess.ipynb`에서 지점별 파일 경로를 새 지점으로 바꿔 실행한다. 원본은 CP949 인코딩이므로 `encoding="cp949"`를 유지해야 한다.
4. 관측지점 이전 이력은 기상자료개방포털 → 메타데이터 → 지점정보 → 관측지점정보에서 확인한다. 분석 기간 중에 관측소가 옮겨졌다면 해석에 반영해야 한다(이 분석에서는 대구 2017년 8월 이전).