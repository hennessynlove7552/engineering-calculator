# TDD (Test-Driven Development) 규칙

이 프로젝트의 모든 코어 로직은 TDD(테스트 주도 개발) 방식으로 구현해야 합니다.

## 적용 범위

**TDD를 적용해야 하는 코드:**
- ✅ 계산 엔진 (`utils/calculations.ts`)
- ✅ 단위 변환 시스템 (`utils/unitConversion.ts`)
- ✅ 공식 검색 및 필터링 (`utils/formulaSearch.ts`)
- ✅ 데이터 검증 로직 (`utils/validators.ts`)
- ✅ 포맷팅 유틸리티 (`utils/formatters.ts`)
- ✅ 비즈니스 로직을 포함한 모든 유틸리티 함수
- ✅ 상태 관리 스토어의 비즈니스 로직

**TDD를 적용하지 않는 코드:**
- ❌ UI 컴포넌트 (React 컴포넌트) - 수동 테스트로 검증
- ❌ 스타일링 코드
- ❌ 레이아웃 컴포넌트
- ❌ 페이지 컴포넌트

> **참고**: UI 컴포넌트는 자동화된 테스트를 작성하지 않습니다. 대신 브라우저에서 수동으로 동작을 확인합니다.

## TDD 개발 프로세스

### 1. Red (실패하는 테스트 작성)
```typescript
// 예시: utils/calculations.test.ts
describe('CalculationEngine', () => {
  it('should calculate force using Newton\'s second law', () => {
    const formula = getFormula('newtons-second-law')
    const inputs = {
      m: { value: 10, unit: 'kg', variable: 'm' },
      a: { value: 5, unit: 'm/s²', variable: 'a' }
    }
    
    const result = CalculationEngine.calculate(formula, inputs)
    
    expect(result.value).toBe(50)
    expect(result.unit).toBe('N')
  })
})
```

### 2. Green (테스트를 통과하는 최소한의 코드 작성)
```typescript
// utils/calculations.ts
export class CalculationEngine {
  static calculate(formula: Formula, inputs: Record<string, CalculationInput>): CalculationResult {
    // 테스트를 통과하는 최소한의 구현
    return {
      value: inputs.m.value * inputs.a.value,
      unit: 'N',
      variable: 'F',
      precision: 2
    }
  }
}
```

### 3. Refactor (코드 개선)
```typescript
// 리팩토링: 더 일반적이고 확장 가능한 구현
export class CalculationEngine {
  static calculate(formula: Formula, inputs: Record<string, CalculationInput>): CalculationResult {
    const normalizedInputs = this.normalizeUnits(formula, inputs)
    const scope = this.createScope(normalizedInputs)
    const result = math.evaluate(formula.equation, scope)
    return this.formatResult(result, formula)
  }
  
  // ... 헬퍼 메서드들
}
```

## 테스트 작성 가이드라인

### 테스트 구조
```typescript
describe('ClassName or FunctionName', () => {
  // 테스트 전 설정
  beforeEach(() => {
    // 공통 설정
  })
  
  // 정상 케이스
  it('should [expected behavior] when [condition]', () => {
    // Arrange (준비)
    const input = createTestInput()
    
    // Act (실행)
    const result = functionUnderTest(input)
    
    // Assert (검증)
    expect(result).toBe(expectedValue)
  })
  
  // 엣지 케이스
  it('should handle edge case: [description]', () => {
    // ...
  })
  
  // 에러 케이스
  it('should throw error when [invalid condition]', () => {
    expect(() => functionUnderTest(invalidInput)).toThrow()
  })
})
```

### 테스트 커버리지 목표
- **코어 로직**: 최소 90% 커버리지
- **유틸리티 함수**: 최소 85% 커버리지
- **엣지 케이스**: 모든 주요 엣지 케이스 커버
- **UI 컴포넌트**: 0% (자동화 테스트 없음, 수동 검증만)

### 테스트 명명 규칙
- 테스트 파일: `*.test.ts` 또는 `*.spec.ts`
- 테스트 설명: 명확하고 구체적으로 작성
  - ✅ `should calculate force correctly with positive values`
  - ✅ `should throw error when mass is negative`
  - ❌ `test1`, `it works`

## 테스트 도구

