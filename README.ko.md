# Azure Entra ID 실무 가이드

[English](README.md) | [한국어](README.ko.md)

Microsoft Entra ID 기초부터 조건부 액세스, 거버넌스, 하이브리드 ID를 활용한 운영 환경까지 포괄적인 가이드입니다.

## 주요 내용

| 섹션 | 설명 |
|---------|-------------|
| [시작하기 (Start Here)](https://yeongseon.github.io/azure-entra-id-practical-guide/) | 개요, 학습 경로 및 저장소 맵 |
| [플랫폼 (Platform)](https://yeongseon.github.io/azure-entra-id-practical-guide/platform/) | Entra ID 아키텍처, 테넌트, 사용자, 앱, 인증, 토큰 |
| [베스트 프랙티스 (Best Practices)](https://yeongseon.github.io/azure-entra-id-practical-guide/best-practices/) | 테넌트 설계, MFA, 조건부 액세스, RBAC, ID 보호 |
| [시나리오 (Scenarios)](https://yeongseon.github.io/azure-entra-id-practical-guide/scenarios/) | 앱 등록, 조건부 액세스, 하이브리드 ID, B2B, 거버넌스 |
| [운영 (Operations)](https://yeongseon.github.io/azure-entra-id-practical-guide/operations/) | 사용자 수명 주기, 그룹 관리, 동의, 로그인/감사 로그 |
| [트러블슈팅 (Troubleshooting)](https://yeongseon.github.io/azure-entra-id-practical-guide/troubleshooting/) | 의사 결정 트리, 첫 10분 카드, 플레이북 |
| [참조 (Reference)](https://yeongseon.github.io/azure-entra-id-practical-guide/reference/) | CLI 치트시트, Graph API 참조, 플랫폼 제한, 오류 코드 |

## 시나리오 가이드

일반적인 ID 시나리오를 위한 실용적 구성:
- **앱 등록** — 웹 앱 인증 및 API 권한
- **조건부 액세스** — MFA 적용 및 디바이스 규정 준수
- **하이브리드 ID** — Entra Connect 동기화 및 클라우드 동기화
- **B2B 협업** — 게스트 사용자 관리 및 교차 테넌트 액세스
- **거버넌스** — 액세스 검토 및 자격 관리

## 빠른 시작

```bash
# 저장소 복제
git clone https://github.com/yeongseon/azure-entra-id-practical-guide.git

# MkDocs 의존성 설치
pip install mkdocs-material mkdocs-minify-plugin

# 로컬 문서 서버 시작
mkdocs serve
```

로컬에서 `http://127.0.0.1:8000`에 접속하여 문서를 확인하세요.

## 기여하기

기여는 언제나 환영합니다. 다음 사항을 준수해 주세요:
- 모든 CLI 예제에는 긴 플래그를 사용하세요 (`-g` 대신 `--resource-group`)
- 모든 문서에는 Mermaid 다이어그램을 포함하세요
- 모든 콘텐츠는 출처 URL과 함께 Microsoft Learn을 참조해야 합니다
- CLI 출력 예제에 개인 식별 정보(PII)를 포함하지 마세요

## 관련 프로젝트

| 저장소 | 설명 |
|---|---|
| [azure-virtual-machine-practical-guide](https://github.com/yeongseon/azure-virtual-machine-practical-guide) | Azure Virtual Machines 실무 가이드 |
| [azure-networking-practical-guide](https://github.com/yeongseon/azure-networking-practical-guide) | Azure Networking 실무 가이드 |
| [azure-storage-practical-guide](https://github.com/yeongseon/azure-storage-practical-guide) | Azure Storage 실무 가이드 |
| [azure-app-service-practical-guide](https://github.com/yeongseon/azure-app-service-practical-guide) | Azure App Service 실무 가이드 |
| [azure-functions-practical-guide](https://github.com/yeongseon/azure-functions-practical-guide) | Azure Functions 실무 가이드 |
| [azure-container-apps-practical-guide](https://github.com/yeongseon/azure-container-apps-practical-guide) | Azure Container Apps 실무 가이드 |
| [azure-kubernetes-service-practical-guide](https://github.com/yeongseon/azure-kubernetes-service-practical-guide) | Azure Kubernetes Service 실무 가이드 |
| [azure-monitoring-practical-guide](https://github.com/yeongseon/azure-monitoring-practical-guide) | Azure Monitoring 실무 가이드 |

## 면책 조항

이 프로젝트는 독립적인 커뮤니티 프로젝트입니다. Microsoft와 제휴하거나 보증을 받지 않았습니다. Azure 및 Microsoft Entra ID는 Microsoft Corporation의 상표입니다.

## 라이선스

[MIT](LICENSE)
