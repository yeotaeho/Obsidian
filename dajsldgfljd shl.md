


벡터 DB 저장과 DART API 통합을 확인 중입니다.


[4 tools called]


두 가지 모두 구현 가능합니다. 현재 상태와 구현 방법을 정리합니다.

## 1. 벡터 DB에 기준서 PDF 저장

네, 맞습니다. LlamaParse/Unstructured로 파싱 후 저장하면 됩니다.

### 현재 상태
- `generate_embeddings.py`: DB 테이블(DataPoint 등)의 임베딩 생성
- PDF → 벡터 DB 저장 전용 스크립트는 없음

### 필요한 스크립트 구조

```python
# ai/ifrs_agent/scripts/store_pdf_to_vector_db.py

"""
PDF를 파싱하여 벡터 DB에 저장하는 스크립트
"""
import os
from pathlib import Path
from typing import List, Dict
from loguru import logger

from llama_parse import LlamaParse  # 또는 unstructured
from FlagEmbedding import FlagModel
from ifrs_agent.database.base import get_session
from ifrs_agent.model.models import DocumentChunk  # 벡터 저장용 테이블 필요

def store_pdf_to_vector_db(
    pdf_path: str,
    document_type: str = "standard",  # "standard", "report", "guidance"
    standard: str = None,  # "IFRS_S2"
    chunk_size: int = 1000,
    chunk_overlap: int = 200
):
    """
    1. PDF 파싱 (LlamaParse/Unstructured)
    2. 텍스트 청크 분할
    3. 임베딩 생성 (BGE-M3)
    4. 벡터 DB 저장 (PostgreSQL + pgvector)
    """
    # 1. PDF 파싱
    logger.info(f"PDF 파싱 시작: {pdf_path}")
    parser = LlamaParse(api_key=os.getenv("LLAMA_CLOUD_API_KEY"))
    parsed_doc = parser.load_data(pdf_path)
    
    # 2. 텍스트 추출 및 청크 분할
    text = "\n\n".join([page.get_text() for page in parsed_doc])
    chunks = split_text_into_chunks(text, chunk_size, chunk_overlap)
    
    # 3. 임베딩 생성
    embedder = FlagModel('BAAI/bge-m3', use_fp16=True)
    embeddings = embedder.encode(chunks, normalize_embeddings=True)
    
    # 4. 벡터 DB 저장
    db = get_session()
    try:
        for i, (chunk, embedding) in enumerate(zip(chunks, embeddings)):
            chunk_record = DocumentChunk(
                document_path=pdf_path,
                document_type=document_type,
                standard=standard,
                chunk_index=i,
                chunk_text=chunk,
                embedding=embedding.tolist(),
                metadata={
                    "page": i // 10,  # 대략적인 페이지 번호
                    "chunk_size": len(chunk)
                }
            )
            db.add(chunk_record)
        db.commit()
        logger.info(f"✅ {len(chunks)}개 청크 저장 완료")
    finally:
        db.close()
```

### 실행 방법

```bash
# IFRS S2 기준서를 벡터 DB에 저장
python -m ifrs_agent.scripts.store_pdf_to_vector_db \
    --pdf-path "ai/ifrs_agent/data/issb-2023-a-ifrs-s2-climate-related-disclosures.pdf" \
    --document-type "standard" \
    --standard "IFRS_S2" \
    --chunk-size 1000 \
    --chunk-overlap 200
```
## 2. DART API 크롤링 구현

DART API 키가 있으면 바로 구현 가능합니다.

### 필요한 DART 클라이언트 구조

