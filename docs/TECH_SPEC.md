# Scientific Calculator - Technical Specification

> 과학 계산기 웹 애플리케이션 기술 명세서

**버전**: 2.0.0  
**작성일**: 2025-12-23  
**프로젝트**: Scientific Calculator  
**기반 문서**: [PRD v1.0.0](./PRD.md)

---

## 📋 목차

1. [개요](#개요)
2. [기술 스택](#기술-스택)
3. [시스템 아키텍처](#시스템-아키텍처)
4. [프로젝트 구조](#프로젝트-구조)
5. [개발 환경](#개발-환경)
6. [코어 로직 구현](#코어-로직-구현)
7. [상태 관리](#상태-관리)
8. [UI 컴포넌트](#ui-컴포넌트)
9. [데이터 저장](#데이터-저장)
10. [테스트 전략](#테스트-전략)
11. [빌드 및 배포](#빌드-및-배포)
12. [성능 최적화](#성능-최적화)

---

## 🎯 개요

### 프로젝트 범위 (MVP)

**포함 기능**:
- ✅ 과학 계산기 (기본 연산 + 과학 함수)
- ✅ 계산 기록 (LocalStorage)
- ✅ 설정 (테마, 햅틱, 정밀도)

**제외 기능** (Phase 2):
- ❌ 단위 변환기
- ❌ 상수 라이브러리
- ❌ 그래프 플로팅

### 기술적 목표

1. **모바일 우선**: 최대 너비 448px, 터치 최적화
2. **TDD 개발**: 코어 로직 90% 커버리지
3. **타입 안전성**: TypeScript 100%
4. **성능**: Lighthouse 90+, 로딩 < 2초
5. **오프라인**: PWA 지원

---

## 🛠 기술 스택

### Core Technologies

| 기술 | 버전 | 용도 | 선택 이유 |
|------|------|------|-----------|
| **React** | 19.2.3 | UI 라이브러리 | 최신 기능, 성능 최적화 |
| **TypeScript** | 5.9.3 | 타입 시스템 | 타입 안전성, 개발 생산성 |
| **Vite** | 7.3.0 | 빌드 도구 | 빠른 HMR, 최적화된 번들링 |

### Styling & UI

| 기술 | 버전 | 용도 |
|------|------|------|
| **Tailwind CSS** | 3.4+ | 유틸리티 CSS |
| **Space Grotesk** | Latest | 타이포그래피 |
| **Material Symbols** | Latest | 아이콘 |

### State & Data

| 기술 | 버전 | 용도 |
|------|------|------|
| **Zustand** | 4.5+ | 전역 상태 관리 |
| **mathjs** | 12.4+ | 수학 연산 엔진 |

### Development & Testing

| 기술 | 버전 | 용도 |
|------|------|------|
| **Vitest** | 1.2+ | 단위 테스트 |
| **ESLint** | 9.39+ | 코드 린팅 |
| **Prettier** | 3.2+ | 코드 포맷팅 |

### Deployment

| 기술 | 용도 |
|------|------|
| **GitHub Actions** | CI/CD |
| **GitHub Pages** | 호스팅 |
| **pnpm** | 패키지 관리 |

---

## 🏗 시스템 아키텍처

### 레이어 구조

```
┌─────────────────────────────────────┐
│      Presentation Layer             │
│   (Calculator UI, Settings)         │
└─────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────┐
│     Business Logic Layer            │
│  (CalculationEngine, Formatter)     │
└─────────────────────────────────────┘
                  ↓
┌─────────────────────────────────────┐
│         Data Layer                  │
│  (Zustand Store, LocalStorage)      │
└─────────────────────────────────────┘
```

### 컴포넌트 계층

```
App
├── CalculatorPage (메인)
│   ├── Header
│   │   ├── Title
│   │   └── SettingsButton
│   ├── Display
│   │   ├── Expression
│   │   └── Result
│   └── Keypad
│       ├── ScientificFunctions
│       │   ├── sin, cos, tan, deg
│       │   └── ln, log, √, x²
│       └── NumberPad
│           ├── AC, ←, %, ÷
│           ├── 7, 8, 9, ×
│           ├── 4, 5, 6, -
│           ├── 1, 2, 3, +
│           └── 0, ., =
├── HistoryPage (기록)
│   ├── Header
│   ├── HistoryList
│   │   └── HistoryItem[]
│   └── ClearAllButton
└── SettingsPage (설정)
    ├── Header
    ├── ThemeSelector
    ├── PrecisionSlider
    └── HapticToggle
```

---

## 📁 프로젝트 구조

```
scientific-calculator/
├── .github/
│   └── workflows/
│       ├── deploy.yml              # 배포 워크플로우
│       └── pr-check.yml            # PR 체크
├── docs/
│   ├── PRD.md                      # 제품 요구사항
│   ├── TECH_SPEC.md                # 기술 명세서 (이 문서)
│   ├── TDD_RULES.md                # TDD 규칙
│   └── SOLID_PRINCIPLES.md         # SOLID 원칙
├── public/
│   ├── manifest.json               # PWA 매니페스트
│   └── icons/                      # PWA 아이콘
├── src/
│   ├── components/
│   │   ├── calculator/
│   │   │   ├── Display.tsx         # 결과 표시
│   │   │   ├── ScientificFunctions.tsx
│   │   │   ├── NumberPad.tsx
│   │   │   └── Calculator.tsx      # 메인 계산기
│   │   ├── history/
│   │   │   ├── HistoryItem.tsx
│   │   │   └── HistoryList.tsx
│   │   ├── settings/
│   │   │   ├── ThemeSelector.tsx
│   │   │   ├── PrecisionSlider.tsx
│   │   │   └── HapticToggle.tsx
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   └── BottomNav.tsx
│   │   └── ui/
│   │       ├── Button.tsx          # 공통 버튼
│   │       └── Card.tsx
│   ├── pages/
│   │   ├── CalculatorPage.tsx      # 메인 페이지
│   │   ├── HistoryPage.tsx         # 기록 페이지
│   │   └── SettingsPage.tsx        # 설정 페이지
│   ├── stores/
│   │   ├── useCalculatorStore.ts   # 계산기 상태
│   │   ├── useHistoryStore.ts      # 기록 상태
│   │   └── useSettingsStore.ts     # 설정 상태
│   ├── utils/                      # 코어 로직 (TDD)
│   │   ├── calculations.ts         # 계산 엔진
│   │   ├── formatters.ts           # 포맷터
│   │   ├── validators.ts           # 검증기
│   │   └── haptics.ts              # 햅틱 피드백
│   ├── hooks/
│   │   ├── useCalculator.ts        # 계산기 훅
│   │   ├── useHistory.ts           # 기록 훅
│   │   └── useTheme.ts             # 테마 훅
│   ├── types/
│   │   ├── calculator.ts           # 계산기 타입
│   │   └── history.ts              # 기록 타입
│   ├── constants/
│   │   └── config.ts               # 설정 상수
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── tests/
│   └── unit/
│       ├── calculations.test.ts
│       ├── formatters.test.ts
│       └── validators.test.ts
├── package.json
├── vite.config.ts
├── tsconfig.json
└── tailwind.config.js
```

---

## ⚙️ 개발 환경

### 1. Vite 설정

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { VitePWA } from 'vite-plugin-pwa'
import path from 'path'

export default defineConfig(({ mode }) => ({
  plugins: [
    react(),
    VitePWA({
      registerType: 'autoUpdate',
      manifest: {
        name: 'Scientific Calculator',
        short_name: 'Calculator',
        description: 'Modern scientific calculator',
        theme_color: '#135bec',
        background_color: '#101622',
        display: 'standalone',
        icons: [
          {
            src: '/icon-192.png',
            sizes: '192x192',
            type: 'image/png'
          },
          {
            src: '/icon-512.png',
            sizes: '512x512',
            type: 'image/png'
          }
        ]
      }
    })
  ],
  
  base: mode === 'production' ? '/engineering-calculator/' : '/',
  
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@types': path.resolve(__dirname, './src/types'),
      '@stores': path.resolve(__dirname, './src/stores'),
    }
  },
  
  build: {
    target: 'es2015',
    minify: 'esbuild',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'math-vendor': ['mathjs'],
        }
      }
    }
  }
}))
```

### 2. Tailwind 설정

```javascript
// tailwind.config.js
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
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
      maxWidth: {
        'calculator': '448px', // 모바일 최적화
      },
    },
  },
  plugins: [],
}
```

---

## 🧮 코어 로직 구현

### 1. 계산 엔진 (`utils/calculations.ts`)

```typescript
import { evaluate } from 'mathjs'

export interface CalculationResult {
  value: number
  expression: string
  error?: string
}

export class CalculationEngine {
  /**
   * 표현식 계산
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
        error: this.getErrorMessage(error),
      }
    }
  }

  /**
   * 삼각함수 (각도 모드 지원)
   */
  static sin(angle: number, isDegree: boolean = true): number {
    const radians = isDegree ? this.degToRad(angle) : angle
    return Math.sin(radians)
  }

  static cos(angle: number, isDegree: boolean = true): number {
    const radians = isDegree ? this.degToRad(angle) : angle
    return Math.cos(radians)
  }

  static tan(angle: number, isDegree: boolean = true): number {
    const radians = isDegree ? this.degToRad(angle) : angle
    return Math.tan(radians)
  }

  /**
   * 각도 변환
   */
  private static degToRad(degrees: number): number {
    return (degrees * Math.PI) / 180
  }

  /**
   * 에러 메시지 생성
   */
  private static getErrorMessage(error: unknown): string {
    if (error instanceof Error) {
      if (error.message.includes('division by zero')) {
        return 'Error: Division by zero'
      }
      return 'Error: Invalid expression'
    }
    return 'Error: Unknown error'
  }
}
```

**테스트** (`tests/unit/calculations.test.ts`):

```typescript
import { describe, it, expect } from 'vitest'
import { CalculationEngine } from '@utils/calculations'

describe('CalculationEngine', () => {
  describe('calculate', () => {
    it('should calculate basic arithmetic', () => {
      expect(CalculationEngine.calculate('2 + 2').value).toBe(4)
      expect(CalculationEngine.calculate('10 - 3').value).toBe(7)
      expect(CalculationEngine.calculate('4 * 5').value).toBe(20)
      expect(CalculationEngine.calculate('15 / 3').value).toBe(5)
    })

    it('should handle complex expressions', () => {
      const result = CalculationEngine.calculate('(2 + 3) * 4')
      expect(result.value).toBe(20)
    })

    it('should return error for division by zero', () => {
      const result = CalculationEngine.calculate('1 / 0')
      expect(result.error).toContain('Division by zero')
    })

    it('should return error for invalid expression', () => {
      const result = CalculationEngine.calculate('2 +')
      expect(result.error).toBeDefined()
    })
  })

  describe('trigonometric functions', () => {
    it('should calculate sin in degrees', () => {
      expect(CalculationEngine.sin(30, true)).toBeCloseTo(0.5, 5)
      expect(CalculationEngine.sin(90, true)).toBeCloseTo(1, 5)
    })

    it('should calculate cos in degrees', () => {
      expect(CalculationEngine.cos(0, true)).toBeCloseTo(1, 5)
      expect(CalculationEngine.cos(60, true)).toBeCloseTo(0.5, 5)
    })

    it('should calculate tan in degrees', () => {
      expect(CalculationEngine.tan(45, true)).toBeCloseTo(1, 5)
    })

    it('should calculate in radians', () => {
      expect(CalculationEngine.sin(Math.PI / 2, false)).toBeCloseTo(1, 5)
    })
  })
})
```

### 2. 포맷터 (`utils/formatters.ts`)

```typescript
export class Formatter {
  /**
   * 숫자 포맷팅
   */
  static formatNumber(value: number, precision: number = 10): string {
    // 매우 큰/작은 숫자는 과학적 표기법
    if (Math.abs(value) >= 1e10 || (Math.abs(value) < 1e-10 && value !== 0)) {
      return value.toExponential(precision)
    }

    // 일반 숫자
    const formatted = parseFloat(value.toPrecision(precision))
    return formatted.toString()
  }

  /**
   * 표현식 포맷팅 (보기 좋게)
   */
  static formatExpression(expression: string): string {
    return expression
      .replace(/\*/g, '×')
      .replace(/\//g, '÷')
      .replace(/sqrt/g, '√')
  }

  /**
   * 기록 항목 포맷팅
   */
  static formatHistoryItem(expression: string, result: number): string {
    return `${this.formatExpression(expression)} = ${this.formatNumber(result)}`
  }
}
```

### 3. 햅틱 피드백 (`utils/haptics.ts`)

```typescript
export class Haptics {
  /**
   * 햅틱 피드백 실행
   */
  static vibrate(duration: number = 10): void {
    if ('vibrate' in navigator) {
      navigator.vibrate(duration)
    }
  }

  /**
   * 버튼 탭 피드백
   */
  static buttonTap(): void {
    this.vibrate(10)
  }

  /**
   * 에러 피드백
   */
  static error(): void {
    this.vibrate([10, 50, 10])
  }

  /**
   * 성공 피드백
   */
  static success(): void {
    this.vibrate(20)
  }
}
```

---

## 🗄️ 상태 관리

### Calculator Store

```typescript
// stores/useCalculatorStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'
import { CalculationEngine } from '@utils/calculations'
import { Formatter } from '@utils/formatters'

interface CalculatorState {
  // State
  display: string
  expression: string
  isDegree: boolean
  lastResult: number | null
  
  // Actions
  appendToExpression: (value: string) => void
  calculate: () => void
  clear: () => void
  backspace: () => void
  toggleAngleMode: () => void
  
  // Scientific functions
  applySin: () => void
  applyCos: () => void
  applyTan: () => void
  applyLn: () => void
  applyLog: () => void
  applySqrt: () => void
  applySquare: () => void
}

export const useCalculatorStore = create<CalculatorState>()(
  persist(
    (set, get) => ({
      display: '0',
      expression: '',
      isDegree: true,
      lastResult: null,

      appendToExpression: (value) => set((state) => {
        const newExpression = state.expression + value
        return {
          expression: newExpression,
          display: newExpression || '0',
        }
      }),

      calculate: () => {
        const { expression } = get()
        if (!expression) return

        const result = CalculationEngine.calculate(expression)
        
        if (result.error) {
          set({ display: result.error })
        } else {
          const formatted = Formatter.formatNumber(result.value)
          set({
            display: formatted,
            expression: '',
            lastResult: result.value,
          })
          
          // 기록에 추가
          useHistoryStore.getState().addToHistory(expression, result.value)
        }
      },

      clear: () => set({
        display: '0',
        expression: '',
        lastResult: null,
      }),

      backspace: () => set((state) => {
        const newExpression = state.expression.slice(0, -1)
        return {
          expression: newExpression,
          display: newExpression || '0',
        }
      }),

      toggleAngleMode: () => set((state) => ({
        isDegree: !state.isDegree,
      })),

      applySin: () => {
        const { expression, isDegree } = get()
        const newExpr = `sin(${expression || '0'})`
        set({ expression: newExpr, display: newExpr })
      },

      applyCos: () => {
        const { expression } = get()
        const newExpr = `cos(${expression || '0'})`
        set({ expression: newExpr, display: newExpr })
      },

      applyTan: () => {
        const { expression } = get()
        const newExpr = `tan(${expression || '0'})`
        set({ expression: newExpr, display: newExpr })
      },

      applyLn: () => {
        const { expression } = get()
        const newExpr = `log(${expression || '0'})`
        set({ expression: newExpr, display: newExpr })
      },

      applyLog: () => {
        const { expression } = get()
        const newExpr = `log10(${expression || '0'})`
        set({ expression: newExpr, display: newExpr })
      },

      applySqrt: () => {
        const { expression } = get()
        const newExpr = `sqrt(${expression || '0'})`
        set({ expression: newExpr, display: newExpr })
      },

      applySquare: () => {
        const { expression } = get()
        const newExpr = `(${expression || '0'})^2`
        set({ expression: newExpr, display: newExpr })
      },
    }),
    {
      name: 'calculator-storage',
      partialize: (state) => ({ isDegree: state.isDegree }),
    }
  )
)
```

### History Store

```typescript
// stores/useHistoryStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'
import { Formatter } from '@utils/formatters'

interface HistoryItem {
  id: string
  expression: string
  result: number
  timestamp: number
}

interface HistoryState {
  items: HistoryItem[]
  addToHistory: (expression: string, result: number) => void
  removeItem: (id: string) => void
  clearAll: () => void
  loadExpression: (expression: string) => void
}

export const useHistoryStore = create<HistoryState>()(
  persist(
    (set) => ({
      items: [],

      addToHistory: (expression, result) => set((state) => ({
        items: [
          {
            id: Date.now().toString(),
            expression,
            result,
            timestamp: Date.now(),
          },
          ...state.items,
        ].slice(0, 50), // 최대 50개
      })),

      removeItem: (id) => set((state) => ({
        items: state.items.filter((item) => item.id !== id),
      })),

      clearAll: () => set({ items: [] }),

      loadExpression: (expression) => {
        useCalculatorStore.getState().appendToExpression(expression)
      },
    }),
    {
      name: 'history-storage',
    }
  )
)
```

### Settings Store

```typescript
// stores/useSettingsStore.ts
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
      theme: 'dark',
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

### Button 컴포넌트

```typescript
// components/ui/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from 'react'
import { clsx } from 'clsx'
import { Haptics } from '@utils/haptics'
import { useSettingsStore } from '@stores/useSettingsStore'

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'number' | 'operator' | 'function' | 'action' | 'equals'
  children: ReactNode
}

export function Button({ 
  variant = 'number', 
  children, 
  className, 
  onClick,
  ...props 
}: ButtonProps) {
  const hapticFeedback = useSettingsStore((state) => state.hapticFeedback)

  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    if (hapticFeedback) {
      Haptics.buttonTap()
    }
    onClick?.(e)
  }

  return (
    <button
      className={clsx(
        'flex items-center justify-center rounded-2xl h-16 font-bold transition-all',
        'hover:brightness-110 active:scale-95',
        {
          // 숫자 버튼
          'bg-white dark:bg-[#1a1f29] text-neutral-900 dark:text-white text-2xl shadow-sm': 
            variant === 'number',
          
          // 연산자 버튼
          'bg-primary/10 dark:bg-primary/20 text-primary text-2xl': 
            variant === 'operator',
          
          // 과학 함수 버튼
          'bg-neutral-100 dark:bg-[#282e39] text-neutral-700 dark:text-white text-sm': 
            variant === 'function',
          
          // 액션 버튼 (AC, ←, %)
          'bg-neutral-200 dark:bg-[#343b48] text-primary text-xl': 
            variant === 'action',
          
          // = 버튼
          'bg-primary text-white text-3xl shadow-lg shadow-primary/30 col-span-2': 
            variant === 'equals',
        },
        className
      )}
      onClick={handleClick}
      {...props}
    >
      {children}
    </button>
  )
}
```

---

## 💾 데이터 저장

### LocalStorage 구조

```typescript
// LocalStorage Keys
{
  "calculator-storage": {
    "state": {
      "isDegree": true
    },
    "version": 0
  },
  
  "history-storage": {
    "state": {
      "items": [
        {
          "id": "1703318400000",
          "expression": "sin(30) + 5",
          "result": 5.5,
          "timestamp": 1703318400000
        }
      ]
    },
    "version": 0
  },
  
  "settings-storage": {
    "state": {
      "theme": "dark",
      "precision": 10,
      "hapticFeedback": true
    },
    "version": 0
  }
}
```

---

## 🧪 테스트 전략

### 테스트 범위

#### ✅ 자동화 테스트 (TDD - 90% 커버리지)

**코어 로직**:
- `calculations.ts` - 계산 엔진
- `formatters.ts` - 포맷터
- `validators.ts` - 검증기
- `haptics.ts` - 햅틱 피드백

**상태 관리**:
- Store actions (비즈니스 로직만)

#### 🖱️ 수동 테스트

**UI 컴포넌트**:
- 버튼 인터랙션
- 애니메이션
- 반응형 레이아웃

**E2E 시나리오**:
- 계산 플로우
- 기록 저장/불러오기
- 설정 변경

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
    "lint": "eslint .",
    "type-check": "tsc --noEmit"
  }
}
```

---

## 🚀 빌드 및 배포

### GitHub Actions Workflow

이미 설정된 `.github/workflows/deploy.yml` 사용:
- Lint → Type Check → Test → Build → Deploy

### 배포 URL

```
https://hennessynlove7552.github.io/engineering-calculator/
```

---

## ⚡ 성능 최적화

### 1. 번들 최적화

```typescript
// vite.config.ts
rollupOptions: {
  output: {
    manualChunks: {
      'react-vendor': ['react', 'react-dom'],
      'math-vendor': ['mathjs'],
    }
  }
}
```

### 2. 메모이제이션

```typescript
// 계산 결과 캐싱
const result = useMemo(() => {
  return CalculationEngine.calculate(expression)
}, [expression])
```

### 3. PWA 캐싱

```typescript
// Service Worker로 오프라인 지원
// vite-plugin-pwa가 자동 생성
```

### 성능 목표

| 지표 | 목표 |
|------|------|
| Initial Load | < 2s |
| Time to Interactive | < 3s |
| Bundle Size | < 300KB |
| Lighthouse Score | > 90 |

---

**문서 버전**: 2.0.0  
**최종 수정일**: 2025-12-23  
**작성자**: Scientific Calculator Team
