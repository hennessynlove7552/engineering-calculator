# 공학용 계산기 - 기술 명세서
**Technical Design Document**

---

## 1. 문서 개요

### 1.1 목적
이 문서는 공학용 계산기 웹 애플리케이션 구현을 위한 상세한 기술 사양을 제공합니다. 기술 스택, 시스템 아키텍처, 컴포넌트 설계, 데이터 모델 및 구현 가이드라인을 정의합니다.

### 1.2 범위
- 프론트엔드 아키텍처 및 기술 스택
- 백엔드 서비스 (MVP의 경우 선택 사항)
- 데이터 모델 및 저장소
- API 설계
- 배포 및 인프라
- 개발 워크플로우 및 도구

### 1.3 대상 독자
- 프론트엔드 개발자
- 백엔드 개발자
- DevOps 엔지니어
- 기술 리드

---

## 2. 기술 스택

### 2.1 프론트엔드 스택

#### 핵심 프레임워크
**선택: React 18+ with TypeScript**

**선택 이유**:
- 강력한 생태계 및 커뮤니티 지원
- Virtual DOM을 통한 뛰어난 성능
- 타입 안전성과 더 나은 개발자 경험을 위한 TypeScript
- 풍부한 컴포넌트 라이브러리 생태계
- 빌드 도구와의 쉬운 통합

**고려한 대안**:
- Vue.js 3: 좋은 옵션이지만 생태계가 작음
- Svelte: 뛰어난 성능이지만 생태계가 덜 성숙함

#### 빌드 도구
**선택: Vite 5+**

**기능**:
- 초고속 HMR (Hot Module Replacement)
- Rollup을 사용한 최적화된 프로덕션 빌드
- 네이티브 ESM 지원
- 기본 TypeScript 지원
- 플러그인 생태계

**구성**:
```javascript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@types': path.resolve(__dirname, './src/types'),
    }
  },
  build: {
    target: 'es2015',
    minify: 'terser',
    sourcemap: true
  }
})
```

#### 스타일링 솔루션
**선택: Tailwind CSS 3.4+**

**선택 이유**:
- 기존 디자인 구현과 일치
- 빠른 개발을 위한 유틸리티 우선 접근 방식
- 작은 번들 크기를 위한 뛰어난 퍼징
- 내장 다크 모드 지원
- 사용자 정의 가능한 디자인 시스템

**구성**:
```javascript
// tailwind.config.js
module.exports = {
  darkMode: 'class',
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: '#137fec',
        'primary-hover': '#116ecf',
        'background-light': '#f6f7f8',
        'background-dark': '#111a22',
        'surface-dark': '#1a2632',
        'surface-border': '#233648',
        'text-secondary': '#92adc9',
      },
      fontFamily: {
        display: ['Space Grotesk', 'sans-serif'],
        body: ['Noto Sans', 'sans-serif'],
        mono: ['ui-monospace', 'SFMono-Regular', 'monospace'],
      },
    },
  },
  plugins: [require('@tailwindcss/forms')],
}
```

#### 상태 관리
**선택: Zustand**

**선택 이유**:
- 경량 (< 1KB)
- 간단한 API, 최소한의 보일러플레이트
- 컨텍스트 프로바이더 불필요
- TypeScript 우선 설계
- 소규모에서 중규모 앱에 완벽

**대안 고려**:
- Redux Toolkit: 이 사용 사례에는 너무 무거움
- Jotai/Recoil: 원자적 접근 방식이 여기서는 필요하지 않음

**스토어 구조**:
```typescript
// stores/useFormulaStore.ts
interface FormulaStore {
  selectedFormula: Formula | null
  selectedCategory: string
  searchQuery: string
  favorites: string[]
  history: CalculationHistory[]
  
  setSelectedFormula: (formula: Formula) => void
  setCategory: (category: string) => void
  setSearchQuery: (query: string) => void
  toggleFavorite: (formulaId: string) => void
  addToHistory: (calculation: CalculationHistory) => void
}
```

#### 라우팅
**선택: React Router 6+**

**라우트**:
```typescript
const routes = [
  { path: '/', element: <Dashboard /> },
  { path: '/library', element: <Library /> },
  { path: '/library/:category', element: <Library /> },
  { path: '/formula/:id', element: <FormulaDetail /> },
  { path: '/history', element: <History /> },
  { path: '/settings', element: <Settings /> },
  { path: '*', element: <NotFound /> },
]
```

