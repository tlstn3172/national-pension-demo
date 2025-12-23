# Tutorial: 국민연금 시뮬레이터 개발 가이드

**프로젝트:** My Pension Rescue (MPR)  
**난이도:** 중급 ~ 고급  
**예상 소요 시간:** Phase 1 기준 1-2개월

---

## 📚 목차

1. [시작하기 전에](#시작하기-전에)
2. [Phase 1: MVP 구축](#phase-1-mvp-구축)
3. [Phase 2: 고급 기능](#phase-2-고급-기능)
4. [Phase 3: 커뮤니티 기능](#phase-3-커뮤니티-기능)
5. [Phase 4: 고도화](#phase-4-고도화)
6. [문제 해결](#문제-해결)
7. [참고 자료](#참고-자료)

---

## 시작하기 전에

### 필수 사전 지식

- **JavaScript/TypeScript:** ES6+ 문법, 비동기 프로그래밍
- **React:** 컴포넌트, Hooks, 상태 관리
- **데이터 시각화:** 차트 라이브러리 기본 사용법
- **Git/GitHub:** 버전 관리 기초

### 개발 환경 요구사항

- **Node.js:** v18 이상
- **npm 또는 yarn:** 최신 버전
- **코드 에디터:** VS Code 권장
- **Git:** 버전 관리용

### 추천 VS Code 확장

```
- ESLint
- Prettier
- ES7+ React/Redux/React-Native snippets
- GitLens
- Thunder Client (API 테스트용)
```

---

## Phase 1: MVP 구축

### Step 1: 프로젝트 초기 설정

#### 1.1 프로젝트 디렉토리 생성

```bash
# 프로젝트 루트 디렉토리로 이동
cd c:\Users\WIN\Desktop\newproject1\national-pension-demo

# 기본 디렉토리 구조 생성
mkdir src data tests docs
mkdir src\components src\utils src\services
```

#### 1.2 Git 저장소 초기화

```bash
# Git 초기화
git init

# .gitignore 파일 생성
echo "node_modules/" > .gitignore
echo "build/" >> .gitignore
echo "dist/" >> .gitignore
echo ".env" >> .gitignore
echo ".DS_Store" >> .gitignore
```

#### 1.3 React 프로젝트 생성

**옵션 A: Create React App 사용 (권장 - 초보자용)**

```bash
# 현재 디렉토리에 React 앱 생성
npx create-react-app .

# 또는 새 디렉토리에 생성 후 파일 이동
npx create-react-app my-pension-rescue
# 생성된 파일들을 현재 디렉토리로 이동
```

**옵션 B: Vite 사용 (권장 - 빠른 개발)**

```bash
# Vite로 React 프로젝트 생성
npm create vite@latest . -- --template react

# 의존성 설치
npm install
```

**옵션 C: Streamlit 사용 (Python 기반 - 빠른 프로토타이핑)**

```bash
# Python 가상환경 생성
python -m venv venv

# 가상환경 활성화 (Windows)
venv\Scripts\activate

# Streamlit 설치
pip install streamlit pandas plotly
```

> **💡 추천:** 이 튜토리얼은 React + Vite 기준으로 진행합니다.

#### 1.4 필수 라이브러리 설치

```bash
# 차트 라이브러리
npm install recharts

# UI 프레임워크
npm install @mui/material @emotion/react @emotion/styled

# 유틸리티
npm install lodash date-fns

# 개발 도구
npm install -D eslint prettier
```

#### 1.5 프로젝트 구조 확인

```
national-pension-demo/
├── docs/
│   ├── PRD.md
│   ├── TASKS.md
│   └── Tutorial.md (현재 파일)
├── src/
│   ├── components/
│   ├── utils/
│   ├── services/
│   ├── App.jsx
│   └── main.jsx
├── data/
├── tests/
├── package.json
└── vite.config.js
```

---

### Step 2: 데이터 수집 및 준비

#### 2.1 데이터 소스 조사

**통계청 KOSIS 데이터 접근**

1. [KOSIS 웹사이트](https://kosis.kr/) 방문
2. 검색: "장래인구추계"
3. 필요한 데이터:
   - 연도별 인구 추계 (2025~2070)
   - 연령대별 인구 분포
   - 출생률, 사망률

**국민연금공단 데이터**

1. [국민연금공단 공시실](https://www.nps.or.kr/) 방문
2. 필요한 데이터:
   - 기금 운용 현황
   - 가입자 수 추이
   - 수급자 수 추이
   - 평균 수급액

#### 2.2 샘플 데이터 생성

실제 데이터 수집 전, 개발용 샘플 데이터를 생성합니다.

**`data/population.csv` 생성**

```csv
year,total_population,working_age_population,elderly_population
2025,51500000,37000000,8500000
2030,51200000,35500000,10200000
2035,50800000,33800000,12100000
2040,50200000,31900000,14200000
2045,49400000,29800000,16100000
2050,48500000,27600000,17900000
2055,47400000,25400000,19500000
2060,46100000,23200000,20900000
2065,44700000,21100000,22100000
2070,43200000,19200000,23000000
```

**`data/pension-fund.csv` 생성**

```csv
year,fund_balance,premium_income,benefit_payment,investment_return
2023,950000000000000,45000000000000,35000000000000,5.2
2024,980000000000000,47000000000000,37000000000000,5.1
2025,1010000000000000,49000000000000,39000000000000,5.0
```

#### 2.3 데이터 로더 유틸리티 작성

**`src/utils/dataLoader.js` 생성**

```javascript
import populationData from '../../data/population.csv';
import pensionFundData from '../../data/pension-fund.csv';

/**
 * CSV 데이터를 파싱하여 객체 배열로 반환
 */
export const loadPopulationData = async () => {
  try {
    // CSV 파싱 로직 (csv-parser 또는 papaparse 사용)
    const response = await fetch(populationData);
    const text = await response.text();
    
    // 간단한 CSV 파싱
    const lines = text.split('\n');
    const headers = lines[0].split(',');
    
    return lines.slice(1).map(line => {
      const values = line.split(',');
      return headers.reduce((obj, header, index) => {
        obj[header.trim()] = values[index]?.trim();
        return obj;
      }, {});
    });
  } catch (error) {
    console.error('Error loading population data:', error);
    return [];
  }
};

export const loadPensionFundData = async () => {
  // 위와 유사한 로직
};
```

**더 나은 방법: papaparse 사용**

```bash
npm install papaparse
```

```javascript
import Papa from 'papaparse';

export const loadCSV = async (filePath) => {
  const response = await fetch(filePath);
  const text = await response.text();
  
  return new Promise((resolve, reject) => {
    Papa.parse(text, {
      header: true,
      dynamicTyping: true,
      complete: (results) => resolve(results.data),
      error: (error) => reject(error)
    });
  });
};
```

---

### Step 3: 핵심 계산 로직 구현

#### 3.1 기금 잔액 계산 공식 설계

**수리 모델 정의**

```
기금 잔액(t+1) = 기금 잔액(t) × (1 + 수익률) + 보험료 수입(t) - 연금 지급액(t)

보험료 수입(t) = 가입자 수(t) × 평균 소득(t) × 보험료율

연금 지급액(t) = 수급자 수(t) × 평균 소득(t-n) × 소득대체율
```

#### 3.2 계산 유틸리티 구현

**`src/utils/calculator.js` 생성**

```javascript
/**
 * 국민연금 기금 시뮬레이션 계산기
 */

// 기본 상수
const DEFAULT_PARAMS = {
  premiumRate: 0.09,        // 보험료율 9%
  replacementRate: 0.40,    // 소득대체율 40%
  retirementAge: 65,        // 수급 연령 65세
  returnRate: 0.05,         // 기금 운용 수익률 5%
  averageIncome: 3000000,   // 평균 소득 300만원
};

/**
 * 연도별 기금 잔액 계산
 * @param {Object} params - 시뮬레이션 파라미터
 * @param {Array} populationData - 인구 데이터
 * @param {number} startYear - 시작 연도
 * @param {number} endYear - 종료 연도
 * @returns {Array} 연도별 기금 잔액 배열
 */
export const calculateFundBalance = (
  params = DEFAULT_PARAMS,
  populationData,
  startYear = 2025,
  endYear = 2070
) => {
  const results = [];
  let currentBalance = 1010000000000000; // 2025년 기준 약 1010조원
  
  for (let year = startYear; year <= endYear; year++) {
    const population = populationData.find(p => p.year === year);
    
    if (!population) continue;
    
    // 가입자 수 (생산가능인구의 약 70%)
    const subscribers = population.working_age_population * 0.7;
    
    // 수급자 수 (노인인구의 약 60%)
    const beneficiaries = population.elderly_population * 0.6;
    
    // 보험료 수입
    const premiumIncome = subscribers * params.averageIncome * 12 * params.premiumRate;
    
    // 연금 지급액
    const benefitPayment = beneficiaries * params.averageIncome * 12 * params.replacementRate;
    
    // 투자 수익
    const investmentReturn = currentBalance * params.returnRate;
    
    // 다음 연도 기금 잔액
    currentBalance = currentBalance + investmentReturn + premiumIncome - benefitPayment;
    
    results.push({
      year,
      balance: currentBalance,
      premiumIncome,
      benefitPayment,
      investmentReturn,
      subscribers,
      beneficiaries,
      isDepleted: currentBalance <= 0
    });
    
    // 기금 고갈 시 중단
    if (currentBalance <= 0) break;
  }
  
  return results;
};

/**
 * 기금 고갈 연도 찾기
 * @param {Array} results - calculateFundBalance 결과
 * @returns {number|null} 고갈 연도 또는 null
 */
export const findDepletionYear = (results) => {
  const depleted = results.find(r => r.isDepleted);
  return depleted ? depleted.year : null;
};

/**
 * 개인별 예상 수급액 계산
 * @param {number} birthYear - 출생 연도
 * @param {number} averageIncome - 평균 소득
 * @param {Object} params - 시뮬레이션 파라미터
 * @returns {Object} 예상 수급액 정보
 */
export const calculatePersonalBenefit = (birthYear, averageIncome, params = DEFAULT_PARAMS) => {
  const retirementYear = birthYear + params.retirementAge;
  const monthlyBenefit = averageIncome * params.replacementRate;
  const annualBenefit = monthlyBenefit * 12;
  
  return {
    retirementYear,
    monthlyBenefit,
    annualBenefit,
    replacementRate: params.replacementRate
  };
};

/**
 * 개인별 보험료 부담 계산
 * @param {number} currentAge - 현재 나이
 * @param {number} monthlyIncome - 월 소득
 * @param {Object} params - 시뮬레이션 파라미터
 * @returns {Object} 보험료 부담 정보
 */
export const calculatePersonalPremium = (currentAge, monthlyIncome, params = DEFAULT_PARAMS) => {
  const monthlyPremium = monthlyIncome * params.premiumRate;
  const annualPremium = monthlyPremium * 12;
  
  // 65세까지 납부한다고 가정
  const yearsUntilRetirement = Math.max(0, 65 - currentAge);
  const totalPremium = annualPremium * yearsUntilRetirement;
  
  return {
    monthlyPremium,
    annualPremium,
    totalPremium,
    yearsUntilRetirement
  };
};

export default {
  calculateFundBalance,
  findDepletionYear,
  calculatePersonalBenefit,
  calculatePersonalPremium,
  DEFAULT_PARAMS
};
```

#### 3.3 계산 로직 테스트

**`tests/calculator.test.js` 생성**

```javascript
import { calculateFundBalance, findDepletionYear } from '../src/utils/calculator';

describe('Calculator Utils', () => {
  const mockPopulationData = [
    { year: 2025, working_age_population: 37000000, elderly_population: 8500000 },
    { year: 2026, working_age_population: 36800000, elderly_population: 8800000 },
  ];
  
  test('calculateFundBalance should return array of results', () => {
    const results = calculateFundBalance({}, mockPopulationData, 2025, 2026);
    expect(results).toHaveLength(2);
    expect(results[0]).toHaveProperty('balance');
    expect(results[0]).toHaveProperty('year');
  });
  
  test('findDepletionYear should return null if fund is sustainable', () => {
    const results = [
      { year: 2025, balance: 1000, isDepleted: false },
      { year: 2026, balance: 1100, isDepleted: false },
    ];
    expect(findDepletionYear(results)).toBeNull();
  });
  
  test('findDepletionYear should return year if fund is depleted', () => {
    const results = [
      { year: 2025, balance: 1000, isDepleted: false },
      { year: 2026, balance: -100, isDepleted: true },
    ];
    expect(findDepletionYear(results)).toBe(2026);
  });
});
```

**테스트 실행**

```bash
# Jest 설치
npm install -D jest @testing-library/react @testing-library/jest-dom

# 테스트 실행
npm test
```

---

### Step 4: UI 컴포넌트 개발

#### 4.1 공통 컴포넌트 - Layout

**`src/components/Layout/Header.jsx` 생성**

```jsx
import React from 'react';
import './Header.css';

const Header = () => {
  return (
    <header className="header">
      <div className="header-container">
        <h1 className="header-title">
          🏦 My Pension Rescue
        </h1>
        <p className="header-subtitle">
          국민연금 지속가능성 진단 및 시나리오 시뮬레이터
        </p>
      </div>
    </header>
  );
};

export default Header;
```

**`src/components/Layout/Header.css` 생성**

```css
.header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 2rem 1rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.header-container {
  max-width: 1200px;
  margin: 0 auto;
}

.header-title {
  font-size: 2rem;
  margin: 0 0 0.5rem 0;
  font-weight: 700;
}

.header-subtitle {
  font-size: 1rem;
  margin: 0;
  opacity: 0.9;
}
```

#### 4.2 대시보드 - Current Status

**`src/components/Dashboard/CurrentStatus.jsx` 생성**

```jsx
import React, { useState, useEffect } from 'react';
import { LineChart, Line, AreaChart, Area, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { loadCSV } from '../../utils/dataLoader';
import { calculateFundBalance, findDepletionYear, DEFAULT_PARAMS } from '../../utils/calculator';
import './CurrentStatus.css';

const CurrentStatus = () => {
  const [chartData, setChartData] = useState([]);
  const [depletionYear, setDepletionYear] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    const loadData = async () => {
      try {
        // 인구 데이터 로드
        const populationData = await loadCSV('/data/population.csv');
        
        // 기금 잔액 계산
        const results = calculateFundBalance(DEFAULT_PARAMS, populationData);
        
        // 차트 데이터 포맷
        const formatted = results.map(r => ({
          year: r.year,
          balance: r.balance / 1000000000000, // 조 단위로 변환
        }));
        
        setChartData(formatted);
        setDepletionYear(findDepletionYear(results));
        setLoading(false);
      } catch (error) {
        console.error('Error loading data:', error);
        setLoading(false);
      }
    };
    
    loadData();
  }, []);
  
  if (loading) {
    return <div className="loading">데이터 로딩 중...</div>;
  }
  
  return (
    <div className="current-status">
      <h2>📊 Current Status</h2>
      <p className="description">
        현재 법령 기준(보험료율 9%, 소득대체율 40%) 유지 시 기금 추이
      </p>
      
      {/* 주요 지표 카드 */}
      <div className="metrics-grid">
        <MetricCard
          title="보험료율"
          value="9%"
          icon="💰"
          description="매월 소득에서 내는 비율"
        />
        <MetricCard
          title="소득대체율"
          value="40%"
          icon="📈"
          description="생애 평균 소득 대비 연금액"
        />
        <MetricCard
          title="수급 연령"
          value="65세"
          icon="👴"
          description="연금을 받기 시작하는 나이"
        />
        <MetricCard
          title="기금 고갈 예상"
          value={depletionYear ? `${depletionYear}년` : '지속 가능'}
          icon="⚠️"
          description="현재 정책 유지 시"
          alert={!!depletionYear}
        />
      </div>
      
      {/* 기금 잔액 추이 그래프 */}
      <div className="chart-container">
        <h3>기금 잔액 추이 (단위: 조원)</h3>
        <ResponsiveContainer width="100%" height={400}>
          <AreaChart data={chartData}>
            <defs>
              <linearGradient id="colorBalance" x1="0" y1="0" x2="0" y2="1">
                <stop offset="5%" stopColor="#8884d8" stopOpacity={0.8}/>
                <stop offset="95%" stopColor="#8884d8" stopOpacity={0}/>
              </linearGradient>
            </defs>
            <CartesianGrid strokeDasharray="3 3" />
            <XAxis dataKey="year" />
            <YAxis />
            <Tooltip formatter={(value) => `${value.toFixed(0)} 조원`} />
            <Legend />
            <Area
              type="monotone"
              dataKey="balance"
              stroke="#8884d8"
              fillOpacity={1}
              fill="url(#colorBalance)"
              name="기금 잔액"
            />
          </AreaChart>
        </ResponsiveContainer>
      </div>
    </div>
  );
};

// 지표 카드 컴포넌트
const MetricCard = ({ title, value, icon, description, alert }) => {
  return (
    <div className={`metric-card ${alert ? 'alert' : ''}`}>
      <div className="metric-icon">{icon}</div>
      <div className="metric-content">
        <h4 className="metric-title">{title}</h4>
        <p className="metric-value">{value}</p>
        <p className="metric-description">{description}</p>
      </div>
    </div>
  );
};

export default CurrentStatus;
```

**`src/components/Dashboard/CurrentStatus.css` 생성**

```css
.current-status {
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
}

.current-status h2 {
  font-size: 1.8rem;
  margin-bottom: 0.5rem;
  color: #333;
}

.description {
  color: #666;
  margin-bottom: 2rem;
}

.metrics-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1.5rem;
  margin-bottom: 3rem;
}

.metric-card {
  background: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  display: flex;
  gap: 1rem;
  transition: transform 0.2s, box-shadow 0.2s;
}

.metric-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}

.metric-card.alert {
  border: 2px solid #ff6b6b;
  background: #fff5f5;
}

.metric-icon {
  font-size: 2.5rem;
}

.metric-content {
  flex: 1;
}

.metric-title {
  font-size: 0.9rem;
  color: #666;
  margin: 0 0 0.5rem 0;
  font-weight: 500;
}

.metric-value {
  font-size: 1.8rem;
  font-weight: 700;
  color: #333;
  margin: 0 0 0.25rem 0;
}

.metric-description {
  font-size: 0.85rem;
  color: #999;
  margin: 0;
}

.chart-container {
  background: white;
  border-radius: 12px;
  padding: 2rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.chart-container h3 {
  margin: 0 0 1.5rem 0;
  color: #333;
}

.loading {
  text-align: center;
  padding: 4rem;
  font-size: 1.2rem;
  color: #666;
}
```

#### 4.3 시뮬레이터 - Reform Lab

**`src/components/Simulator/ReformLab.jsx` 생성**

```jsx
import React, { useState, useEffect } from 'react';
import { AreaChart, Area, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer } from 'recharts';
import { calculateFundBalance, findDepletionYear, DEFAULT_PARAMS } from '../../utils/calculator';
import { loadCSV } from '../../utils/dataLoader';
import './ReformLab.css';

const ReformLab = () => {
  const [params, setParams] = useState(DEFAULT_PARAMS);
  const [populationData, setPopulationData] = useState([]);
  const [currentScenario, setCurrentScenario] = useState([]);
  const [baselineScenario, setBaselineScenario] = useState([]);
  const [depletionYear, setDepletionYear] = useState(null);
  
  // 인구 데이터 로드
  useEffect(() => {
    const loadData = async () => {
      const data = await loadCSV('/data/population.csv');
      setPopulationData(data);
      
      // 기본 시나리오 계산
      const baseline = calculateFundBalance(DEFAULT_PARAMS, data);
      setBaselineScenario(baseline);
      setCurrentScenario(baseline);
    };
    
    loadData();
  }, []);
  
  // 파라미터 변경 시 재계산
  useEffect(() => {
    if (populationData.length === 0) return;
    
    const results = calculateFundBalance(params, populationData);
    setCurrentScenario(results);
    setDepletionYear(findDepletionYear(results));
  }, [params, populationData]);
  
  // 슬라이더 변경 핸들러
  const handleParamChange = (key, value) => {
    setParams(prev => ({
      ...prev,
      [key]: parseFloat(value)
    }));
  };
  
  // 차트 데이터 포맷
  const chartData = currentScenario.map((current, index) => {
    const baseline = baselineScenario[index];
    return {
      year: current.year,
      current: current.balance / 1000000000000,
      baseline: baseline ? baseline.balance / 1000000000000 : null,
    };
  });
  
  return (
    <div className="reform-lab">
      <h2>🔬 Reform Lab</h2>
      <p className="description">
        정책 변수를 조정하여 다양한 개혁 시나리오를 시뮬레이션하세요
      </p>
      
      {/* 변수 조정 컨트롤 */}
      <div className="controls-container">
        <SliderControl
          label="보험료율"
          value={params.premiumRate}
          min={0.09}
          max={0.15}
          step={0.01}
          unit="%"
          onChange={(value) => handleParamChange('premiumRate', value)}
          description="매월 소득에서 내는 연금 보험료 비율"
        />
        
        <SliderControl
          label="소득대체율"
          value={params.replacementRate}
          min={0.40}
          max={0.50}
          step={0.01}
          unit="%"
          onChange={(value) => handleParamChange('replacementRate', value)}
          description="생애 평균 소득 대비 연금액 비율"
        />
        
        <SliderControl
          label="수급 연령"
          value={params.retirementAge}
          min={65}
          max={70}
          step={1}
          unit="세"
          onChange={(value) => handleParamChange('retirementAge', value)}
          description="연금을 받기 시작하는 나이"
        />
        
        <SliderControl
          label="기금 운용 수익률"
          value={params.returnRate}
          min={0.03}
          max={0.07}
          step={0.01}
          unit="%"
          onChange={(value) => handleParamChange('returnRate', value)}
          description="연금 기금 투자 수익률"
        />
      </div>
      
      {/* 고갈 시점 비교 */}
      <div className="depletion-comparison">
        <div className="comparison-item">
          <span className="label">현재 정책 고갈 시점:</span>
          <span className="value baseline">
            {findDepletionYear(baselineScenario) || '지속 가능'}
          </span>
        </div>
        <div className="comparison-item">
          <span className="label">조정된 시나리오 고갈 시점:</span>
          <span className={`value ${depletionYear ? 'alert' : 'success'}`}>
            {depletionYear || '지속 가능 ✅'}
          </span>
        </div>
      </div>
      
      {/* 비교 그래프 */}
      <div className="chart-container">
        <h3>시나리오 비교 (단위: 조원)</h3>
        <ResponsiveContainer width="100%" height={400}>
          <AreaChart data={chartData}>
            <CartesianGrid strokeDasharray="3 3" />
            <XAxis dataKey="year" />
            <YAxis />
            <Tooltip formatter={(value) => `${value?.toFixed(0) || 0} 조원`} />
            <Legend />
            <Area
              type="monotone"
              dataKey="baseline"
              stroke="#999"
              fill="#ddd"
              fillOpacity={0.3}
              name="현재 정책"
            />
            <Area
              type="monotone"
              dataKey="current"
              stroke="#667eea"
              fill="#667eea"
              fillOpacity={0.5}
              name="조정된 시나리오"
            />
          </AreaChart>
        </ResponsiveContainer>
      </div>
    </div>
  );
};

// 슬라이더 컨트롤 컴포넌트
const SliderControl = ({ label, value, min, max, step, unit, onChange, description }) => {
  const displayValue = unit === '%' ? (value * 100).toFixed(0) : value;
  
  return (
    <div className="slider-control">
      <div className="slider-header">
        <label className="slider-label">{label}</label>
        <span className="slider-value">{displayValue}{unit}</span>
      </div>
      <input
        type="range"
        min={min}
        max={max}
        step={step}
        value={value}
        onChange={(e) => onChange(e.target.value)}
        className="slider"
      />
      <p className="slider-description">{description}</p>
    </div>
  );
};

export default ReformLab;
```

---

### Step 5: 앱 통합 및 실행

**`src/App.jsx` 수정**

```jsx
import React, { useState } from 'react';
import Header from './components/Layout/Header';
import CurrentStatus from './components/Dashboard/CurrentStatus';
import ReformLab from './components/Simulator/ReformLab';
import './App.css';

function App() {
  const [activeTab, setActiveTab] = useState('status');
  
  return (
    <div className="App">
      <Header />
      
      <nav className="tab-navigation">
        <button
          className={`tab-button ${activeTab === 'status' ? 'active' : ''}`}
          onClick={() => setActiveTab('status')}
        >
          📊 Current Status
        </button>
        <button
          className={`tab-button ${activeTab === 'simulator' ? 'active' : ''}`}
          onClick={() => setActiveTab('simulator')}
        >
          🔬 Reform Lab
        </button>
      </nav>
      
      <main className="main-content">
        {activeTab === 'status' && <CurrentStatus />}
        {activeTab === 'simulator' && <ReformLab />}
      </main>
      
      <footer className="footer">
        <p>© 2025 My Pension Rescue | 데이터 출처: 통계청, 국민연금공단</p>
      </footer>
    </div>
  );
}

export default App;
```

**개발 서버 실행**

```bash
npm run dev
```

브라우저에서 `http://localhost:5173` 접속

---

## Phase 2: 고급 기능

### Step 6: 세대 간 부담 리포트

_(상세 내용은 TASKS.md 참조)_

### Step 7: 신규 재원 시나리오

_(상세 내용은 TASKS.md 참조)_

---

## 문제 해결

### 자주 발생하는 오류

#### 1. CSV 파일 로드 실패

**문제:** `Failed to fetch` 에러

**해결:**
```javascript
// Vite의 경우 public 폴더 사용
// data 폴더를 public 폴더로 이동
// 또는 import 사용
import populationData from '../data/population.csv?raw';
```

#### 2. 차트가 표시되지 않음

**문제:** Recharts 차트가 렌더링되지 않음

**해결:**
```jsx
// ResponsiveContainer에 명시적 높이 지정
<ResponsiveContainer width="100%" height={400}>
```

#### 3. 계산 결과가 이상함

**문제:** 기금 잔액이 음수 또는 비정상적인 값

**해결:**
- 데이터 타입 확인 (문자열 vs 숫자)
- 단위 확인 (원, 만원, 억원, 조원)
- console.log로 중간 계산 값 확인

---

## 참고 자료

### 공식 문서

- [React 공식 문서](https://react.dev/)
- [Recharts 문서](https://recharts.org/)
- [Material-UI 문서](https://mui.com/)

### 데이터 소스

- [통계청 KOSIS](https://kosis.kr/)
- [국민연금공단](https://www.nps.or.kr/)

### 추천 학습 자료

- [React Hooks 완벽 가이드](https://react.dev/reference/react)
- [데이터 시각화 베스트 프랙티스](https://www.storytellingwithdata.com/)

---

## 다음 단계

1. ✅ Phase 1 MVP 완성
2. 🔄 사용자 피드백 수집
3. 📈 Phase 2 고급 기능 개발
4. 🚀 배포 및 공유

**축하합니다! 🎉** Phase 1 MVP를 완성하셨습니다. 이제 실제 데이터를 연동하고 고급 기능을 추가해보세요!

---

*질문이나 문제가 있으시면 GitHub Issues에 등록해주세요.*
