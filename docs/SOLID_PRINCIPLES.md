# SOLID 원칙

이 프로젝트의 모든 코어 로직은 SOLID 원칙을 따라 구현해야 합니다.

## SOLID 원칙 개요

SOLID는 객체 지향 프로그래밍의 5가지 핵심 설계 원칙입니다:

1. **S**ingle Responsibility Principle (단일 책임 원칙)
2. **O**pen/Closed Principle (개방-폐쇄 원칙)
3. **L**iskov Substitution Principle (리스코프 치환 원칙)
4. **I**nterface Segregation Principle (인터페이스 분리 원칙)
5. **D**ependency Inversion Principle (의존성 역전 원칙)

---

## 1. Single Responsibility Principle (SRP)
### 단일 책임 원칙

> 클래스는 단 하나의 책임만 가져야 하며, 변경의 이유도 단 하나여야 한다.

### ❌ 나쁜 예
```typescript
// 여러 책임을 가진 클래스
class FormulaManager {
  // 책임 1: 공식 계산
  calculate(formula: Formula, inputs: Inputs): Result {
    // ...
  }
  
  // 책임 2: 데이터 저장
  saveToDatabase(result: Result): void {
    // ...
  }
  
  // 책임 3: UI 포맷팅
  formatForDisplay(result: Result): string {
    // ...
  }
  
  // 책임 4: 로깅
  logCalculation(formula: Formula, result: Result): void {
    // ...
  }
}
```

### ✅ 좋은 예
```typescript
// 각 클래스가 하나의 책임만 가짐
class CalculationEngine {
  calculate(formula: Formula, inputs: Inputs): Result {
    // 계산 로직만 담당
  }
}

class CalculationRepository {
  save(result: Result): void {
    // 데이터 저장만 담당
  }
}

class ResultFormatter {
  format(result: Result): string {
    // 포맷팅만 담당
  }
}

class CalculationLogger {
  log(formula: Formula, result: Result): void {
    // 로깅만 담당
  }
}
```

### 적용 가이드
- 클래스/함수가 하나의 명확한 목적만 가지도록 설계
- "이 클래스/함수는 무엇을 하는가?"에 대한 답이 "그리고"를 포함하면 분리 고려
- 변경 이유가 여러 개라면 책임이 여러 개인 것

---

## 2. Open/Closed Principle (OCP)
### 개방-폐쇄 원칙

> 소프트웨어 엔티티는 확장에는 열려 있어야 하고, 수정에는 닫혀 있어야 한다.

### ❌ 나쁜 예
```typescript
// 새로운 단위 타입 추가 시 기존 코드 수정 필요
class UnitConverter {
  convert(value: number, from: string, to: string): number {
    if (from === 'kg' && to === 'g') {
      return value * 1000
    } else if (from === 'g' && to === 'kg') {
      return value / 1000
    } else if (from === 'm' && to === 'cm') {
      return value * 100
    }
    // 새로운 단위 추가 시 여기에 계속 추가해야 함
    throw new Error('Unsupported conversion')
  }
}
```

### ✅ 좋은 예
```typescript
// 확장 가능한 구조
interface UnitDefinition {
  symbol: string
  type: UnitType
  toBase: (value: number) => number
  fromBase: (value: number) => number
}

class UnitConverter {
  private units = new Map<string, UnitDefinition>()
  
  // 새로운 단위를 등록만 하면 됨 (기존 코드 수정 불필요)
  registerUnit(unit: UnitDefinition): void {
    this.units.set(unit.symbol, unit)
  }
  
  convert(value: number, from: string, to: string): number {
    const fromUnit = this.units.get(from)
    const toUnit = this.units.get(to)
    
    if (!fromUnit || !toUnit) {
      throw new Error('Unknown unit')
    }
    
    if (fromUnit.type !== toUnit.type) {
      throw new Error('Incompatible unit types')
    }
    
    const baseValue = fromUnit.toBase(value)
    return toUnit.fromBase(baseValue)
  }
}

// 사용: 새로운 단위 추가 (기존 코드 수정 없음)
converter.registerUnit({
  symbol: 'lb',
  type: 'mass',
  toBase: (v) => v * 0.453592,
  fromBase: (v) => v / 0.453592
})
```

### 적용 가이드
- 추상화와 인터페이스를 활용
- 전략 패턴, 팩토리 패턴 등 디자인 패턴 활용
- 새로운 기능 추가 시 기존 코드를 수정하지 않도록 설계

---

## 3. Liskov Substitution Principle (LSP)
### 리스코프 치환 원칙