#### 폼 처리
**선택: React Hook Form**

**기능**:
- 최소한의 리렌더링
- 내장 검증
- TypeScript 지원
- 작은 번들 크기

#### 아이콘 및 폰트
- **아이콘**: Material Symbols (Google Fonts)
- **폰트**: 
  - Space Grotesk (display)
  - Noto Sans (body)
  - 시스템 모노스페이스 (code)

#### 추가 라이브러리
| 라이브러리 | 목적 | 버전 |
|---------|---------|---------|
| `mathjs` | 수학 계산 | 12+ |
| `framer-motion` | 애니메이션 | 11+ |
| `react-hot-toast` | 알림 | 2+ |
| `clsx` | 조건부 클래스명 | 2+ |
| `date-fns` | 날짜 포맷팅 | 3+ |

### 2.2 백엔드 스택 (MVP의 경우 선택 사항)

#### 런타임
**선택: Node.js 20+ with Express.js**

**선택 이유**:
- 스택 전체에서 JavaScript/TypeScript 일관성
- 대규모 생태계
- 쉬운 배포
- I/O 작업에 대한 좋은 성능

#### 데이터베이스
**선택: PostgreSQL 16+ with Prisma ORM**

**선택 이유**:
- 사용자 데이터에 대한 ACID 준수
- 유연한 공식 저장을 위한 JSON 지원
- 뛰어난 성능
- Prisma는 타입 안전 데이터베이스 액세스 제공

**스키마**:
```prisma
// schema.prisma
model User {
  id          String   @id @default(uuid())
  email       String   @unique
  name        String?
  preferences Json?
  favorites   String[]
  history     Calculation[]
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model Calculation {
  id         String   @id @default(uuid())
  userId     String
  user       User     @relation(fields: [userId], references: [id])
  formulaId  String
  inputs     Json
  result     Float
  timestamp  DateTime @default(now())
}

model Formula {
  id          String   @id
  name        String
  category    String
  equation    String
  description String
  variables   Json
  metadata    Json
  createdAt   DateTime @default(now())
}
```

#### 인증
**선택: Clerk 또는 Auth0**

**기능**:
- OAuth 제공자 (Google, GitHub)
- 세션 관리
- 사용자 관리 UI
- React와의 쉬운 통합

#### API 프레임워크
**선택: tRPC**

**선택 이유**:
- 엔드투엔드 타입 안전성
- 코드 생성 불필요
- TypeScript와의 뛰어난 DX
- 모노레포 설정에 완벽

### 2.3 개발 도구

#### 패키지 매니저
**선택: pnpm**

**선택 이유**:
- npm/yarn보다 빠름
- 효율적인 디스크 공간 사용
- 엄격한 종속성 해결

#### 코드 품질
| 도구 | 목적 |
|------|---------|
| ESLint | 린팅 |
| Prettier | 코드 포맷팅 |
| Husky | Git 훅 |
| lint-staged | 프리커밋 린팅 |
| TypeScript | 타입 체킹 |

#### 테스팅
| 도구 | 목적 |
|------|---------|
| Vitest | 단위 테스트 |
| React Testing Library | 컴포넌트 테스트 |
| Playwright | E2E 테스트 |

#### CI/CD
**선택: GitHub Actions**

**파이프라인**:
1. 린트 및 타입 체크
2. 단위 테스트 실행
3. 프로덕션 번들 빌드
4. E2E 테스트 실행
5. Vercel/Netlify에 배포

---

## 3. 시스템 아키텍처

### 3.1 고수준 아키텍처

