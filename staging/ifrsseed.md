# IFRSseed — ESG 공시 플랫폼 회원가입/로그인 워크플로우와 DB 설계

ESG 공시 플랫폼(IFRSseed)의 인증 워크플로우와 데이터베이스 스키마를 정리했습니다. 핵심은 **HRIS(인사 시스템) 연동으로 역할을 자동 할당**하고, GHG 배출량 산정·승인·감사 이력을 체계적으로 추적하는 것입니다.

## 1. 회원가입 워크플로우 (HRIS 검증)

전체 흐름은 다음과 같습니다. 6단계의 HRIS 사용자 정보 자동 조회가 핵심입니다.

```
[1. 회사정보 입력]
    • 회사명 / 사업자등록번호 입력
    • "회사정보 자동 불러오기" 클릭
    ↓
[2. DART API 연동]
    • DART API 호출 → 회사 공시정보 자동 조회
    • 회사명, 대표자명, 본사 주소, 업종 자동 입력
    ↓
[3. 회사정보 확인]
    • 자동 입력 정보 확인(읽기 전용) → "확인 완료" 체크
    ↓
[4. 사용자 이메일 입력 및 인증]
    • 이메일 입력 → "이메일 인증" → 인증코드 입력 → "인증 완료"
    ↓
[5. 비밀번호 설정]
    • 특수문자·영문·숫자 포함, 8~14자 / 강도 표시
    ↓
[6. HRIS 사용자 정보 자동 조회]  ★ 핵심
    • 회사 ID와 이메일로 HRIS API 호출 → 회사 소속 여부 자동 검증
        GET /api/companies/{company_id}/users?email=user@company.com
        응답: { "exists": true, "name": "홍길동", "position": "부사장",
                "department": "ESG팀", "employee_id": "EMP001", ... }
    ↓
    ┌─ HRIS 조회 성공(회사 소속 확인) ─┐ ┌─ HRIS 조회 실패(소속 없음) ─┐
    [7a. 역할 자동 할당]                 [7b. 기본 역할 할당]
      • HRIS 정보 기반 자동 할당           • 일반 사용자 + 관리자 승인 필요
    [8a. 할당된 역할 확인]               [8b. 승인 대기 → 승인 후 역할 할당]
    ↓
[9. 사용자 계정 생성]
    • 이메일/비밀번호(암호화), 역할, 직급/부서(HRIS), HRIS 연동 정보 저장
    ↓
[10. 회원가입 완료] → [11. 자동 로그인 또는 로그인 페이지 이동]
```

## 2. 단계별 화면

### Step 1: 기본정보 입력
```
┌─────────────────────────────────────────┐
│  회원가입 - Step 1/6                      │
├─────────────────────────────────────────┤
│  회사 이메일: [user@company.com    ]      │
│  비밀번호:   [****************     ]      │
│   • 8자 이상 / 영문, 숫자, 특수문자 포함   │
│  비밀번호 확인: [****************  ]       │
│  [다음]                                   │
└─────────────────────────────────────────┘
```

### Step 2-4: 회사정보 입력 및 확인
```
┌─────────────────────────────────────────┐
│  회원가입 - Step 2-4/6                    │
├─────────────────────────────────────────┤
│  회사명: [회사명 입력          ]          │
│  사업자등록번호: [000-00-00000]           │
│  [회사정보 자동 불러오기]  ↓ (DART API)   │
│  • 회사명: (주)IFRSseed                   │
│  • 대표자명: 홍길동                       │
│  • 본사 주소: 서울시 강남구...            │
│  • 업종: 소프트웨어 개발                  │
│  [확인 완료]                              │
└─────────────────────────────────────────┘
```

### Step 5: 이메일 인증
```
┌─────────────────────────────────────────┐
│  회원가입 - Step 5/6                      │
├─────────────────────────────────────────┤
│  user@company.com로 인증 이메일 발송       │
│   ○ 인증 링크 클릭                        │
│   ○ 인증번호 입력: [123456     ]          │
│  [인증번호 재발송] (유효시간: 10분)        │
│  [인증 완료]                              │
└─────────────────────────────────────────┘
```

