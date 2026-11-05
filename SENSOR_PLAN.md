# SAW: 추가 센서·계보 수집 및 검증 설계

현재 데이터의 미래 event 평가 불가와 후보의 제한된 유용성은 특정 은닉 원인의 증거가
아니다. 아래는 **mechanism hypothesis / 수집 후보**다. 운전조건·교체 주기는 추천하지 않는다.

## 먼저 확보할 정답과 시간축

- UTC acquisition timestamp, 장비 timestamp, ingestion timestamp, clock offset/동기 오차;
  원래 timezone 보존. 허용 동기 오차는 cut duration과 센서 주기에 따라 계측팀이 결정.
- Tool/Spindle/Blade ID, mounting event, wafer/Lot/chip/die/cut/subgroup ID, subgroup n,
  wafer orientation/cut direction/recipe/material/thickness, BG exit·SAW entry·cut 시작/종료.
- target 측정 원시 반복값, 단위/해상도/교정/재측정/누락 이유, 확정 품질 판정 시각,
  공식 관리한계·rule·version·Phase I 기간. 실제 불량/chipping/kerf와 OOC를 별도 정답으로.
- pre-cut setpoint/확정된 누적 이력과 절단 중/후 actual을 별도 저장. 차기 cut/wafer label은
  같은 Tool/Blade에서 timestamp로 연결하며 reset/maintenance/material 경계를 넘어 lag 금지.
- 기존 전체 데이터 한계를 새 정답으로 삼지 않는다. Phase I만으로 한계를 동결한 후
  독립 미래 구간에 적용. measurement-system analysis / 반복 측정으로 라벨 오류 검토.

## 검증 가설과 대안

| 후보 | 수집 단위·시점 | 검증할 가설 | 대안/교란 | 검증 방법 |
|---|---|---|---|---|
| Spindle run-out | um, 절단 전 mounting/정기 측정; probe 방향·보정 기록 | radial/axial 흔들림이 절단 산포와 관련 | blade 장착·두께·wafer 휨·probe 오차 | 같은 material/recipe에서 반복 mounting 비교, 승인된 정비 개입 전후 및 대조 spindle |
| Vibration | acceleration m/s², spectrum Hz; pre-cut idle와 in-cut 별도 | 특정 대역 에너지가 향후 OOC 증가와 관련 | sensor mounting·속도·기계 공진 | 장비별 baseline, timestamp 정렬, 대역 선택은 train-only; in-cut 값은 next-cut만 사용 |
| Blade ID/usage | blade lot/serial, cuts/meters/time, replacement/dressing event | 누적 사용/드레싱이 위험과 관련 | material·recipe·교체 의사결정에 의한 선택편향 | blade-group holdout, usage reset 확인, material 층화; 90% 임의 교체 기준 기각 |
| Motor current/load | A/%·sampling Hz, idle 및 in-cut actual 구분 | 부하 변화가 다음 절단 위험을 시사 | feed speed·wafer 두께·센서offset | preknown baseline 대비 residual, next-cut horizon; 동시점 진단과 분리 |
| Coolant temperature/flow | °C / L/min, setpoint와 actual, pre-cut·in-cut 및 sensor ID | 냉각 안정성 변화가 품질 위험과 관련 | nozzle blockage·recipe·계측오차 | sensor calibration, flow/temp interaction 사전등록, 안전 범위 승인 뒤 소규모 DOE |
| BG upstream quality | um/count, wafer ID+BG exit timestamp | BG 후 TTV/손상과 SAW OOC의 연관성 | 동일 synthetic 생성·재료 두께·SAW recipe | 실측 provenance/공정 순서 확인 후 lagged linkage, 재료/Tool 조정; ID 일치만으로 인과 금지 |

샘플링 주파수와 신호 압축 창은 장비 대역폭/절단 지속시간/anti-aliasing 확인 후 정한다.
현재 파일로 정확한 Hz·안전 setpoint·센서 tolerances를 추정하지 않는다.

## 평가 설계 및 stop

1. 관측 pilot에서 사건 발생률, episode duration, 관측 누락/센서 교정과 label agreement 조사.
2. 최소 3 Tool·각 여러 Blade·교체/정비 경계 포함, 안정/이상/재료 전환 구간을 수집하는
   pilot 목표. 이 개수는 계획 가정이지 일반화를 보장하는 표본수 기준이 아니다.
3. pilot을 보고 독립 OOC episode 수 기반 power/precision 설계. 예: 200개 독립 사건에서
   Recall=0.8이면 binomial 근사 95%폭은 약 ±0.055; 장비/Blade clustering이 있으면
   더 많은 사건 필요. 현재 1,365 OOC **행**을 독립 사건으로 계산하지 않는다.
4. chronological train/validation/calibration/decision/final test. h=1 wafer와 h=1 cut은
   별도 문제. gap은 max rolling lookback + label/measurement latency를 포함하고 Lot/Wafer
   경계가 split을 가로지르면 whole group을 purge. unseen Tool/Blade 외부 평가도 별도 수행.
5. comparator: prior risk, previous known status persistence, 승인된 SPC rule. event Recall,
   normal-hour false alarm burden, row FPR/review, early-warning lead time와 detection delay,
   miss/right censoring, clustered CI. precision·cost·작업자 capacity 기준 사전 승인.
6. 센서는 관측 연관성이어도 원인으로 확정하지 않음. 승인된 safe-envelope DOE 또는
   개입+대조 증거가 확보되기 전에는 '위험 후보/검증 후보/가설'로 표현.
7. 추가 데이터에서도 사전등록 gate를 못 넘으면 모델 기각 유지. 시험 구간 재튜닝 금지.
