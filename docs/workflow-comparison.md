# GitHub Actions 워크플로우 비교 분석

## 📊 세 가지 워크플로우 비교

프로젝트에는 현재 3개의 배포 워크플로우가 있습니다. 각각의 특징과 장단점을 비교합니다.

---

## 1️⃣ deploy.yml (npm 스크립트 기반)

### 특징
- npm 스크립트 `build:presentation` 사용
- npm 캐싱 활용
- 프로젝트 루트 전체 업로드

### 장점
✅ npm 캐싱으로 빌드 속도 빠름  
✅ `package.json`의 스크립트로 빌드 과정 관리  
✅ 추가 빌드 단계 쉽게 추가 가능

### 단점
❌ `package.json`에 `build:presentation` 스크립트 필요  
❌ 프로젝트 루트 전체를 업로드 (불필요한 파일 포함 가능)  
❌ Marp 설정이 npm 스크립트에 숨겨져 있음

### 코드
```yaml
- name: Install dependencies
  run: npm ci

- name: Build presentation
  run: npm run build:presentation

- name: Upload artifact
  uses: actions/upload-pages-artifact@v3
  with:
    path: '.'  # 프로젝트 루트 전체
```

---

## 2️⃣ deploy-marp.yml (Marp CLI 직접 사용)

### 특징
- Marp CLI를 전역으로 설치하여 직접 사용
- 명시적인 Marp 옵션 지정
- `_site` 디렉토리에 출력

### 장점
✅ Marp 명령어가 명확하게 보임  
✅ `package.json` 불필요  
✅ 필요한 파일만 `_site`에 생성하여 업로드  
✅ 워크플로우만 보고 전체 과정 이해 가능

### 단점
❌ npm 캐싱 없음 (매번 Marp CLI 설치)  
❌ 프로젝트 의존성 설치 안 함  
❌ 추가 에셋 복사 로직 없음

### 코드
```yaml
- name: Install Marp CLI
  run: npm install -g @marp-team/marp-cli

- name: Convert Marp to HTML
  run: |
    marp docs/presentation.md \
      --html \
      --allow-local-files \
      -o _site/index.html

- name: Upload artifact
  uses: actions/upload-pages-artifact@v3
  with:
    path: '_site'  # 필요한 파일만
```

---

## 3️⃣ deploy-optimized.yml ⭐ (최적화 버전)

### 특징
- 두 워크플로우의 **장점을 결합**
- npm 캐싱 + 명시적 Marp CLI 사용
- 에셋 자동 복사 기능 추가

### 장점
✅ **npm 캐싱**으로 빌드 속도 향상  
✅ **명시적인 Marp 명령어**로 투명성 확보  
✅ **_site 디렉토리**로 깔끔한 출력  
✅ **에셋 자동 복사** (images, assets 폴더)  
✅ 프로젝트 의존성도 설치하여 확장성 확보  
✅ 에러 발생 시에도 계속 진행 (`continue-on-error`)

### 추가 기능
🎯 **자동 에셋 복사**: `docs/assets`, `docs/images` 폴더가 있으면 자동으로 `_site`에 복사  
🎯 **유연성**: npm 스크립트와 Marp CLI 둘 다 사용 가능  
🎯 **안정성**: 에셋이 없어도 에러 없이 계속 진행

### 코드
```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'  # 캐싱 활성화

- name: Install dependencies
  run: npm ci

- name: Install Marp CLI globally
  run: npm install -g @marp-team/marp-cli

- name: Create output directory
  run: mkdir -p _site

- name: Convert Marp to HTML
  run: |
    marp docs/presentation.md \
      --html \
      --allow-local-files \
      -o _site/index.html

- name: Copy additional assets (if any)
  run: |
    if [ -d "docs/assets" ]; then
      cp -r docs/assets _site/
    fi
    if [ -d "docs/images" ]; then
      cp -r docs/images _site/
    fi
  continue-on-error: true

- name: Upload artifact
  uses: actions/upload-pages-artifact@v3
  with:
    path: '_site'
```

---

## 📈 성능 비교

| 항목 | deploy.yml | deploy-marp.yml | deploy-optimized.yml |
|------|-----------|-----------------|---------------------|
| **빌드 속도** | ⚡⚡⚡ (캐싱) | ⚡⚡ (캐싱 없음) | ⚡⚡⚡ (캐싱) |
| **투명성** | ⭐⭐ (스크립트 숨김) | ⭐⭐⭐ (명시적) | ⭐⭐⭐ (명시적) |
| **유연성** | ⭐⭐⭐ (확장 쉬움) | ⭐⭐ (제한적) | ⭐⭐⭐ (확장 쉬움) |
| **에셋 처리** | ❓ (불명확) | ❌ (없음) | ✅ (자동) |
| **파일 크기** | ❌ (전체 업로드) | ✅ (필요한 것만) | ✅ (필요한 것만) |

---

## 🎯 권장사항

### 현재 상황에 따른 선택

#### **deploy-optimized.yml 사용 추천** ⭐
- 가장 균형 잡힌 솔루션
- 모든 장점을 결합
- 미래 확장성 확보

#### 기존 파일 처리
1. **deploy.yml** → 삭제 또는 이름 변경 (`deploy.yml.backup`)
2. **deploy-marp.yml** → 삭제 또는 이름 변경 (`deploy-marp.yml.backup`)
3. **deploy-optimized.yml** → 메인 워크플로우로 사용

---

## 🔧 마이그레이션 가이드

### Step 1: 기존 워크플로우 비활성화

기존 워크플로우를 삭제하거나 비활성화합니다:

```bash
# 백업 (선택사항)
mv .github/workflows/deploy.yml .github/workflows/deploy.yml.backup
mv .github/workflows/deploy-marp.yml .github/workflows/deploy-marp.yml.backup

# 또는 삭제
rm .github/workflows/deploy.yml
rm .github/workflows/deploy-marp.yml
```

### Step 2: 최적화 버전을 메인으로 설정

```bash
# deploy-optimized.yml을 deploy.yml로 이름 변경
mv .github/workflows/deploy-optimized.yml .github/workflows/deploy.yml
```

### Step 3: 커밋 및 푸시

```bash
git add .github/workflows/
git commit -m "chore: 워크플로우 최적화 - 두 워크플로우의 장점 결합"
git push origin main
```

---

## 💡 추가 개선 아이디어

### 1. 조건부 배포
특정 파일이 변경되었을 때만 배포:

```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'docs/presentation.md'
      - 'docs/assets/**'
      - 'docs/images/**'
```

### 2. 빌드 결과 캐싱
Marp CLI 설치를 캐싱:

```yaml
- name: Cache Marp CLI
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-marp-${{ hashFiles('**/package-lock.json') }}
```

### 3. 다중 프레젠테이션 지원
여러 Marp 파일 동시 변환:

```yaml
- name: Convert all Marp files
  run: |
    for file in docs/*.md; do
      filename=$(basename "$file" .md)
      marp "$file" --html --allow-local-files -o "_site/${filename}.html"
    done
```

---

## 📚 결론

**deploy-optimized.yml**은 두 기존 워크플로우의 장점을 모두 가져와:
- ✅ 빠른 빌드 (npm 캐싱)
- ✅ 명확한 프로세스 (Marp CLI 직접 사용)
- ✅ 깔끔한 출력 (_site 디렉토리)
- ✅ 자동 에셋 관리
- ✅ 확장 가능성

**하나의 워크플로우로 통합하여 관리 복잡도를 줄이고 성능을 최적화하세요!** 🚀