```
┌─────────────────────────────────────────────────────┐
│                   클라이언트 브라우저                  │
│  ┌───────────────────────────────────────────────┐  │
│  │         React SPA (TypeScript)                │  │
│  │  ┌─────────────┐  ┌──────────────────────┐   │  │
│  │  │   UI 레이어  │  │   상태 관리          │   │  │
│  │  │ (컴포넌트)   │  │     (Zustand)        │   │  │
│  │  └─────────────┘  └──────────────────────┘   │  │
│  │  ┌─────────────────────────────────────────┐ │  │
│  │  │      계산 엔진 (mathjs)                  │ │  │
│  │  └─────────────────────────────────────────┘ │  │
│  └───────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
                        │
                        │ HTTPS
                        ▼
┌─────────────────────────────────────────────────────┐
│                  백엔드 서비스                        │
│                   (MVP의 경우 선택 사항)              │
│  ┌───────────────────────────────────────────────┐  │
│  │         API 레이어 (Express + tRPC)           │  │
│  │  ┌─────────────┐  ┌──────────────────────┐   │  │
│  │  │ 인증 서비스  │  │  공식 서비스         │   │  │
│  │  └─────────────┘  └──────────────────────┘   │  │
│  │  ┌─────────────┐  ┌──────────────────────┐   │  │
│  │  │ 사용자 서비스│  │  계산 서비스         │   │  │
│  │  └─────────────┘  └──────────────────────┘   │  │
│  └───────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────┐  │
│  │       데이터베이스 (PostgreSQL + Prisma)       │  │
│  └───────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

### 3.2 프론트엔드 아키텍처

#### 디렉토리 구조
```
src/
├── components/           # React 컴포넌트
│   ├── layout/          # 레이아웃 컴포넌트
│   │   ├── Header.tsx
│   │   ├── Sidebar.tsx
│   │   └── MainLayout.tsx
│   ├── formula/         # 공식 관련 컴포넌트
│   │   ├── FormulaCard.tsx
│   │   ├── FormulaList.tsx
│   │   ├── FormulaDetail.tsx
│   │   └── FormulaSearch.tsx
│   ├── calculator/      # 계산기 컴포넌트
│   │   ├── VariableInput.tsx
│   │   ├── ResultPanel.tsx
│   │   └── Calculator.tsx
│   ├── ui/              # 재사용 가능한 UI 컴포넌트
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Select.tsx
│   │   └── Card.tsx
│   └── common/          # 공통 컴포넌트
│       ├── Loading.tsx
│       ├── ErrorBoundary.tsx
│       └── NotFound.tsx
├── pages/               # 페이지 컴포넌트
│   ├── Dashboard.tsx
│   ├── Library.tsx
│   ├── History.tsx
│   └── Settings.tsx
├── stores/              # 상태 관리
│   ├── useFormulaStore.ts
│   ├── useUserStore.ts
│   └── useCalculatorStore.ts
├── utils/               # 유틸리티 함수
│   ├── calculations.ts
│   ├── unitConversion.ts
│   ├── formatters.ts
│   └── validators.ts
├── hooks/               # 커스텀 React 훅
│   ├── useFormula.ts
│   ├── useCalculator.ts
│   └── useLocalStorage.ts
├── types/               # TypeScript 타입
│   ├── formula.ts
│   ├── calculation.ts
│   └── user.ts
├── data/                # 정적 데이터
│   ├── formulas.json
│   ├── categories.json
│   └── units.json
├── constants/           # 상수
│   ├── categories.ts
│   └── units.ts
├── App.tsx              # 루트 컴포넌트
├── main.tsx             # 진입점
└── vite-env.d.ts        # Vite 타입
```

### 3.3 컴포넌트 아키텍처

#### 컴포넌트 계층 구조
```
App
├── MainLayout
│   ├── Header
│   │   ├── Logo
│   │   ├── Navigation
│   │   └── UserMenu
│   ├── Sidebar
│   │   ├── CategoryList
│   │   └── LibraryFilters
│   ├── FormulaList
│   │   ├── SearchBar
│   │   ├── FormulaCard (다수)
│   │   └── SortControls
│   └── FormulaDetail
│       ├── Breadcrumbs
│       ├── FormulaHeader
│       ├── EquationDisplay
│       ├── Calculator
│       │   ├── VariableInput (다수)
│       │   └── ResultPanel
│       └── AdditionalInfo
│           ├── EngineeringNotes
│           └── RelatedFormulas
```

---

## 4. 데이터 모델

### 4.1 공식 모델

```typescript
// types/formula.ts
export interface Formula {
  id: string
  name: string
  category: FormulaCategory
  subcategory: string
  equation: string
  description: string
  variables: Variable[]
  notes?: string
  relatedFormulas: string[]
  tags: string[]
  difficulty: 'beginner' | 'intermediate' | 'advanced'
  metadata: {
    author?: string
    source?: string
    lastUpdated: string
  }
}

export interface Variable {
  symbol: string
  name: string
  type: 'scalar' | 'vector' | 'matrix'
  units: Unit[]
  defaultUnit: string
  description: string
  constraints?: {
    min?: number
    max?: number
    positive?: boolean
  }
}

export interface Unit {
  symbol: string
  name: string
  conversionFactor: number
  baseUnit: string
}