### Step 6: HRIS 사용자 정보 자동 조회
```
조회 성공                          조회 실패
┌──────────────────┐              ┌──────────────────┐
│ 사용자 정보 확인  │              │ HRIS 미등록 사용자│
│ 직급: 부사장      │              │ 기본 역할: 일반   │
│ 부서: ESG팀       │              │ 관리자 승인 필요  │
│ 할당 역할: ESG팀  │              │ [승인 요청]       │
│ 권한:             │              │                  │
│  • ESG 데이터 통합관리           │                  │
│  • 현업팀 데이터 검토·승인        │                  │
│ [확인]            │              │                  │
└──────────────────┘              └──────────────────┘
```

### Step 7: 회원가입 완료
```
┌─────────────────────────────────────────┐
│  회원가입 완료                            │
│  할당된 역할: ESG팀 / 직급: 부사장        │
│  부서: ESG팀                              │
│  다음 로그인부터 이메일+비밀번호로 로그인  │
│  [시작하기]                               │
└─────────────────────────────────────────┘
```

## 3. 로그인 워크플로우

```
[1-2. 로그인 정보 입력]
    회사 이메일 / 비밀번호 / [ ] 자동 로그인
    ↓
[3-4. 인증 및 정보 조회]
    • 이메일/비밀번호 검증 → 세션 생성 → 사용자 정보·역할 확인
    ↓
[HRIS 정보 동기화 (선택적)]
    • HRIS 연동 활성화 시 최신 직급/부서 동기화, 역할 변경 확인
    ↓
[로그인 완료]
```

로그인 완료 화면에서는 네비게이션 바에 사용자(홍길동), 회사((주)IFRSseed), 역할(ESG팀), 권한(ESG 데이터 통합 관리)이 표시되고, 접근 가능한 탭(회사정보·문단생성·도표 생성·최종보고서)이 노출됩니다.

## 4. 전체 Flow 통합

```
                    [시작]
        ┌─────────────┼─────────────┐
   [회원가입]      [로그인]     [비밀번호 찾기]
[1. 이메일 입력]  [1. 이메일]   [1. 이메일]
[2. 비밀번호]     [2. 비밀번호] [2. 재설정 링크]
[3. 회사정보]     [3. 인증]     [3. 새 비밀번호]
[4. DART API]     [4. 정보 조회][4. 완료]
[5. 이메일 인증]   [5. HRIS 동기화]
[6. HRIS 조회]    [6. 로그인 완료]
[7. 역할 할당]
[8. 계정 생성]
[9. 완료] → [자동 로그인] → [서비스 이용]
```

## 5. 핵심 포인트

**회원가입 시**
1. 이메일 + 비밀번호 입력(필수)
2. 회사정보 입력 및 DART API 연동
3. 이메일 인증
4. HRIS에서 사용자 정보 자동 조회(역할 할당용)
5. 역할 자동 할당(HRIS 정보 기반)
6. 사용자 계정 생성(비밀번호 포함)

**로그인 시**
1. 이메일 + 비밀번호 입력 → 2. 플랫폼 자체 인증 → 3. 사용자 정보·역할 조회 → 4. HRIS 동기화(선택) → 5. 로그인 완료

> **HRIS의 역할: 역할 할당 정보(직급/부서) 제공. 사용자 인증은 플랫폼에서 처리하고, 비밀번호도 플랫폼에 저장합니다.** 이 흐름으로 보안을 유지하면서 역할을 자동 할당할 수 있습니다.

## 6. DB 스키마 — GHG 배출량 관련 테이블