```python
# ai/ifrs_agent/utils/dart_client.py

"""
DART 전자공시 API 클라이언트
"""
import os
import requests
from typing import List, Dict, Optional
from loguru import logger

class DARTClient:
    """DART API 클라이언트"""
    
    BASE_URL = "https://opendart.fss.or.kr/api"
    
    def __init__(self, api_key: str = None):
        self.api_key = api_key or os.getenv("DART_API_KEY")
        if not self.api_key:
            raise ValueError("DART_API_KEY가 설정되지 않았습니다.")
    
    def get_company_code(self, company_name: str) -> Optional[str]:
        """회사명으로 기업 코드 조회"""
        url = f"{self.BASE_URL}/company.json"
        params = {
            "crtfc_key": self.api_key,
            "corp_name": company_name
        }
        response = requests.get(url, params=params)
        data = response.json()
        
        if data.get("status") == "000":
            return data.get("list", [{}])[0].get("corp_code")
        return None
    
    def get_report_list(
        self,
        corp_code: str,
        bgn_de: str,  # "20240101"
        end_de: str,  # "20241231"
        pblntf_ty: str = "A"  # A: 정기공시, B: 주요사항보고
    ) -> List[Dict]:
        """보고서 목록 조회"""
        url = f"{self.BASE_URL}/list.json"
        params = {
            "crtfc_key": self.api_key,
            "corp_code": corp_code,
            "bgn_de": bgn_de,
            "end_de": end_de,
            "pblntf_ty": pblntf_ty
        }
        response = requests.get(url, params=params)
        data = response.json()
        
        if data.get("status") == "000":
            return data.get("list", [])
        return []
    
    def download_report(self, rcept_no: str) -> Optional[bytes]:
        """보고서 원문 다운로드 (PDF)"""
        url = f"https://dart.fss.or.kr/dsaf001/main.do?rcpNo={rcept_no}"
        # 실제로는 보고서 다운로드 API 사용
        # 또는 웹 크롤링으로 PDF 다운로드
        return None
    
    def get_sustainability_reports(
        self,
        corp_code: str,
        year: int
    ) -> List[Dict]:
        """지속가능경영보고서 목록 조회"""
        bgn_de = f"{year}0101"
        end_de = f"{year}1231"
        
        reports = self.get_report_list(corp_code, bgn_de, end_de)
        
        # 지속가능경영보고서 필터링
        sustainability_reports = [
            r for r in reports
            if "지속가능" in r.get("report_nm", "") or
               "ESG" in r.get("report_nm", "") or
               "sustainability" in r.get("report_nm", "").lower()
        ]
        
        return sustainability_reports
```

### RAG Node에 통합

```python
# ai/ifrs_agent/agent/rag_node.py 수정

async def _crawl_external_sources(
    self,
    company_id: str,
    fiscal_year: int
) -> List[Dict[str, Any]]:
    """외부 소스 크롤링 (실제 구현)"""
    from ifrs_agent.utils.dart_client import DARTClient
    
    logger.info(f"DART API 크롤링: {company_id}, {fiscal_year}")
    
    dart_client = DARTClient()
    
    # 1. 기업 코드 조회
    corp_code = dart_client.get_company_code(company_id)
    if not corp_code:
        logger.warning(f"기업 코드를 찾을 수 없습니다: {company_id}")
        return []
    
    # 2. 지속가능경영보고서 목록 조회
    reports = dart_client.get_sustainability_reports(corp_code, fiscal_year)
    
    # 3. 보고서 다운로드 및 파싱
    documents = []
    for report in reports:
        pdf_content = dart_client.download_report(report["rcept_no"])
        if pdf_content:
            # PDF 파싱 (LlamaParse)
            parsed = await self._parse_pdf(pdf_content)
            documents.append({
                "content": parsed,
                "source": f"DART-{report['rcept_no']}",
                "metadata": report
            })
    
    return documents
```

## 구현 순서

### 1단계: 벡터 DB 저장 스크립트 작성
- PDF 파싱 (LlamaParse/Unstructured)
- 청크 분할
- 임베딩 생성
- PostgreSQL + pgvector 저장

### 2단계: DART API 클라이언트 구현
- 기업 코드 조회
- 보고서 목록 조회
- 보고서 다운로드
- RAG Node 통합

### 3단계: 통합 테스트
- IFRS S2 기준서 → 벡터 DB 저장
- DART API → 삼성SDS 보고서 크롤링
- RAG Node에서 검색 테스트