export type FormulaCategory = 
  | 'mathematics'
  | 'physics'
  | 'chemistry'
  | 'electrical'
  | 'civil'
  | 'mechanical'
```

### 4.2 계산 모델

```typescript
// types/calculation.ts
export interface Calculation {
  id: string
  formulaId: string
  timestamp: Date
  inputs: Record<string, CalculationInput>
  result: CalculationResult
  userId?: string
}

export interface CalculationInput {
  value: number
  unit: string
  variable: string
}

export interface CalculationResult {
  value: number
  unit: string
  variable: string
  precision: number
}
```

### 4.3 사용자 모델

```typescript
// types/user.ts
export interface User {
  id: string
  email: string
  name?: string
  avatar?: string
  preferences: UserPreferences
  favorites: string[]
  createdAt: Date
}

export interface UserPreferences {
  unitSystem: 'SI' | 'Imperial'
  decimalPrecision: number
  theme: 'light' | 'dark' | 'system'
  language: string
}
```

---

## 5. 핵심 기능 구현

### 5.1 계산 엔진

```typescript
// utils/calculations.ts
import { create, all } from 'mathjs'

const math = create(all)

export class CalculationEngine {
  /**
   * 주어진 입력으로 공식 평가
   */
  static calculate(
    formula: Formula,
    inputs: Record<string, CalculationInput>
  ): CalculationResult {
    try {
      // 모든 입력을 기본 단위로 변환
      const normalizedInputs = this.normalizeUnits(formula, inputs)
      
      // 방정식 파싱 및 평가
      const scope = this.createScope(normalizedInputs)
      const result = math.evaluate(formula.equation, scope)
      
      // 결과를 원하는 단위로 변환
      return this.formatResult(result, formula)
    } catch (error) {
      throw new CalculationError('계산 실패', error)
    }
  }
  
  /**
   * 입력을 기본 단위로 변환
   */
  private static normalizeUnits(
    formula: Formula,
    inputs: Record<string, CalculationInput>
  ): Record<string, number> {
    const normalized: Record<string, number> = {}
    
    for (const [key, input] of Object.entries(inputs)) {
      const variable = formula.variables.find(v => v.symbol === key)
      if (!variable) continue
      
      const unit = variable.units.find(u => u.symbol === input.unit)
      if (!unit) continue
      
      normalized[key] = input.value * unit.conversionFactor
    }
    
    return normalized
  }
  
  /**
   * mathjs 평가를 위한 스코프 생성
   */
  private static createScope(
    inputs: Record<string, number>
  ): Record<string, number> {
    return { ...inputs }
  }
  
  /**
   * 적절한 정밀도로 결과 포맷
   */
  private static formatResult(
    value: number,
    formula: Formula
  ): CalculationResult {
    return {
      value: Number(value.toFixed(4)),
      unit: this.getResultUnit(formula),
      variable: this.getResultVariable(formula),
      precision: 4
    }
  }
}
```

### 5.2 단위 변환 시스템

```typescript
// utils/unitConversion.ts
export interface UnitDefinition {
  symbol: string
  name: string
  type: UnitType
  toBase: (value: number) => number
  fromBase: (value: number) => number
}

export type UnitType = 
  | 'length' | 'mass' | 'time' | 'force' 
  | 'energy' | 'power' | 'temperature'

export class UnitConverter {
  private static units: Map<string, UnitDefinition> = new Map()
  
  /**
   * 단위 정의 등록
   */
  static registerUnit(unit: UnitDefinition): void {
    this.units.set(unit.symbol, unit)
  }
  
  /**
   * 한 단위에서 다른 단위로 값 변환
   */
  static convert(
    value: number,
    fromUnit: string,
    toUnit: string
  ): number {
    const from = this.units.get(fromUnit)
    const to = this.units.get(toUnit)
    
    if (!from || !to) {
      throw new Error(`알 수 없는 단위: ${fromUnit} 또는 ${toUnit}`)
    }
    
    if (from.type !== to.type) {
      throw new Error('다른 단위 유형 간 변환 불가')
    }
    
    // 기본 단위로 변환한 다음 대상 단위로 변환
    const baseValue = from.toBase(value)
    return to.fromBase(baseValue)
  }
}

// 단위 정의
UnitConverter.registerUnit({
  symbol: 'kg',
  name: '킬로그램',
  type: 'mass',
  toBase: (v) => v,
  fromBase: (v) => v
})

