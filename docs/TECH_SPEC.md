# Engineering Calculator - Technical Specification

> 공학용 계산기 웹 애플리케이션 기술 명세서

**버전**: 1.0.0  
**작성일**: 2025-12-23  
**프로젝트**: Engineering Calculator

---

## 📋 목차

1. [개요](#개요)
2. [기술 스택](#기술-스택)
3. [시스템 아키텍처](#시스템-아키텍처)
4. [프로젝트 구조](#프로젝트-구조)
5. [개발 환경 설정](#개발-환경-설정)
6. [코어 로직 구현](#코어-로직-구현)
7. [상태 관리](#상태-관리)
8. [UI 컴포넌트](#ui-컴포넌트)
9. [테스트 전략](#테스트-전략)
10. [빌드 및 배포](#빌드-및-배포)
11. [성능 최적화](#성능-최적화)

---

## 🎯 개요

### 프로젝트 목표

- **현대적인 웹 기술 스택** 활용
- **TDD 및 SOLID 원칙** 기반 개발
- **타입 안전성** 100% 보장
- **90% 이상 테스트 커버리지** 달성
- **Lighthouse 점수 90+** 달성

### 핵심 기능

1. **과학 계산기**: 기본 연산, 삼각함수, 로그, 제곱근 등
2. **단위 변환기**: 길이, 무게, 온도, 속도 등 다양한 단위 변환
3. **계산 기록**: 이전 계산 내역 저장 및 재사용
4. **상수 라이브러리**: 수학/물리 상수 빠른 입력

---

## 🛠 기술 스택

### Frontend Core

| 기술 | 버전 | 용도 | 선택 이유 |
|------|------|------|-----------|
| **React** | 19.2.3 | UI 라이브러리 | 최신 기능 (Compiler, Server Components 준비) |
| **TypeScript** | 5.9.3 | 타입 시스템 | 타입 안전성, 개발 생산성 향상 |
| **Vite** | 7.3.0 | 빌드 도구 | 빠른 HMR, 최적화된 번들링 |

### Styling & UI

| 기술 | 버전 | 용도 |
|------|------|------|
| **Tailwind CSS** | 3.4+ | 유틸리티 CSS 프레임워크 |
| **Framer Motion** | 11.0+ | 애니메이션 라이브러리 |
| **Material Symbols** | Latest | 아이콘 시스템 |
| **Space Grotesk** | Latest | 타이포그래피 |

### State Management & Data

| 기술 | 버전 | 용도 |
|------|------|------|
| **Zustand** | 4.5+ | 전역 상태 관리 |
| **React Hook Form** | 7.50+ | 폼 상태 관리 |
| **mathjs** | 12.4+ | 수학 연산 엔진 |

### Development Tools

| 기술 | 버전 | 용도 |
|------|------|------|
| **ESLint** | 9.39.2 | 코드 린팅 |
| **Prettier** | 3.2+ | 코드 포맷팅 |
| **Husky** | 9.0+ | Git 훅 관리 |
| **lint-staged** | 15.0+ | Pre-commit 검사 |

### Testing

| 기술 | 버전 | 용도 |
|------|------|------|
| **Vitest** | 1.2+ | 단위 테스트 프레임워크 |
| **Testing Library** | 14.2+ | React 컴포넌트 테스트 (선택적) |
| **@vitest/ui** | 1.2+ | 테스트 UI 대시보드 |

### CI/CD & Deployment

| 기술 | 용도 |
|------|------|
| **GitHub Actions** | CI/CD 파이프라인 |
| **GitHub Pages** | 정적 사이트 호스팅 |
| **pnpm** | 패키지 관리자 |

---

## 🏗 시스템 아키텍처

### 레이어 구조

```
┌─────────────────────────────────────┐
│         Presentation Layer          │
│    (React Components, Routing)      │
└─────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────┐
│        Business Logic Layer         │
│  (Calculation Engine, Validators)   │
└─────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────┐
│          Data Layer                 │
│   (Zustand Stores, LocalStorage)    │
└─────────────────────────────────────┘
```

### 컴포넌트 아키텍처

```
App
├── Layout
│   ├── Header
│   └── BottomNav (모바일)
├── Pages
│   ├── Calculator (과학 계산기)
│   ├── UnitConverter (단위 변환기)
│   ├── History (계산 기록)
│   └── Constants (상수 목록)
└── Shared Components
    ├── Display (결과 표시)
    ├── Keypad (숫자 패드)
    └── Button (공통 버튼)
```

---

## 📁 프로젝트 구조

```
engineering-calculator/
├── .github/
│   └── workflows/
│       ├── deploy.yml          # 배포 워크플로우
│       └── pr-check.yml        # PR 체크 워크플로우
├── docs/
│   ├── PRD.md                  # 제품 요구사항
│   ├── TECH_SPEC.md            # 기술 명세서 (이 문서)
│   ├── TDD_RULES.md            # TDD 개발 규칙
│   ├── SOLID_PRINCIPLES.md     # SOLID 원칙
│   └── design/                 # 디자인 파일
├── public/
│   └── vite.svg                # 파비콘
├── src/
│   ├── components/             # React 컴포넌트
│   │   ├── layout/            # 레이아웃 컴포넌트
│   │   │   ├── Header.tsx
│   │   │   ├── BottomNav.tsx
│   │   │   └── MainLayout.tsx
│   │   ├── calculator/        # 계산기 컴포넌트
│   │   │   ├── Display.tsx
│   │   │   ├── Keypad.tsx
│   │   │   ├── ScientificFunctions.tsx
│   │   │   └── Calculator.tsx
│   │   ├── converter/         # 단위 변환기 컴포넌트
│   │   │   ├── UnitSelector.tsx
│   │   │   ├── ConversionInput.tsx
│   │   │   └── UnitConverter.tsx
│   │   ├── history/           # 기록 컴포넌트
│   │   │   ├── HistoryItem.tsx
│   │   │   └── HistoryList.tsx
│   │   ├── constants/         # 상수 컴포넌트
│   │   │   ├── ConstantCard.tsx
│   │   │   └── ConstantList.tsx
│   │   └── ui/                # 공통 UI 컴포넌트
│   │       ├── Button.tsx
│   │       ├── Input.tsx
│   │       └── Card.tsx
│   ├── pages/                 # 페이지 컴포넌트
│   │   ├── CalculatorPage.tsx
│   │   ├── ConverterPage.tsx
│   │   ├── HistoryPage.tsx
│   │   └── ConstantsPage.tsx
│   ├── stores/                # Zustand 상태 관리
│   │   ├── useCalculatorStore.ts
│   │   ├── useConverterStore.ts
│   │   ├── useHistoryStore.ts
│   │   └── useSettingsStore.ts
│   ├── utils/                 # 유틸리티 함수 (TDD)
│   │   ├── calculations.ts
│   │   ├── unitConversion.ts
│   │   ├── formatters.ts
│   │   └── validators.ts
│   ├── hooks/                 # 커스텀 훅
│   │   ├── useCalculator.ts
│   │   ├── useConverter.ts
│   │   └── useLocalStorage.ts
│   ├── types/                 # TypeScript 타입
│   │   ├── calculator.ts
│   │   ├── converter.ts
│   │   └── history.ts
│   ├── constants/             # 상수 정의
│   │   ├── mathConstants.ts
│   │   ├── physicsConstants.ts
│   │   └── units.ts
│   ├── App.tsx                # 루트 컴포넌트
│   ├── main.tsx               # 엔트리 포인트
│   └── index.css              # 글로벌 스타일
├── tests/                     # 테스트 파일
│   ├── unit/                  # 단위 테스트
│   │   ├── calculations.test.ts
│   │   ├── unitConversion.test.ts
│   │   └── validators.test.ts
│   └── integration/           # 통합 테스트
│       └── calculator.test.ts
├── .eslintrc.cjs              # ESLint 설정
├── .prettierrc                # Prettier 설정
├── .gitignore                 # Git 무시 파일
├── eslint.config.js           # ESLint 설정 (새 형식)
├── index.html                 # HTML 템플릿
├── package.json               # 패키지 정의
├── pnpm-lock.yaml             # 의존성 잠금 파일
├── tsconfig.json              # TypeScript 설정
├── tsconfig.app.json          # 앱용 TS 설정
├── tsconfig.node.json         # Node용 TS 설정
├── vite.config.ts             # Vite 설정
└── README.md                  # 프로젝트 README
```

---

## ⚙️ 개발 환경 설정

### 1. Vite 설정 (`vite.config.ts`)

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig(({ mode }) => ({
  plugins: [react()],
  
  // GitHub Pages 배포를 위한 base 경로
  base: mode === 'production' ? '/engineering-calculator/' : '/',
  
  // 절대 경로 alias
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@types': path.resolve(__dirname, './src/types'),
      '@stores': path.resolve(__dirname, './src/stores'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
    }
  },
  
  // 빌드 설정
  build: {
    target: 'es2015',
    minify: 'esbuild',
    sourcemap: true,
    outDir: 'dist',
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'math-vendor': ['mathjs'],
        }
      }
    }
  },
  
  // 개발 서버 설정
  server: {
    port: 5173,
    open: true,
  }
}))
```

### 2. TypeScript 설정 (`tsconfig.json`)

```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@utils/*": ["./src/utils/*"],
      "@types/*": ["./src/types/*"],
      "@stores/*": ["./src/stores/*"],
      "@hooks/*": ["./src/hooks/*"]
    }
  }
}
```

### 3. Tailwind CSS 설정 (`tailwind.config.js`)

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: '#135bec',
        'background-light': '#f6f6f8',
        'background-dark': '#101622',
        'surface-dark': '#1e2433',
        'surface-light': '#ffffff',
      },
      fontFamily: {
        display: ['Space Grotesk', 'sans-serif'],
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
  ],
}
```

### 4. Vitest 설정 (`vitest.config.ts`)

```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/setupTests.ts',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'src/setupTests.ts',
        '**/*.d.ts',
        '**/*.config.*',
        '**/mockData',
        'src/main.tsx',
      ],
    },
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@types': path.resolve(__dirname, './src/types'),
    }
  },
})
```

---

## 🧮 코어 로직 구현

### 1. 계산 엔진 (`utils/calculations.ts`)

**책임**: 수학 연산 수행

```typescript
import { evaluate } from 'mathjs'

export interface CalculationResult {
  value: number
  expression: string
  error?: string
}

export class CalculationEngine {
  /**
   * 수식을 평가하여 결과 반환
   * @param expression 수학 표현식
   * @returns 계산 결과
   */
  static calculate(expression: string): CalculationResult {
    try {
      const value = evaluate(expression)
      return {
        value: typeof value === 'number' ? value : parseFloat(value),
        expression,
      }
    } catch (error) {
      return {
        value: 0,
        expression,
        error: error instanceof Error ? error.message : 'Calculation error',
      }
    }
  }

  /**
   * 삼각함수 계산 (각도 모드)
   */
  static sin(angle: number, isDegree: boolean = true): number {
    const radians = isDegree ? (angle * Math.PI) / 180 : angle
    return Math.sin(radians)
  }

  static cos(angle: number, isDegree: boolean = true): number {
    const radians = isDegree ? (angle * Math.PI) / 180 : angle
    return Math.cos(radians)
  }

  static tan(angle: number, isDegree: boolean = true): number {
    const radians = isDegree ? (angle * Math.PI) / 180 : angle
    return Math.tan(radians)
  }
}
```

**테스트 예시** (`tests/unit/calculations.test.ts`):

```typescript
import { describe, it, expect } from 'vitest'
import { CalculationEngine } from '@utils/calculations'

describe('CalculationEngine', () => {
  describe('calculate', () => {
    it('should calculate basic arithmetic', () => {
      const result = CalculationEngine.calculate('2 + 2')
      expect(result.value).toBe(4)
      expect(result.error).toBeUndefined()
    })

    it('should handle complex expressions', () => {
      const result = CalculationEngine.calculate('sin(30) + 5')
      expect(result.value).toBeCloseTo(5.5, 1)
    })

    it('should return error for invalid expression', () => {
      const result = CalculationEngine.calculate('2 +')
      expect(result.error).toBeDefined()
    })
  })

  describe('trigonometric functions', () => {
    it('should calculate sin in degrees', () => {
      expect(CalculationEngine.sin(30, true)).toBeCloseTo(0.5, 5)
    })

    it('should calculate cos in radians', () => {
      expect(CalculationEngine.cos(Math.PI, false)).toBeCloseTo(-1, 5)
    })
  })
})
```

### 2. 단위 변환 (`utils/unitConversion.ts`)

**책임**: 다양한 단위 간 변환

```typescript
export type UnitCategory = 'length' | 'mass' | 'temperature' | 'speed'

export interface UnitDefinition {
  symbol: string
  name: string
  category: UnitCategory
  toBase: (value: number) => number
  fromBase: (value: number) => number
}

export class UnitConverter {
  private static units: Map<string, UnitDefinition> = new Map()

  static registerUnit(unit: UnitDefinition): void {
    this.units.set(unit.symbol, unit)
  }

  static convert(value: number, from: string, to: string): number {
    const fromUnit = this.units.get(from)
    const toUnit = this.units.get(to)

    if (!fromUnit || !toUnit) {
      throw new Error(`Unknown unit: ${from} or ${to}`)
    }

    if (fromUnit.category !== toUnit.category) {
      throw new Error(`Cannot convert between different categories`)
    }

    // Convert to base unit, then to target unit
    const baseValue = fromUnit.toBase(value)
    return toUnit.fromBase(baseValue)
  }
}

// 길이 단위 등록
UnitConverter.registerUnit({
  symbol: 'm',
  name: 'Meter',
  category: 'length',
  toBase: (v) => v,
  fromBase: (v) => v,
})

UnitConverter.registerUnit({
  symbol: 'km',
  name: 'Kilometer',
  category: 'length',
  toBase: (v) => v * 1000,
  fromBase: (v) => v / 1000,
})

UnitConverter.registerUnit({
  symbol: 'cm',
  name: 'Centimeter',
  category: 'length',
  toBase: (v) => v / 100,
  fromBase: (v) => v * 100,
})
```

### 3. 포맷터 (`utils/formatters.ts`)

**책임**: 숫자 및 결과 포맷팅

```typescript
export class Formatter {
  /**
   * 숫자를 읽기 쉬운 형식으로 포맷
   */
  static formatNumber(value: number, precision: number = 10): string {
    // 매우 큰 숫자는 과학적 표기법 사용
    if (Math.abs(value) >= 1e10 || (Math.abs(value) < 1e-10 && value !== 0)) {
      return value.toExponential(precision)
    }

    // 일반 숫자는 소수점 자리 제한
    return parseFloat(value.toPrecision(precision)).toString()
  }

  /**
   * 표현식을 보기 좋게 포맷
   */
  static formatExpression(expression: string): string {
    return expression
      .replace(/\*/g, '×')
      .replace(/\//g, '÷')
      .replace(/sqrt/g, '√')
  }
}
```

### 4. 검증기 (`utils/validators.ts`)

**책임**: 입력 값 검증

```typescript
export class Validator {
  /**
   * 숫자 입력 검증
   */
  static isValidNumber(value: string): boolean {
    return !isNaN(parseFloat(value)) && isFinite(parseFloat(value))
  }

  /**
   * 표현식 검증
   */
  static isValidExpression(expression: string): boolean {
    try {
      evaluate(expression)
      return true
    } catch {
      return false
    }
  }

  /**
   * 범위 검증
   */
  static isInRange(value: number, min: number, max: number): boolean {
    return value >= min && value <= max
  }
}
```

---

## 🗄️ 상태 관리

### Zustand Store 구조

#### 1. Calculator Store (`stores/useCalculatorStore.ts`)

```typescript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface CalculatorState {
  // State
  display: string
  expression: string
  history: string[]
  isDegree: boolean
  
  // Actions
  appendToExpression: (value: string) => void
  calculate: () => void
  clear: () => void
  backspace: () => void
  toggleAngleMode: () => void
  addToHistory: (expression: string, result: string) => void
}

export const useCalculatorStore = create<CalculatorState>()(
  persist(
    (set, get) => ({
      display: '0',
      expression: '',
      history: [],
      isDegree: true,

      appendToExpression: (value) => set((state) => ({
        expression: state.expression + value,
        display: state.expression + value,
      })),

      calculate: () => {
        const { expression } = get()
        const result = CalculationEngine.calculate(expression)
        
        if (!result.error) {
          set({
            display: Formatter.formatNumber(result.value),
            expression: '',
          })
          get().addToHistory(expression, result.value.toString())
        }
      },

      clear: () => set({ display: '0', expression: '' }),

      backspace: () => set((state) => ({
        expression: state.expression.slice(0, -1),
        display: state.expression.slice(0, -1) || '0',
      })),

      toggleAngleMode: () => set((state) => ({
        isDegree: !state.isDegree,
      })),

      addToHistory: (expression, result) => set((state) => ({
        history: [`${expression} = ${result}`, ...state.history].slice(0, 50),
      })),
    }),
    {
      name: 'calculator-storage',
      partialize: (state) => ({ history: state.history, isDegree: state.isDegree }),
    }
  )
)
```

#### 2. Settings Store (`stores/useSettingsStore.ts`)

```typescript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface SettingsState {
  theme: 'light' | 'dark' | 'system'
  precision: number
  hapticFeedback: boolean
  
  setTheme: (theme: 'light' | 'dark' | 'system') => void
  setPrecision: (precision: number) => void
  toggleHapticFeedback: () => void
}

export const useSettingsStore = create<SettingsState>()(
  persist(
    (set) => ({
      theme: 'system',
      precision: 10,
      hapticFeedback: true,

      setTheme: (theme) => set({ theme }),
      setPrecision: (precision) => set({ precision }),
      toggleHapticFeedback: () => set((state) => ({
        hapticFeedback: !state.hapticFeedback,
      })),
    }),
    {
      name: 'settings-storage',
    }
  )
)
```

---

## 🎨 UI 컴포넌트

### 디자인 시스템

#### 색상 팔레트

```typescript
// tailwind.config.js
colors: {
  primary: '#135bec',
  'background-light': '#f6f6f8',
  'background-dark': '#101622',
  'surface-dark': '#1e2433',
  'surface-light': '#ffffff',
}
```

#### 타이포그래피

- **폰트**: Space Grotesk
- **크기**:
  - Display (결과): 56px
  - Expression: 20px
  - Button: 24px

#### 컴포넌트 예시

**Button 컴포넌트** (`components/ui/Button.tsx`):

```typescript
import { ButtonHTMLAttributes, ReactNode } from 'react'
import { clsx } from 'clsx'

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'number' | 'operator' | 'function' | 'action'
  children: ReactNode
}

export function Button({ variant = 'number', children, className, ...props }: ButtonProps) {
  return (
    <button
      className={clsx(
        'flex items-center justify-center rounded-2xl h-16 font-bold transition-all',
        'hover:brightness-110 active:scale-95',
        {
          'bg-white dark:bg-[#1a1f29] text-neutral-900 dark:text-white text-2xl shadow-sm': 
            variant === 'number',
          'bg-primary/10 dark:bg-primary/20 text-primary text-2xl': 
            variant === 'operator',
          'bg-neutral-100 dark:bg-[#282e39] text-neutral-700 dark:text-white text-sm': 
            variant === 'function',
          'bg-neutral-200 dark:bg-[#343b48] text-primary text-xl': 
            variant === 'action',
        },
        className
      )}
      {...props}
    >
      {children}
    </button>
  )
}
```

---

## 🧪 테스트 전략

### 테스트 피라미드

```
        /\
       /E2\      E2E Tests (수동)
      /____\     - 전체 사용자 플로우
     /      \
    / Integ \   Integration Tests (선택적)
   /__________\  - 컴포넌트 통합
  /            \
 /    Unit      \ Unit Tests (TDD)
/________________\ - 코어 로직 90%+ 커버리지
```

### 테스트 범위

#### ✅ 자동화 테스트 (TDD)

- **코어 로직**: 90% 이상 커버리지
  - `calculations.ts`
  - `unitConversion.ts`
  - `formatters.ts`
  - `validators.ts`
- **상태 관리**: 비즈니스 로직만
  - Store actions
  - State transitions

#### 🖱️ 수동 테스트

- **UI 컴포넌트**: 브라우저에서 수동 확인
- **사용자 플로우**: E2E 시나리오
- **반응형 디자인**: 다양한 화면 크기

### package.json Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage",
    "test:ui": "vitest --ui",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "type-check": "tsc --noEmit"
  }
}
```

---

## 🚀 빌드 및 배포

### CI/CD 파이프라인

#### Deploy Workflow (`.github/workflows/deploy.yml`)

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      - run: pnpm install --no-frozen-lockfile
      - run: pnpm lint || echo "Lint not configured yet"
      - run: pnpm type-check || echo "Type check not configured yet"
      - run: pnpm test:run || echo "Tests not configured yet"
      - run: pnpm build
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'
  
  deploy:
    if: github.ref == 'refs/heads/main'
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/deploy-pages@v4
        id: deployment
```

### 배포 URL

- **Production**: https://hennessynlove7552.github.io/engineering-calculator/
- **Preview**: `pnpm preview` (로컬)

---

## ⚡ 성능 최적화

### 1. 코드 스플리팅

```typescript
// App.tsx
import { lazy, Suspense } from 'react'

const CalculatorPage = lazy(() => import('./pages/CalculatorPage'))
const ConverterPage = lazy(() => import('./pages/ConverterPage'))
const HistoryPage = lazy(() => import('./pages/HistoryPage'))

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/" element={<CalculatorPage />} />
        <Route path="/converter" element={<ConverterPage />} />
        <Route path="/history" element={<HistoryPage />} />
      </Routes>
    </Suspense>
  )
}
```

### 2. 번들 최적화

```typescript
// vite.config.ts
build: {
  rollupOptions: {
    output: {
      manualChunks: {
        'react-vendor': ['react', 'react-dom'],
        'math-vendor': ['mathjs'],
        'state-vendor': ['zustand'],
      }
    }
  }
}
```

### 3. 메모이제이션

```typescript
// 비용이 큰 계산 캐싱
const result = useMemo(() => {
  return CalculationEngine.calculate(expression)
}, [expression])

// 콜백 메모이제이션
const handleCalculate = useCallback(() => {
  calculate()
}, [calculate])
```

### 성능 목표

| 지표 | 목표 | 측정 방법 |
|------|------|-----------|
| Initial Load | < 2s | Lighthouse |
| Time to Interactive | < 3s | Lighthouse |
| Bundle Size | < 500KB | `pnpm build` |
| Lighthouse Score | > 90 | Chrome DevTools |

---

## 📚 참고 자료

- [React 19 Documentation](https://react.dev/)
- [Vite Documentation](https://vitejs.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Zustand Documentation](https://github.com/pmndrs/zustand)
- [Tailwind CSS](https://tailwindcss.com/)
- [Vitest Documentation](https://vitest.dev/)
- [mathjs Documentation](https://mathjs.org/)

---

**문서 버전**: 1.0.0  
**최종 수정일**: 2025-12-23  
**작성자**: Engineering Calculator Team