### 사용 도구
- **테스트 러너**: Vitest
- **어설션 라이브러리**: Vitest (내장)
- **모킹**: Vitest (vi.mock, vi.fn)
- **커버리지**: Vitest (내장 c8)

### 테스트 실행 명령어
```bash
# 모든 테스트 실행
pnpm test

# Watch 모드
pnpm test:watch

# 커버리지 리포트
pnpm test:coverage

# 특정 파일만 테스트
pnpm test calculations.test.ts
```

## 예시: 완전한 TDD 사이클

### 1단계: 요구사항 정의
> "단위 변환기는 킬로그램을 그램으로 변환할 수 있어야 한다"

### 2단계: 테스트 작성 (Red)
```typescript
// utils/unitConversion.test.ts
describe('UnitConverter', () => {
  it('should convert kilograms to grams', () => {
    const result = UnitConverter.convert(1, 'kg', 'g')
    expect(result).toBe(1000)
  })
  
  it('should convert grams to kilograms', () => {
    const result = UnitConverter.convert(1000, 'g', 'kg')
    expect(result).toBe(1)
  })
  
  it('should throw error for incompatible units', () => {
    expect(() => {
      UnitConverter.convert(1, 'kg', 'm')
    }).toThrow('Cannot convert between different unit types')
  })
})
```

### 3단계: 구현 (Green)
```typescript
// utils/unitConversion.ts
export class UnitConverter {
  private static units = new Map([
    ['kg', { type: 'mass', toBase: (v: number) => v }],
    ['g', { type: 'mass', toBase: (v: number) => v / 1000 }],
  ])
  
  static convert(value: number, fromUnit: string, toUnit: string): number {
    const from = this.units.get(fromUnit)
    const to = this.units.get(toUnit)
    
    if (!from || !to) {
      throw new Error('Unknown unit')
    }
    
    if (from.type !== to.type) {
      throw new Error('Cannot convert between different unit types')
    }
    
    const baseValue = from.toBase(value)
    return to.fromBase ? to.fromBase(baseValue) : baseValue / to.toBase(1)
  }
}
```

### 4단계: 리팩토링 (Refactor)
```typescript
// 더 확장 가능한 구조로 개선
export interface UnitDefinition {
  symbol: string
  name: string
  type: UnitType
  toBase: (value: number) => number
  fromBase: (value: number) => number
}

export class UnitConverter {
  private static units: Map<string, UnitDefinition> = new Map()
  
  static registerUnit(unit: UnitDefinition): void {
    this.units.set(unit.symbol, unit)
  }
  
  static convert(value: number, fromUnit: string, toUnit: string): number {
    // 개선된 구현
  }
}
```

## 모범 사례

### ✅ DO (해야 할 것)
- 코드 작성 전에 항상 테스트를 먼저 작성
- 각 테스트는 하나의 동작만 검증
- 테스트는 독립적이고 순서에 무관하게 실행 가능해야 함
- 의미 있는 테스트 이름 사용
- AAA 패턴 (Arrange-Act-Assert) 따르기
- 엣지 케이스와 에러 케이스 테스트
- 테스트 코드도 프로덕션 코드만큼 깔끔하게 유지

### ❌ DON'T (하지 말아야 할 것)
- 테스트 없이 코어 로직 구현
- 여러 동작을 하나의 테스트에서 검증
- 테스트 간 의존성 생성
- 구현 세부사항 테스트 (인터페이스만 테스트)
- 테스트를 나중으로 미루기
- 실패하는 테스트를 무시하거나 주석 처리

## 코드 리뷰 체크리스트

코어 로직 PR 시 다음을 확인:
- [ ] 모든 새로운 함수/클래스에 대한 테스트가 있는가?
- [ ] 테스트가 먼저 작성되었는가? (커밋 히스토리 확인)
- [ ] 모든 테스트가 통과하는가?
- [ ] 커버리지가 목표치를 충족하는가?
- [ ] 엣지 케이스가 테스트되었는가?
- [ ] 에러 처리가 테스트되었는가?
- [ ] 테스트 이름이 명확한가?

## 참고 자료

- [Vitest 문서](https://vitest.dev/)
- [Testing Library 모범 사례](https://testing-library.com/docs/queries/about)
- [TDD by Example - Kent Beck](https://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530)

---

**이 규칙을 따르지 않는 코어 로직 PR은 리뷰에서 반려됩니다.**