UnitConverter.registerUnit({
  symbol: 'g',
  name: '그램',
  type: 'mass',
  toBase: (v) => v / 1000,
  fromBase: (v) => v * 1000
})

UnitConverter.registerUnit({
  symbol: 'lb',
  name: '파운드',
  type: 'mass',
  toBase: (v) => v * 0.453592,
  fromBase: (v) => v / 0.453592
})
```

### 5.3 공식 검색 및 필터

```typescript
// utils/formulaSearch.ts
export class FormulaSearch {
  /**
   * 쿼리로 공식 검색
   */
  static search(
    formulas: Formula[],
    query: string,
    options: SearchOptions = {}
  ): Formula[] {
    const normalizedQuery = query.toLowerCase().trim()
    
    if (!normalizedQuery) return formulas
    
    return formulas.filter(formula => {
      // 이름에서 검색
      if (formula.name.toLowerCase().includes(normalizedQuery)) {
        return true
      }
      
      // 카테고리에서 검색
      if (formula.category.toLowerCase().includes(normalizedQuery)) {
        return true
      }
      
      // 태그에서 검색
      if (formula.tags.some(tag => 
        tag.toLowerCase().includes(normalizedQuery)
      )) {
        return true
      }
      
      // 방정식에서 검색
      if (options.searchEquation && 
          formula.equation.toLowerCase().includes(normalizedQuery)) {
        return true
      }
      
      return false
    })
  }
  
  /**
   * 카테고리별로 공식 필터링
   */
  static filterByCategory(
    formulas: Formula[],
    category: FormulaCategory | 'all'
  ): Formula[] {
    if (category === 'all') return formulas
    return formulas.filter(f => f.category === category)
  }
  
  /**
   * 공식 정렬
   */
  static sort(
    formulas: Formula[],
    sortBy: SortOption
  ): Formula[] {
    const sorted = [...formulas]
    
    switch (sortBy) {
      case 'alphabetical':
        return sorted.sort((a, b) => a.name.localeCompare(b.name))
      case 'category':
        return sorted.sort((a, b) => a.category.localeCompare(b.category))
      default:
        return sorted
    }
  }
}

export interface SearchOptions {
  searchEquation?: boolean
  searchDescription?: boolean
}

export type SortOption = 'alphabetical' | 'category' | 'recent' | 'popular'
```

---

## 6. API 설계 (선택적 백엔드)

### 6.1 REST API 엔드포인트

#### 인증
```
POST   /api/auth/register        # 새 사용자 등록
POST   /api/auth/login           # 사용자 로그인
POST   /api/auth/logout          # 사용자 로그아웃
GET    /api/auth/me              # 현재 사용자 가져오기
```

#### 공식
```
GET    /api/formulas             # 모든 공식 가져오기
GET    /api/formulas/:id         # ID로 공식 가져오기
GET    /api/formulas/category/:category  # 카테고리별로 가져오기
POST   /api/formulas/search      # 공식 검색
```

#### 사용자 데이터
```
GET    /api/users/favorites      # 사용자 즐겨찾기 가져오기
POST   /api/users/favorites/:id  # 즐겨찾기에 추가
DELETE /api/users/favorites/:id  # 즐겨찾기에서 제거
GET    /api/users/history        # 계산 히스토리 가져오기
POST   /api/users/history        # 계산 저장
```

### 6.2 tRPC API (권장)

```typescript
// server/routers/formula.ts
import { z } from 'zod'
import { router, publicProcedure } from '../trpc'

export const formulaRouter = router({
  getAll: publicProcedure
    .query(async ({ ctx }) => {
      return await ctx.prisma.formula.findMany()
    }),
    
  getById: publicProcedure
    .input(z.string())
    .query(async ({ ctx, input }) => {
      return await ctx.prisma.formula.findUnique({
        where: { id: input }
      })
    }),
    
  search: publicProcedure
    .input(z.object({
      query: z.string(),
      category: z.string().optional()
    }))
    .query(async ({ ctx, input }) => {
      return await ctx.prisma.formula.findMany({
        where: {
          OR: [
            { name: { contains: input.query, mode: 'insensitive' } },
            { category: { contains: input.query, mode: 'insensitive' } }
          ],
          ...(input.category && { category: input.category })
        }
      })
    })
})
```

---

## 7. 성능 최적화

### 7.1 코드 스플리팅

```typescript
// 페이지 지연 로딩
const Dashboard = lazy(() => import('./pages/Dashboard'))
const Library = lazy(() => import('./pages/Library'))
const History = lazy(() => import('./pages/History'))
const Settings = lazy(() => import('./pages/Settings'))