두 스크립트를 작성해드릴까요?



python -m ifrs_agent.main --generate "IFRS S2 기후 관련 배출량 데이터 추출" --standards IFRS_S2 --fiscal-year 2024 --company-id samsung-sds



python -m ifrs_agent.main --test-rag --generate "IFRS S2 기후 관련 배출량 데이터 추출" --standards IFRS_S2 --fiscal-year 2024 --company-id samsung-sds --target-dps S2-15-a S2-16-a




## 데이터베이스 테이블 컬럼 상세 설명  
  
### 1. `data_points` 테이블 (Data Point)  
  
ESG 지표의 최소 공시 단위를 저장하는 핵심 테이블입니다.  
  dp_id,dp_code,name_ko,name_en,description,standard,category,topic,subtopic,dp_type,unit,validation_rules,value_range,equivalent_dps,parent_indicator,child_dps,financial_linkages,financial_impact_type,disclosure_requirement,reporting_frequency,is_active,deleted_at,deleted_by,created_at,updated_at

| 컬럼명                        | 타입            | 설명                              | 예시                                                           |     |
| -------------------------- | ------------- | ------------------------------- | ------------------------------------------------------------ | --- |
| **dp_id**                  | String(50)    | Data Point 고유 식별자 (Primary Key) | `"S2-29-a"`, `"GRI-305-1"`                                   |     |
| **dp_code**                | String(100)   | 표준화된 DP 코드 (Unique)             | `"IFRS_S2_SCOPE1_EMISSIONS"`                                 |     |
| **name_ko**                | String(200)   | 한국어 명칭                          | `"Scope 1 온실가스 배출량"`                                         |     |
| **name_en**                | String(200)   | 영어 명칭                           | `"Scope 1 GHG emissions"`                                    |     |
| **description**            | Text          | 상세 설명                           | `"보고 기간 중 직접 배출된 온실가스..."`                                   |     |
| **standard**               | String(50)    | 기준서 코드                          | `"IFRS_S2"`, `"GRI"`, `"TCFD"`                               |     |
| **category**               | String(1)     | ESG 카테고리 (E/S/G)                | `"E"` (Environment)                                          |     |
| **topic**                  | String(100)   | 주제                              | `"기후"`, `"온실가스 배출"`                                          |     |
| **subtopic**               | String(100)   | 세부 주제                           | `"Scope 1 배출량"`                                              |     |
| **dp_type**                | ENUM          | 데이터 타입                          | `"quantitative"`, `"qualitative"`, `"narrative"`, `"binary"` |     |
| **unit**                   | ENUM          | 단위                              | `"tco2e"`, `"currency_krw"`, `"count"`, `"percentage"`       |     |
| **validation_rules**       | JSONB         | 검증 규칙 (JSON)                    | `{"min": 0, "max": 1000000, "required": true}`               |     |
| **value_range**            | JSONB         | 값 범위 (JSON)                     | `{"min": 0, "max": 1000000}`                                 |     |
| **equivalent_dps**         | ARRAY(String) | 동일 의미 DP ID 목록                  | `["GRI-305-1", "SASB-EM-110a.1"]`                            |     |
| **parent_indicator**       | String(50)    | 상위 지표 DP ID (FK)                | `"S2-29"`                                                    |     |
| **child_dps**              | ARRAY(String) | 하위 DP ID 목록                     | `["S2-29-a-1", "S2-29-a-2"]`                                 |     |
| **financial_linkages**     | ARRAY(String) | 연결된 재무 계정 코드 목록                 | `["탄소배출권", "환경부채"]`                                          |     |
| **financial_impact_type**  | String(50)    | 재무 영향 유형                        | `"비용"`, `"수익"`, `"자산"`, `"부채"`                               |     |
| **disclosure_requirement** | ENUM          | 공시 요구사항                         | `"필수"`, `"권장"`, `"선택"`                                       |     |
| **reporting_frequency**    | String(20)    | 보고 주기                           | `"연간"`, `"반기"`, `"분기"`                                       |     |
| **is_active**              | Boolean       | 활성 여부 (Soft Delete)             | `true` / `false`                                             |     |
| **deleted_at**             | TIMESTAMP     | 삭제 시각                           | `2024-01-01 12:00:00`                                        |     |
| **deleted_by**             | String(100)   | 삭제자                             | `"admin"`                                                    |     |
| **created_at**             | TIMESTAMP     | 생성 시각                           | `2024-01-01 12:00:00`                                        |     |
| **updated_at**             | TIMESTAMP     | 수정 시각                           | `2024-01-01 12:00:00`                                        |     |
| **embedding**              | Vector(1024)  | 벡터 임베딩 (BGE-M3)                 | `[0.123, -0.456, ...]` (1024차원)                              |     |
| **embedding_text**         | Text          | 임베딩 생성에 사용된 텍스트                 | `"Scope 1 온실가스 배출량 Scope 1 GHG emissions..."`                |     |
| **embedding_updated_at**   | TIMESTAMP     | 임베딩 업데이트 시각                     | `2024-01-01 12:00:00`                                        |     |
  