### 6.1. `ghg_emission_results` — 배출량 산정 결과
```sql
CREATE TABLE ghg_emission_results (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL,

  -- 기간 정보
  period_year INTEGER NOT NULL,
  period_month INTEGER,

  -- Scope별 배출량
  scope1_total_tco2e DECIMAL(18, 4),             -- Scope 1 총 배출량
  scope1_fixed_combustion_tco2e DECIMAL(18, 4),  -- 고정연소
  scope1_mobile_combustion_tco2e DECIMAL(18, 4), -- 이동연소
  scope1_fugitive_tco2e DECIMAL(18, 4),          -- 탈루 (냉매)
  scope1_incineration_tco2e DECIMAL(18, 4),      -- 소각

  scope2_location_tco2e DECIMAL(18, 4),          -- Scope 2 위치 기반
  scope2_market_tco2e DECIMAL(18, 4),            -- Scope 2 시장 기반
  scope2_renewable_tco2e DECIMAL(18, 4),         -- 재생에너지 반영

  scope3_total_tco2e DECIMAL(18, 4),             -- Scope 3 총 배출량
  scope3_category_1_tco2e DECIMAL(18, 4),        -- Cat.1: 구매 물품
  scope3_category_4_tco2e DECIMAL(18, 4),        -- Cat.4: 인바운드 물류
  scope3_category_6_tco2e DECIMAL(18, 4),        -- Cat.6: 출장
  scope3_category_7_tco2e DECIMAL(18, 4),        -- Cat.7: 통근
  scope3_category_9_tco2e DECIMAL(18, 4),        -- Cat.9: 아웃바운드 물류
  scope3_category_11_tco2e DECIMAL(18, 4),       -- Cat.11: 제품 사용
  scope3_category_12_tco2e DECIMAL(18, 4),       -- Cat.12: 제품 폐기

  total_tco2e DECIMAL(18, 4),                    -- 총 배출량

  -- 적용 프레임워크 및 버전
  applied_framework TEXT,    -- 'GHG_Protocol' | 'IFRS_S2' | 'K-ETS' | 'GRI' | 'ESRS'
  calculation_version TEXT,  -- 'v1' | 'v2' | 'latest'

  -- 데이터 신뢰도
  data_quality_score DECIMAL(5, 2),  -- 0~100
  data_quality_level TEXT,           -- 'M1' | 'M2' | 'E1' | 'E2'

  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  INDEX idx_ghg_results_company (company_id, period_year),
  INDEX idx_ghg_results_framework (company_id, applied_framework)
);
```

### 6.2. `ghg_emission_factors` — 배출계수
```sql
CREATE TABLE ghg_emission_factors (
  id UUID PRIMARY KEY,

  -- 배출계수 식별
  factor_code TEXT NOT NULL UNIQUE,  -- 'KR_2024_GRID_ELECTRICITY'
  factor_name_ko TEXT NOT NULL,
  factor_name_en TEXT,

  -- 배출계수 값
  emission_factor DECIMAL(18, 6),  -- tCO2e/단위
  unit TEXT NOT NULL,              -- 'kWh' | 'Nm³' | 'L' | 'kg' 등

  -- 적용 범위
  applicable_scope TEXT,     -- 'Scope1' | 'Scope2' | 'Scope3'
  applicable_category TEXT,  -- '고정연소' | '이동연소' | '전력' 등

  -- 기준 정보
  reference_year INTEGER,   -- 2024
  reference_source TEXT,    -- '환경부' | 'K-ETS' | 'IPCC' | 'IEA'
  reference_url TEXT,

  -- GWP 정보
  gwp_value DECIMAL(18, 4),  -- 지구온난화지수 (CO2=1 기준)

  -- 유효 기간
  effective_from DATE,
  effective_to DATE,

  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  INDEX idx_ghg_factors_code (factor_code),
  INDEX idx_ghg_factors_scope (applicable_scope, applicable_category)
);
```