// 라우트 기반 코드 스플리팅
<Suspense fallback={<Loading />}>
  <Routes>
    <Route path="/" element={<Dashboard />} />
    <Route path="/library" element={<Library />} />
    <Route path="/history" element={<History />} />
    <Route path="/settings" element={<Settings />} />
  </Routes>
</Suspense>
```

### 7.2 메모이제이션

```typescript
// 비용이 많이 드는 계산 메모이제이션
const filteredFormulas = useMemo(() => {
  return FormulaSearch.search(formulas, searchQuery)
}, [formulas, searchQuery])

// 콜백 메모이제이션
const handleCalculate = useCallback((inputs: Record<string, number>) => {
  const result = CalculationEngine.calculate(formula, inputs)
  setResult(result)
}, [formula])
```

### 7.3 가상 스크롤링

```typescript
// 큰 공식 목록의 경우
import { useVirtualizer } from '@tanstack/react-virtual'

const FormulaList = ({ formulas }: { formulas: Formula[] }) => {
  const parentRef = useRef<HTMLDivElement>(null)
  
  const virtualizer = useVirtualizer({
    count: formulas.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 120,
  })
  
  return (
    <div ref={parentRef} className="h-full overflow-auto">
      <div style={{ height: `${virtualizer.getTotalSize()}px` }}>
        {virtualizer.getVirtualItems().map(virtualItem => (
          <FormulaCard
            key={formulas[virtualItem.index].id}
            formula={formulas[virtualItem.index]}
          />
        ))}
      </div>
    </div>
  )
}
```

---

## 8. 보안 고려사항

### 8.1 입력 검증

```typescript
// 모든 사용자 입력 검증
export const validateInput = (
  value: number,
  variable: Variable
): ValidationResult => {
  if (isNaN(value)) {
    return { valid: false, error: '유효하지 않은 숫자' }
  }
  
  if (variable.constraints) {
    const { min, max, positive } = variable.constraints
    
    if (positive && value < 0) {
      return { valid: false, error: '값은 양수여야 합니다' }
    }
    
    if (min !== undefined && value < min) {
      return { valid: false, error: `값은 ${min} 이상이어야 합니다` }
    }
    
    if (max !== undefined && value > max) {
      return { valid: false, error: `값은 ${max} 이하여야 합니다` }
    }
  }
  
  return { valid: true }
}
```

### 8.2 XSS 방지

```typescript
// 표시 전 공식 방정식 살균
import DOMPurify from 'dompurify'

export const sanitizeEquation = (equation: string): string => {
  return DOMPurify.sanitize(equation, {
    ALLOWED_TAGS: ['sup', 'sub', 'i', 'b'],
    ALLOWED_ATTR: []
  })
}
```

### 8.3 속도 제한 (백엔드)

```typescript
// API 호출 속도 제한
import rateLimit from 'express-rate-limit'

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15분
  max: 100, // 각 IP를 windowMs당 100개 요청으로 제한
  message: '이 IP에서 너무 많은 요청'
})

app.use('/api/', limiter)
```

---

## 9. 테스팅 전략

### 9.1 단위 테스트

```typescript
// utils/calculations.test.ts
import { describe, it, expect } from 'vitest'
import { CalculationEngine } from './calculations'

describe('CalculationEngine', () => {
  it('뉴턴의 제2법칙을 올바르게 계산해야 함', () => {
    const formula = getFormula('newtons-second-law')
    const inputs = {
      m: { value: 15.5, unit: 'kg', variable: 'm' },
      a: { value: 9.81, unit: 'm/s²', variable: 'a' }
    }
    
    const result = CalculationEngine.calculate(formula, inputs)
    
    expect(result.value).toBeCloseTo(152.055, 2)
    expect(result.unit).toBe('N')
  })
  
  it('단위 변환을 처리해야 함', () => {
    const result = UnitConverter.convert(1, 'kg', 'g')
    expect(result).toBe(1000)
  })
})
```

### 9.2 컴포넌트 테스트

```typescript
// components/Calculator.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { Calculator } from './Calculator'