---

# 1단계: 쿼리 최적화
optimized_queries = _optimize_query(query, target_dps)
  └─> 기본 쿼리 + DP별 쿼리 생성 (최대 3개)

# 2단계: 하이브리드 검색
├─ 2-1. 회사별 데이터 검색 (company_id, fiscal_year가 있는 경우)
│   └─> _hybrid_search(queries, filters={company_id, fiscal_year}, top_k=5)
│       ├─> 쿼리 임베딩 생성 (BGE-M3)
│       ├─> 벡터 DB 검색 (VectorStoreRepository)
│       └─> 결과 변환 및 중복 제거
│
└─ 2-2. 기준서 문서 검색 (항상 수행)
    └─> _hybrid_search(queries, filters={standard, document_type="standard"}, top_k=5)

# 3단계: 외부 크롤링 (DART API)
if company_id & fiscal_year 존재 AND (회사 데이터 부족 OR 외부 데이터 필요):
    └─> _crawl_external_sources(company_id, fiscal_year)
        ├─> _get_company_code(company_id)  # 회사명 → DART 기업코드
        ├─> _get_sustainability_reports(corp_code, fiscal_year)  # 지속가능보고서 목록
        └─> 각 보고서 처리:
            ├─> _download_report(rcept_no)  # PDF 다운로드
            ├─> DocumentService.store_pdf_to_vector_db()  # 벡터 DB 저장
            └─> 텍스트 추출 (PyMuPDF)

# 4단계: 멀티모달 콘텐츠 추출 (표·이미지)
if documents 존재:
    └─> _extract_multimodal_content(documents, target_dps)
        └─> TODO: LlamaParse 표 추출, 이미지 캡셔닝

# 5단계: DP 추출 및 팩트 시트 생성
fact_sheets = _extract_dps(search_results, target_dps)
  └─> 각 target_dp에 대해:
      ├─> DB에서 DataPoint 메타데이터 조회
      │   ├─> 정확히 일치하는 DP 검색
      │   └─> 없으면 _find_similar_dp() 실행
      │       ├─> Parent-Child 관계 검색
      │       ├─> 키워드 기반 검색
      │       └─> 숫자 기반 유사 검색
      │
      ├─> 관련 검색 결과 필터링 (DP 이름, ID 포함)
      │
      └─> LLM으로 값 추출
          └─> _extract_with_llm(relevant_results, dp_id, dp_metadata)
              ├─> 검색 결과 요약
              ├─> DP 메타데이터 정보 구성
              ├─> LLM 프롬프트 생성
              ├─> LLM 호출 (ChatGroq)
              └─> _parse_llm_response() - JSON 파싱

# 6단계: 상태 업데이트
state["fact_sheets"] = fact_sheets
state["yearly_data"] = _organize_by_year(fact_sheets)  # 연도별 정리
state["current_node"] = "retrieving"
state["status"] = "retrieving"