### 6.3. `ghg_calculation_evidence` — 산정 근거
```sql
CREATE TABLE ghg_calculation_evidence (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL,

  -- 연결 정보
  activity_data_id UUID NOT NULL,  -- ghg_activity_data.id 참조
  tab_type TEXT NOT NULL,  -- 'power_heat_steam' | 'fuel_vehicle' | 'refrigerant' | 'waste' | 'logistics_travel' | 'raw_materials'

  -- 배출계수 정보 (산정 시점 스냅샷)
  applied_factor_id TEXT,                 -- 'EF-LNG-2023' | 'EF-전력-2024'
  applied_factor_value DECIMAL(18, 6),    -- 산정 시점 배출계수 값 (스냅샷)
  applied_factor_version TEXT,            -- '2023-환경부' | '2024-K-ETS'
  applied_gwp_basis TEXT,                 -- 'AR5' | 'AR6'

  -- 산정 방법론
  calculation_method TEXT,   -- '연료연소법' | 'spend-based' | 'distance-based' | 'activity-based'
  calculation_formula TEXT,  -- 산정 산식 예: '125000 × 0.0005 × 1.0'

  -- 산정 입력값 (활동자료 스냅샷)
  activity_amount DECIMAL(18, 4),  -- 예: 125000
  activity_unit TEXT,              -- 'kWh' | 'Nm³' | 'L'

  -- 산정 결과
  ghg_emission_tco2e DECIMAL(18, 4),  -- 산정된 배출량 (tCO₂e)

  -- 산정 메타데이터
  calculated_at TIMESTAMPTZ NOT NULL,  -- 산정 실행 일시
  calculated_by TEXT NOT NULL,         -- 산정 실행 사용자 ID
  calculation_version TEXT,            -- 'v1' | 'v2' | 'v3' (재산정 시 증가)

  -- 재산정 이력 추적
  is_latest BOOLEAN DEFAULT TRUE,  -- 최신 산정 여부
  previous_evidence_id UUID,       -- 이전 산정 근거 참조 (이력 체인)

  -- Scope 분류
  scope_type TEXT,      -- 'Scope1' | 'Scope2' | 'Scope3'
  scope_category TEXT,  -- '고정연소' | '이동연소' | '전력' | 'Cat.1' 등

  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  INDEX idx_evidence_activity (activity_data_id),
  INDEX idx_evidence_company (company_id, calculated_at),
  INDEX idx_evidence_latest (company_id, activity_data_id, is_latest),
  INDEX idx_evidence_factor (applied_factor_id),
  INDEX idx_evidence_scope (company_id, scope_type, scope_category)
);
```

### 6.4. `ghg_calculation_snapshots` — 산정 버전 스냅샷
```sql
CREATE TABLE ghg_calculation_snapshots (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL,

  -- 버전 정보
  snapshot_version TEXT NOT NULL,  -- 'v1' | 'v2' | 'v3'
  label TEXT,                      -- '2024년 1분기 최종' | '수원공장 수정 반영'

  -- 스냅샷 데이터
  payload JSONB NOT NULL,          -- 전체 데이터셋 (scope1, scope2, scope3, boundaryPolicy)
  period_locks_snapshot JSONB,     -- 당시 Lock 상태

  created_by TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  INDEX idx_ghg_snapshots_company (company_id, created_at)
);
```

### 6.5. `ghg_period_locks` — 기간별 데이터 잠금
```sql
CREATE TABLE ghg_period_locks (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL,

  -- 잠금 대상
  period_year INTEGER NOT NULL,
  period_month INTEGER,  -- NULL이면 연간 잠금
  scope_type TEXT,       -- 'scope1' | 'scope2' | 'scope3' | 'all'

  -- 잠금 상태
  status TEXT NOT NULL,  -- 'locked' | 'unlocked'
  locked_by TEXT NOT NULL,
  locked_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  lock_reason TEXT,

  -- 잠금 해제 정보
  unlocked_by TEXT,
  unlocked_at TIMESTAMPTZ,
  unlock_reason TEXT,

  INDEX idx_ghg_locks_company (company_id, period_year, period_month)
);
```

### 6.6. `ghg_audit_logs` — 변경 추적 로그

