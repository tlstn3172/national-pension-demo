# 🏦 My Pension Rescue (MPR)

**국민연금 지속가능성 진단 및 시나리오 시뮬레이터**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![React](https://img.shields.io/badge/React-18+-blue.svg)](https://reactjs.org/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

---

## 📖 프로젝트 소개

**My Pension Rescue**는 국민연금의 지속가능성을 진단하고, 다양한 정책 개혁 시나리오를 시뮬레이션할 수 있는 인터랙티브 웹 플랫폼입니다.

### 🎯 핵심 목표

- **데이터 기반 정책 이해**: 복잡한 연금 정책을 시각적으로 쉽게 이해
- **인터랙티브 시뮬레이션**: 보험료율, 소득대체율 등 주요 변수를 직접 조정하며 결과 확인
- **세대 간 형평성 분석**: 정책 변화가 각 세대에 미치는 영향을 객관적으로 분석
- **합리적 논의 촉진**: 감정적 논쟁을 지양하고 데이터 기반의 건설적인 정책 논의 지원

---

## ✨ 주요 기능

### 📊 Current Status Dashboard
현재 법령 기준(보험료율 9%, 소득대체율 40%) 유지 시:
- 기금 잔액 추이 시각화
- 예상 고갈 시점 표시
- 주요 정책 지표 한눈에 확인

### 🔬 Reform Lab Simulator
사용자가 직접 정책 변수를 조정:
- **보험료율**: 9% ~ 15%
- **소득대체율**: 40% ~ 50%
- **수급 연령**: 65세 ~ 70세
- **기금 운용 수익률**: 3% ~ 7%

실시간으로 기금 고갈 시점 변화를 확인하고, 현재 정책과 비교 분석

### 📈 Sustainability Insight (예정)
- 세대별 부담 분석
- 지속가능성 점수 산출
- 최적 개혁안 추천

---

## 🚀 빠른 시작

### 사전 요구사항

- **Node.js**: v18 이상
- **npm** 또는 **yarn**
- **Git**

### 설치 및 실행

```bash
# 저장소 클론
git clone https://github.com/roboco-io/national-pension-demo.git
cd national-pension-demo

# 의존성 설치
npm install

# 개발 서버 실행
npm run dev

# 브라우저에서 http://localhost:5173 접속
```

### 프로덕션 빌드

```bash
# 프로덕션 빌드 생성
npm run build

# 빌드 결과물 미리보기
npm run preview
```

---

## 📁 프로젝트 구조

```
national-pension-demo/
├── docs/                      # 프로젝트 문서
│   ├── PRD.md                # 제품 요구사항 문서
│   ├── TASKS.md              # 개발 태스크 목록
│   ├── Tutorial.md           # 개발 튜토리얼
│   └── ideation.md           # 아이디어 문서
├── src/                       # 소스 코드
│   ├── components/           # React 컴포넌트
│   │   ├── Layout/          # 레이아웃 컴포넌트
│   │   ├── Dashboard/       # 대시보드 컴포넌트
│   │   └── Simulator/       # 시뮬레이터 컴포넌트
│   ├── utils/               # 유틸리티 함수
│   │   ├── calculator.js    # 핵심 계산 로직
│   │   └── dataLoader.js    # 데이터 로더
│   ├── services/            # API 서비스
│   ├── App.jsx              # 메인 앱 컴포넌트
│   └── main.jsx             # 진입점
├── data/                     # 데이터 파일
│   ├── population.csv       # 인구 통계 데이터
│   ├── pension-fund.csv     # 기금 추이 데이터
│   └── economic-indicators.csv
├── tests/                    # 테스트 코드
├── public/                   # 정적 파일
├── .gitignore
├── package.json
├── vite.config.js
└── README.md                # 본 문서
```

---

## 🛠️ 기술 스택

### Frontend
- **React 18+**: UI 프레임워크
- **Vite**: 빌드 도구
- **Recharts**: 데이터 시각화
- **Material-UI**: UI 컴포넌트 라이브러리

### Data & Utils
- **PapaParse**: CSV 파싱
- **Lodash**: 유틸리티 함수
- **date-fns**: 날짜 처리

### Development
- **ESLint**: 코드 품질 관리
- **Prettier**: 코드 포맷팅
- **Jest**: 테스트 프레임워크

---

## 📊 데이터 출처

본 프로젝트는 다음의 공식 데이터를 활용합니다:

- **통계청 KOSIS**: 장래인구추계, 경제활동인구조사
- **국민연금공단**: 기금 운용 현황, 가입자/수급자 통계
- **기획재정부**: 경제 지표 및 재정 전망

> **참고**: 현재 버전은 샘플 데이터를 사용하며, 향후 실시간 API 연동 예정

---

## 🗺️ 로드맵

### ✅ Phase 1: MVP (완료 예정)
- [x] 프로젝트 기획 및 문서화
- [ ] 기본 대시보드 구현
- [ ] 4가지 변수 시뮬레이터
- [ ] 기금 잔액 추이 시각화

### 🔄 Phase 2: 고급 기능 (진행 예정)
- [ ] 세대 간 부담 리포트
- [ ] 신규 재원 시나리오 (로봇세, 부가가치세 등)
- [ ] 실시간 데이터 연동 (KOSIS API)

### 📅 Phase 3: 커뮤니티 기능
- [ ] 사용자 시나리오 저장 및 공유
- [ ] 최적 개혁안 투표
- [ ] 전문가 의견 및 해설

### 🚀 Phase 4: 고도화
- [ ] AI 기반 최적 시나리오 추천
- [ ] 개인 맞춤형 연금 설계 도구
- [ ] 다국어 지원 (영어, 중국어)
- [ ] 모바일 앱

---

## 🤝 기여하기

프로젝트에 기여해주셔서 감사합니다! 다음 방법으로 기여할 수 있습니다:

### 버그 리포트 및 기능 제안
- [GitHub Issues](https://github.com/roboco-io/national-pension-demo/issues)에 등록해주세요

### Pull Request
1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### 코드 스타일
- ESLint 규칙을 준수해주세요
- Prettier로 코드를 포맷팅해주세요
- 의미 있는 커밋 메시지를 작성해주세요 (Conventional Commits)

---

## 📚 문서

더 자세한 정보는 다음 문서를 참고하세요:

- **[PRD.md](docs/PRD.md)**: 제품 요구사항 문서 - 프로젝트의 목표, 기능, 기술 스펙
- **[TASKS.md](docs/TASKS.md)**: 개발 태스크 목록 - 단계별 개발 체크리스트
- **[Tutorial.md](docs/Tutorial.md)**: 개발 튜토리얼 - 실전 개발 가이드 및 코드 예제
- **[ideation.md](docs/ideation.md)**: 아이디어 문서 - 프로젝트 기획 배경

---

## 🧪 테스트

```bash
# 전체 테스트 실행
npm test

# 테스트 커버리지 확인
npm run test:coverage

# 특정 테스트 파일 실행
npm test calculator.test.js
```

---

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

---

## 👥 제작자

**프로젝트 기획 및 개발**
- 경영전략 기획 역량 포트폴리오 프로젝트

---

## 🙏 감사의 말

- **통계청**: 인구 통계 데이터 제공
- **국민연금공단**: 기금 운용 데이터 제공
- **React 커뮤니티**: 훌륭한 라이브러리와 도구들

---

## 📞 문의

프로젝트에 대한 질문이나 제안사항이 있으시면:

- **GitHub Issues**: [이슈 등록](https://github.com/roboco-io/national-pension-demo/issues)
- **Email**: [문의하기](mailto:contact@example.com)

---

## 🌟 Star History

이 프로젝트가 유용하다면 ⭐️ Star를 눌러주세요!

---

<div align="center">

**Made with ❤️ for better pension policy discussions**

[Documentation](docs/) · [Report Bug](https://github.com/roboco-io/national-pension-demo/issues) · [Request Feature](https://github.com/roboco-io/national-pension-demo/issues)

</div>