describe('Calculator', () => {
  it('모든 변수에 대한 입력 필드를 렌더링해야 함', () => {
    const formula = getFormula('newtons-second-law')
    render(<Calculator formula={formula} />)
    
    expect(screen.getByLabelText(/질량/i)).toBeInTheDocument()
    expect(screen.getByLabelText(/가속도/i)).toBeInTheDocument()
  })
  
  it('버튼 클릭 시 결과를 계산해야 함', async () => {
    const formula = getFormula('newtons-second-law')
    render(<Calculator formula={formula} />)
    
    fireEvent.change(screen.getByLabelText(/질량/i), {
      target: { value: '15.5' }
    })
    fireEvent.change(screen.getByLabelText(/가속도/i), {
      target: { value: '9.81' }
    })
    fireEvent.click(screen.getByText(/계산/i))
    
    expect(await screen.findByText(/152.05/)).toBeInTheDocument()
  })
})
```

### 9.3 E2E 테스트

```typescript
// e2e/formula-calculation.spec.ts
import { test, expect } from '@playwright/test'

test('사용자가 뉴턴의 제2법칙을 사용하여 힘을 계산할 수 있음', async ({ page }) => {
  await page.goto('/library/physics')
  
  // 공식 선택
  await page.click('text=뉴턴의 제2법칙')
  
  // 값 입력
  await page.fill('input[name="m"]', '15.5')
  await page.fill('input[name="a"]', '9.81')
  
  // 계산
  await page.click('button:has-text("계산")')
  
  // 결과 확인
  await expect(page.locator('.result-value')).toContainText('152.05')
})
```

---

## 10. 배포

### 10.1 프론트엔드 배포 (GitHub Pages)

**Vite 구성**:
```javascript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig(({ mode }) => ({
  plugins: [react()],
  base: mode === 'production' ? '/engineering-calculator/' : '/',
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@types': path.resolve(__dirname, './src/types'),
    }
  },
  build: {
    target: 'es2015',
    minify: 'terser',
    sourcemap: true,
    outDir: 'dist'
  }
}))
```

**GitHub Pages 설정**:
1. 저장소 Settings > Pages에서 Source를 "GitHub Actions"로 설정
2. `gh-pages` 브랜치가 자동으로 생성되어 배포됨
3. 커스텀 도메인 설정 가능 (선택사항)

### 10.2 환경 변수

```bash
# .env.example
VITE_API_URL=https://api.engineeringcalc.com
VITE_AUTH_DOMAIN=engineeringcalc.clerk.app
VITE_ANALYTICS_ID=G-XXXXXXXXXX
```

### 10.3 CI/CD 파이프라인 (GitHub Actions)

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

# GitHub Pages 배포를 위한 권한 설정
permissions:
  contents: read
  pages: write
  id-token: write

# 동시 배포 방지
concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  # 빌드 작업
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      
      - name: Install dependencies
        run: pnpm install --frozen-lockfile
      
      - name: Lint
        run: pnpm lint
      
      - name: Type check
        run: pnpm type-check
      
      - name: Run tests
        run: pnpm test:run
      
      - name: Build
        run: pnpm build
        env:
          NODE_ENV: production
      
      - name: Setup Pages
        uses: actions/configure-pages@v4
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'
  
  # 배포 작업 (main 브랜치에만)
  deploy:
    if: github.ref == 'refs/heads/main'
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**추가 워크플로우 - PR 체크**:
```yaml
# .github/workflows/pr-check.yml
name: PR Check

on:
  pull_request:
    branches: [main, develop]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      
      - name: Install dependencies
        run: pnpm install --frozen-lockfile
      
      - name: Lint
        run: pnpm lint
      
      - name: Type check
        run: pnpm type-check
      
      - name: Run tests
        run: pnpm test:run
      
      - name: Build
        run: pnpm build