> 서브타입은 언제나 기반 타입으로 교체할 수 있어야 한다.

### ❌ 나쁜 예
```typescript
interface Calculator {
  calculate(inputs: Inputs): Result
  validate(inputs: Inputs): boolean
}

class BasicCalculator implements Calculator {
  calculate(inputs: Inputs): Result {
    if (!this.validate(inputs)) {
      throw new Error('Invalid inputs')
    }
    return this.performCalculation(inputs)
  }
  
  validate(inputs: Inputs): boolean {
    return inputs.value > 0
  }
}

// LSP 위반: 부모 클래스와 다른 동작
class AdvancedCalculator extends BasicCalculator {
  validate(inputs: Inputs): boolean {
    // 부모와 다른 검증 로직 (음수 허용)
    return true  // 항상 true 반환
  }
}
```

### ✅ 좋은 예
```typescript
interface Calculator {
  calculate(inputs: Inputs): Result
}

interface InputValidator {
  validate(inputs: Inputs): boolean
}

class PositiveValueValidator implements InputValidator {
  validate(inputs: Inputs): boolean {
    return inputs.value > 0
  }
}

class AllValueValidator implements InputValidator {
  validate(inputs: Inputs): boolean {
    return true
  }
}

class CalculationEngine implements Calculator {
  constructor(private validator: InputValidator) {}
  
  calculate(inputs: Inputs): Result {
    if (!this.validator.validate(inputs)) {
      throw new Error('Invalid inputs')
    }
    return this.performCalculation(inputs)
  }
}

// 사용
const basicCalc = new CalculationEngine(new PositiveValueValidator())
const advancedCalc = new CalculationEngine(new AllValueValidator())
```

### 적용 가이드
- 서브클래스는 부모 클래스의 계약을 준수해야 함
- 부모 클래스의 메서드를 오버라이드할 때 동작을 완전히 바꾸지 말 것
- 상속보다는 컴포지션을 우선 고려

---

## 4. Interface Segregation Principle (ISP)
### 인터페이스 분리 원칙

> 클라이언트는 자신이 사용하지 않는 메서드에 의존하지 않아야 한다.

### ❌ 나쁜 예
```typescript
// 너무 많은 책임을 가진 인터페이스
interface FormulaService {
  calculate(formula: Formula, inputs: Inputs): Result
  save(result: Result): void
  export(result: Result): string
  share(result: Result): void
  plot(result: Result): Graph
  analyze(result: Result): Analysis
}

// 계산만 필요한 클래스도 모든 메서드를 구현해야 함
class SimpleCalculator implements FormulaService {
  calculate(formula: Formula, inputs: Inputs): Result {
    // 구현
  }
  
  // 사용하지 않는 메서드들도 구현해야 함
  save(result: Result): void {
    throw new Error('Not supported')
  }
  
  export(result: Result): string {
    throw new Error('Not supported')
  }
  
  // ...
}
```

### ✅ 좋은 예
```typescript
// 작고 집중된 인터페이스들
interface Calculator {
  calculate(formula: Formula, inputs: Inputs): Result
}

interface ResultStorage {
  save(result: Result): void
}

interface ResultExporter {
  export(result: Result): string
}

interface ResultSharer {
  share(result: Result): void
}

interface ResultVisualizer {
  plot(result: Result): Graph
}

interface ResultAnalyzer {
  analyze(result: Result): Analysis
}

// 필요한 인터페이스만 구현
class BasicCalculator implements Calculator {
  calculate(formula: Formula, inputs: Inputs): Result {
    // 구현
  }
}

class AdvancedCalculator implements Calculator, ResultStorage, ResultExporter {
  calculate(formula: Formula, inputs: Inputs): Result {
    // 구현
  }
  
  save(result: Result): void {
    // 구현
  }
  
  export(result: Result): string {
    // 구현
  }
}
```

### 적용 가이드
- 인터페이스는 작고 집중되어야 함
- 클라이언트가 필요한 메서드만 포함
- 큰 인터페이스는 여러 작은 인터페이스로 분리

---

## 5. Dependency Inversion Principle (DIP)
### 의존성 역전 원칙

> 고수준 모듈은 저수준 모듈에 의존해서는 안 되며, 둘 다 추상화에 의존해야 한다.

### ❌ 나쁜 예
```typescript
// 고수준 모듈이 저수준 모듈에 직접 의존
class LocalStorageRepository {
  save(data: any): void {
    localStorage.setItem('data', JSON.stringify(data))
  }
  
  load(): any {
    return JSON.parse(localStorage.getItem('data') || '{}')
  }
}

class CalculationService {
  private storage = new LocalStorageRepository()  // 구체적인 구현에 의존
  
  saveResult(result: Result): void {
    this.storage.save(result)
  }
}
```