기본 형태와, FK·UUID를 보강한 개선 형태를 함께 정리합니다.

```sql
-- 개선 버전 (FK 제약조건 및 UUID 적용)
CREATE TABLE ghg_audit_logs (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL REFERENCES companies(id),

  -- 변경 대상 (Polymorphic Association)
  entity_type TEXT NOT NULL,  -- 'activity_data' | 'emission_results' | 'calculation_snapshots'
  entity_id UUID NOT NULL,    -- 해당 테이블의 PK (FK 제약은 걸 수 없음)

  -- 변경 정보
  action TEXT NOT NULL,  -- 'insert' | 'update' | 'delete'
  old_value JSONB,       -- 변경 전 값 (변경된 필드만)
  new_value JSONB,       -- 변경 후 값 (변경된 필드만)

  -- 변경자 정보 (users 테이블 FK)
  changed_by UUID NOT NULL REFERENCES users(id),
  changed_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  change_reason TEXT,  -- 'ERP 데이터 오류 정정' | '현장 확인 후 수정' | '마감 처리'

  -- 트리거 정보
  triggered_by TEXT,  -- 'api' | 'trigger' | 'manual'

  INDEX idx_ghg_audit_entity (entity_type, entity_id),
  INDEX idx_ghg_audit_company (company_id, changed_at),
  INDEX idx_ghg_audit_user (changed_by, changed_at)
);
```

### 6.7. `ghg_audit_comments` — 감사 코멘트
```sql
CREATE TABLE ghg_audit_comments (
  id UUID PRIMARY KEY,
  company_id UUID NOT NULL,

  -- 연결 정보
  related_entity_type TEXT NOT NULL,
  related_entity_id UUID NOT NULL,

  -- 코멘트 정보
  comment_text TEXT NOT NULL,
  comment_type TEXT,  -- 'question' | 'finding' | 'recommendation'

  -- 작성자 정보
  commented_by TEXT NOT NULL,
  commented_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

  -- 응답 정보
  response_text TEXT,
  responded_by TEXT,
  responded_at TIMESTAMPTZ,

  INDEX idx_ghg_comments_entity (related_entity_type, related_entity_id)
);
```

## 7. 승인 워크플로우 테이블

다단계 승인 워크플로우를 관리하는 두 테이블입니다. 요청부터 최종 승인까지 단계별로 추적·기록합니다.

### 7.1. `workflow_approvals` — 승인 워크플로우 메인

승인 요청의 전체 정보와 진행 상태를 관리합니다.

**다양한 승인 타입 지원**
```sql
workflow_type:
  - 'ghg_unlock'           -- GHG 잠금 해제 요청
  - 'ghg_data_submission'  -- GHG 데이터 제출 승인
  - 'ghg_final_approval'   -- GHG 최종 승인
  - 'data_approval'        -- 일반 데이터 승인
  - 'report_approval'      -- 보고서 승인
  - 'unlock_request'       -- 잠금 해제 요청
  - 'final_submission'     -- 최종 제출
  - 'custom'               -- 커스텀 승인
```

**다양한 엔티티에 대한 승인(Polymorphic Association)**
```sql
related_entity_type:
  - 'ghg_calculation_snapshots'
  - 'ghg_unlock_request'
  - 'ghg_emission_results'
  - 'environmental_data'
  - 'sr_report_content'
  - 'comments'
```

**다단계 승인 프로세스 관리**
```sql
current_step: 1, 2, 3...  -- 현재 진행 중인 단계
total_steps: 3            -- 총 단계 수
status: 'pending' | 'in_progress' | 'approved' | 'rejected' | 'cancelled'
```

**승인자 정보 관리(JSONB)**
```json
approvers: [
  { "user_id": "uuid-1", "step": 1, "role": "reviewer",       "status": "pending" },
  { "user_id": "uuid-2", "step": 2, "role": "approver",       "status": "pending" },
  { "user_id": "uuid-3", "step": 3, "role": "final_approver", "status": "pending" }
]
```

