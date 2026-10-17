# SignMate

MediaPipe 기반 기초 수어 학습 및 실시간 손동작 인식 웹앱(PWA)

부산 해커톤 출품작 · 8시간 · 팀 프로젝트 · 대학총장상 수상 (12팀 참가)

**담당 파트: 수어 인식 ML 파이프라인** — 데이터 수집 → 모델 학습 → 브라우저 추론
관련 디렉터리: [`ml/`](ml/), [`mediapipe0.1/`](mediapipe0.1/)

## 구성

| 디렉터리 | 내용 |
|---|---|
| `src/`, `public/` | React + TypeScript 프런트엔드 (학습·인식·퀴즈 화면) |
| `ml/` | Python 학습 파이프라인 — 특징 추출, MLP 학습, 웹 자산 내보내기 |
| `mediapipe0.1/` | 프런트 통합용 번들 — 모델 JSON + 브라우저 추론 엔진 |

## 수어 인식 동작 방식

- MediaPipe Hands로 양손 21개 랜드마크를 검출하고 손목 기준 상대좌표로 정규화합니다.
- 특징 벡터는 `21 × 3 × 2 = 126`차원으로 고정하며, 보이지 않는 손은 0으로 채웁니다.
- 프레임 단위 MLP(126 → 256 → 128 → 64 → 11)로 11개 라벨을 분류합니다.
- 학습된 Keras 모델을 레이어 가중치 JSON으로 내보내, 브라우저에서 별도 ML 런타임 없이 추론합니다.
- 최근 프레임들의 예측 확률을 평균하고 신뢰도 임계값을 적용해, 확신이 낮으면 결과를 내지 않습니다.

자세한 내용은 [`ml/README.md`](ml/README.md)와 [`mediapipe0.1/README.md`](mediapipe0.1/README.md)에 있습니다.

## 한계

프레임 단위 모델이므로 손 모양은 구분하지만 **동작의 시간 흐름은 반영하지 못합니다.**
움직임 자체가 의미를 갖는 수어는 인식 대상에서 제외했습니다.
시퀀스 모델(LSTM/GRU)로의 확장 지점은 `ml/train.py`에 주석으로 남겨 두었습니다.

학습 데이터(CSV)와 Keras 원본 모델은 저장소에 포함하지 않았습니다.
브라우저 데모는 내보낸 `mediapipe0.1/assets/signmate_model.json`으로 동작합니다.

## 실행

프런트엔드:

```bash
npm install
npm run dev
```

인식 데모만 확인:

```bash
python3 -m http.server 8000
# http://localhost:8000/mediapipe0.1/demo/
```

## 기술 스택

React · TypeScript · Vite · Tailwind CSS · PWA
Python · TensorFlow/Keras · MediaPipe · OpenCV · scikit-learn