### ✅ 좋은 예
```typescript
// 추상화에 의존
interface StorageRepository {
  save(data: any): void
  load(): any
}

class LocalStorageRepository implements StorageRepository {
  save(data: any): void {
    localStorage.setItem('data', JSON.stringify(data))
  }
  
  load(): any {
    return JSON.parse(localStorage.getItem('data') || '{}')
  }
}

class IndexedDBRepository implements StorageRepository {
  save(data: any): void {
    // IndexedDB 구현
  }
  
  load(): any {
    // IndexedDB 구현
  }
}

class CalculationService {
  constructor(private storage: StorageRepository) {}  // 추상화에 의존
  
  saveResult(result: Result): void {
    this.storage.save(result)
  }
}

// 사용: 의존성 주입
const service1 = new CalculationService(new LocalStorageRepository())
const service2 = new CalculationService(new IndexedDBRepository())
```

### 적용 가이드
- 구체적인 구현이 아닌 인터페이스에 의존
- 의존성 주입(Dependency Injection) 활용
- 생성자를 통해 의존성 전달

---

## 프로젝트 적용 예시

### 계산 엔진 설계

```typescript
// 1. SRP: 각 클래스가 하나의 책임만 가짐
interface Calculator {
  calculate(formula: Formula, inputs: Inputs): Result
}

interface UnitNormalizer {
  normalize(inputs: Inputs, formula: Formula): NormalizedInputs
}

interface ResultFormatter {
  format(result: number, formula: Formula): Result
}

// 2. OCP: 새로운 공식 추가 시 기존 코드 수정 불필요
interface FormulaEvaluator {
  evaluate(equation: string, scope: Scope): number
}

class MathJsEvaluator implements FormulaEvaluator {
  evaluate(equation: string, scope: Scope): number {
    return math.evaluate(equation, scope)
  }
}

// 3. LSP: 모든 Calculator 구현체는 동일하게 동작
class BasicCalculator implements Calculator {
  constructor(
    private normalizer: UnitNormalizer,
    private evaluator: FormulaEvaluator,
    private formatter: ResultFormatter
  ) {}
  
  calculate(formula: Formula, inputs: Inputs): Result {
    const normalized = this.normalizer.normalize(inputs, formula)
    const value = this.evaluator.evaluate(formula.equation, normalized)
    return this.formatter.format(value, formula)
  }
}

// 4. ISP: 작고 집중된 인터페이스
interface InputValidator {
  validate(inputs: Inputs): ValidationResult
}

interface ConstraintChecker {
  check(value: number, constraints: Constraints): boolean
}

// 5. DIP: 추상화에 의존
class CalculationService {
  constructor(
    private calculator: Calculator,
    private validator: InputValidator,
    private storage: StorageRepository
  ) {}
  
  performCalculation(formula: Formula, inputs: Inputs): Result {
    const validation = this.validator.validate(inputs)
    if (!validation.valid) {
      throw new Error(validation.error)
    }
    
    const result = this.calculator.calculate(formula, inputs)
    this.storage.save(result)
    return result
  }
}
```

## 코드 리뷰 체크리스트

SOLID 원칙 준수 확인:
- [ ] **SRP**: 각 클래스/함수가 하나의 책임만 가지는가?
- [ ] **OCP**: 새로운 기능 추가 시 기존 코드 수정이 필요한가?
- [ ] **LSP**: 서브타입이 부모 타입을 완전히 대체할 수 있는가?
- [ ] **ISP**: 인터페이스가 너무 크지 않은가? 사용하지 않는 메서드가 있는가?
- [ ] **DIP**: 구체적인 구현이 아닌 추상화에 의존하는가?

## 리팩토링 신호

다음 상황에서 SOLID 원칙 적용을 고려:
- 클래스가 100줄 이상
- 메서드가 20줄 이상
- 클래스에 5개 이상의 public 메서드
- 하나의 클래스를 수정할 때 여러 테스트가 깨짐
- 새로운 기능 추가 시 여러 곳을 수정해야 함
- 코드 재사용이 어려움

## 참고 자료

- [Clean Code - Robert C. Martin](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [SOLID Principles of Object-Oriented Design](https://en.wikipedia.org/wiki/SOLID)
- [Refactoring - Martin Fowler](https://refactoring.com/)

---

**SOLID 원칙을 따르지 않는 코어 로직 PR은 리뷰에서 반려됩니다.**