```

---

## 11. 개발 워크플로우

### 11.1 Git 워크플로우

**브랜치 전략**:
- `main`: 프로덕션 준비 코드
- `develop`: 통합 브랜치
- `feature/*`: 기능 브랜치
- `bugfix/*`: 버그 수정 브랜치
- `hotfix/*`: 프로덕션 핫픽스

**커밋 규칙**:
```
feat: 새 공식 카테고리 추가
fix: 온도 단위 변환 수정
docs: API 문서 업데이트
style: Prettier로 코드 포맷
refactor: 계산 엔진 단순화
test: 공식 검색 테스트 추가
chore: 종속성 업데이트
```

### 11.2 코드 리뷰 체크리스트

- [ ] 코드가 TypeScript 모범 사례를 따름
- [ ] 모든 테스트 통과
- [ ] console.log 또는 디버깅 코드 없음
- [ ] 적절한 오류 처리
- [ ] 접근성 고려사항
- [ ] 성능 최적화 적용
- [ ] 문서 업데이트

### 11.3 릴리스 프로세스

1. `develop`에서 릴리스 브랜치 생성
2. `package.json`에서 버전 업데이트
3. CHANGELOG.md 업데이트
4. 전체 테스트 스위트 실행
5. `main`으로 풀 리퀘스트 생성
6. 승인 후 병합 및 릴리스 태그
7. 프로덕션에 배포
8. 문제 모니터링

---

## 12. 모니터링 및 분석

### 12.1 오류 추적
**도구**: Sentry

```typescript
// main.tsx
import * as Sentry from '@sentry/react'

Sentry.init({
  dsn: import.meta.env.VITE_SENTRY_DSN,
  environment: import.meta.env.MODE,
  tracesSampleRate: 1.0,
})
```

### 12.2 분석
**도구**: Google Analytics 4

```typescript
// utils/analytics.ts
export const trackEvent = (
  eventName: string,
  params?: Record<string, any>
) => {
  if (window.gtag) {
    window.gtag('event', eventName, params)
  }
}

// 공식 사용 추적
trackEvent('formula_viewed', {
  formula_id: formula.id,
  category: formula.category
})

// 계산 추적
trackEvent('calculation_performed', {
  formula_id: formula.id,
  success: true
})
```

### 12.3 성능 모니터링

```typescript
// utils/performance.ts
export const measurePerformance = (name: string) => {
  const start = performance.now()
  
  return () => {
    const duration = performance.now() - start
    console.log(`${name} took ${duration}ms`)
    
    // 분석으로 전송
    trackEvent('performance', {
      metric: name,
      duration: Math.round(duration)
    })
  }
}

// 사용법
const endMeasure = measurePerformance('formula_calculation')
const result = CalculationEngine.calculate(formula, inputs)
endMeasure()
```

---

## 13. 접근성 구현

### 13.1 키보드 네비게이션

```typescript
// components/FormulaCard.tsx
const FormulaCard = ({ formula, onClick }: Props) => {
  const handleKeyPress = (e: KeyboardEvent) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault()
      onClick(formula)
    }
  }
  
  return (
    <div
      role="button"
      tabIndex={0}
      onClick={() => onClick(formula)}
      onKeyPress={handleKeyPress}
      aria-label={`${formula.name} 공식 보기`}
    >
      {/* 카드 내용 */}
    </div>
  )
}
```

### 13.2 ARIA 레이블

```typescript
// 상호작용 요소에 대한 적절한 ARIA 레이블
<button
  aria-label="즐겨찾기에 추가"
  aria-pressed={isFavorite}
>
  <StarIcon />
</button>

<input
  type="number"
  aria-label="킬로그램 단위의 질량"
  aria-describedby="mass-help"
/>
<span id="mass-help" className="sr-only">
  킬로그램 단위로 질량 값을 입력하세요
</span>
```

---

## 14. 부록

### 14.1 Package.json

```json
{
  "name": "engineering-calculator",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "format": "prettier --write \"src/**/*.{ts,tsx,css}\"",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:e2e": "playwright test"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "react-router-dom": "^6.22.0",
    "zustand": "^4.5.0",
    "mathjs": "^12.4.0",
    "react-hook-form": "^7.50.0",
    "framer-motion": "^11.0.0",
    "react-hot-toast": "^2.4.1",
    "clsx": "^2.1.0",
    "date-fns": "^3.3.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.55",
    "@types/react-dom": "^18.2.19",
    "@typescript-eslint/eslint-plugin": "^6.21.0",
    "@typescript-eslint/parser": "^6.21.0",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.17",
    "eslint": "^8.56.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.5",
    "postcss": "^8.4.35",
    "prettier": "^3.2.5",
    "tailwindcss": "^3.4.1",
    "typescript": "^5.3.3",
    "vite": "^5.1.0",
    "vitest": "^1.2.2",
    "@testing-library/react": "^14.2.1",
    "@testing-library/jest-dom": "^6.4.2",
    "@playwright/test": "^1.41.2"
  }
}
```

### 14.2 TypeScript 구성

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@utils/*": ["./src/utils/*"],
      "@types/*": ["./src/types/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

---

**문서 버전**: 1.0  
**최종 업데이트**: 2025-12-23  
**작성자**: 엔지니어링 팀  
**상태**: 구현 준비 완료
