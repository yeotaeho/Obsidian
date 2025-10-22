JDK안에 클래스 
### 1. **입력 읽기 메소드 (next 계열)**

- `next()`
    
- `nextLine()`
    
- `nextInt()`
    
- `nextLong()`
    
- `nextDouble()`
    
- `nextFloat()`
    
- `nextByte()`
    
- `nextShort()`
    
- `nextBoolean()`
    
- `nextBigInteger()`
    
- `nextBigDecimal()`
    

👉 데이터 타입별로 하나씩 있어서 꽤 많아요.

---

### 2. **확인 (hasNext 계열)**

- `hasNext()`
    
- `hasNextLine()`
    
- `hasNextInt()`
    
- `hasNextLong()`
    
- `hasNextDouble()`
    
- `hasNextFloat()`
    
- `hasNextByte()`
    
- `hasNextShort()`
    
- `hasNextBoolean()`
    
- `hasNextBigInteger()`
    
- `hasNextBigDecimal()`
    

👉 위에 대응하는 타입별 `hasNextXXX()` 버전도 전부 있음.

---

### 3. **검색 / 패턴 관련**

- `findInLine(String pattern)`
    
- `findInLine(Pattern pattern)`
    
- `findWithinHorizon(String pattern, int horizon)`
    
- `findWithinHorizon(Pattern pattern, int horizon)`
    
- `skip(String pattern)`
    
- `skip(Pattern pattern)`
    
- `useDelimiter(String pattern)`
    
- `useDelimiter(Pattern pattern)`
    
- `delimiter()`
    

---

### 4. **설정 관련**

- `useRadix(int radix)` → 진법 변경 (예: 16진수)
    
- `radix()` → 현재 진법 반환
    
- `locale()`
    
- `useLocale(Locale locale)`
    
- `match()`
    

---

### 5. **기타**

- `close()` → 스캐너 닫기
    
- `ioException()` → I/O 예외 확인
    
- `remove()` → (Iterator 인터페이스 때문에 있지만 거의 안 씀)