### 7.2. `workflow_approval_steps` — 승인 단계별 상세

각 단계별 승인자의 처리 내역(승인/반려, 코멘트, 전자서명, 알림 발송 여부)을 기록합니다.

```sql
-- 단계별 승인자 정보
step_order: 1, 2, 3...
approver_id: UUID
approver_role: 'reviewer' | 'approver' | 'final_approver'

-- 승인 처리 정보
action: 'approved' | 'rejected' | 'pending' | 'cancelled'
comment: TEXT
action_at: TIMESTAMPTZ
```

전자서명(e-Sign)과 알림 발송도 추적합니다.

```json
e_sign_data: {
  "signerId": "user-uuid",
  "timestamp": "2025-01-15T10:30:00Z",
  "hash": "sha256-hash-value",
  "certificate": "certificate-data"
}
```

```sql
notification_sent: BOOLEAN
notification_sent_at: TIMESTAMPTZ
```

## 8. 사용 시나리오

### 시나리오 1: GHG 잠금 해제 요청 승인
```
[1단계: 검토자] 사용자A가 잠금 해제 요청
  → workflow_approvals 생성 (type='ghg_unlock', status='pending', step 1/3)
  → workflow_approval_steps 생성 (step_order:1, action:'pending') → 검토자 알림
[2단계: 승인자] 검토자 승인
  → steps 업데이트 (step 1: 'approved', '검토 완료') → approvals (step 2, 'in_progress')
  → 승인자 알림
[3단계: 최종 승인자] 승인자 승인 → 최종 승인자가 전자서명 후 승인
  → approvals (status:'approved', completed_at, completed_by) → 잠금 해제 완료
```

### 시나리오 2: SR 보고서 승인
```
[현업팀] SR 보고서 작성 완료 → approvals 생성 (type='report_approval')
[ESG팀] 1단계 검토·승인 (reviewer)
[최종 승인권자] 2단계 최종 승인 (final_approver, 전자서명 필수)
[완료] approvals.status = 'approved' → 보고서 제출 가능
```

### 두 테이블의 관계
```
workflow_approvals (1) ──< (N) workflow_approval_steps
  [메인 정보]                  [단계별 상세]
  - 전체 승인 상태             - 각 단계별 승인자
  - 현재 단계                  - 승인/반려 처리
  - 승인자 목록 (JSONB)        - 코멘트 / 전자서명 / 알림 발송 여부
```

**주요 특징**: 범용성(Polymorphic Association으로 GHG·SR·일반 데이터에 적용), 유연한 다단계 지원(JSONB), 감사 추적(전자서명으로 법적 책임 명확화), 알림 연동.

## 9. 대시보드 및 시스템 관리 테이블 요약

### 대시보드 및 시스템 관리(7개)
- **진행률 집계**: `page_progress`(페이지별 진행 상태·완료율), `progress_snapshots`(진행률 이력)
- **알림 관리**: `notifications`(인앱 알림 메시지·읽음 상태)
- **할 일 리스트**: `todos`(우선순위·기한·상태)
- **팀원 현황**: `team_assignments`(페이지 담당·작업 할당), `team_activity_logs`(활동 이력)
- **리포트 출력**: `report_exports`(다운로드/출력 이력)
- **검토 요청**: `review_requests`(검토 요청·승인/반려)

### 온프레미스 로그인 및 사용자 관리(4개)
- `users`: 온프레미스 사용자 정보(초기 비밀번호, 최초 로그인 관리 포함)
- `companies`: 회사 기본 정보 및 마감일 관리
- `user_sessions`: 로그인 세션 및 JWT 토큰 관리
- `password_reset_tokens`: 비밀번호 재설정 토큰 관리

> **설계 원칙: FK 제약조건(참조 무결성), 인덱스(조회 성능), CHECK 제약조건(데이터 무결성), Polymorphic Association(유연한 엔티티 참조), JSONB(유연한 데이터 저장).**
