# BigData Analysis Team 2

"빅데이터 분석" 팀 프로젝트를 위한 공동 Repository입니다.

> **처음 참여하는 팀원은 `1. Quick Start`부터 진행하세요.**
>
> 기본 작업 흐름은 다음과 같습니다.
>
> `develop 최신화 → feature branch 생성 → 작업 → Ruff/pytest 확인 → Push → PR → CI 통과 → develop Merge`

---

# 1. Quick Start

## 1.1 개인 PC에서 Repository Clone

```bash
git clone https://github.com/romain1121/BigData_Analysis_Team-2.git
cd BigData_Analysis_Team-2
```

Python 3.11 환경을 사용합니다.

개발에 필요한 Dependency를 설치합니다.

```bash
pip install -r requirements-dev.txt
```

`requirements-dev.txt`에는 프로젝트 Dependency와 함께 다음 개발 도구가 포함되어 있습니다.

- `ruff`
- `pytest`
- `nbstripout`

---

## 1.2 nbstripout 설정

개인 Jupyter Notebook의 실행 Output과 실행 번호가 불필요한 Git Diff를 만들지 않도록 `nbstripout`을 사용합니다.

Repository를 Clone한 **각 환경에서 최초 1회** 실행합니다.

```bash
nbstripout --install --attributes .gitattributes
```

설정 확인:

```bash
nbstripout --status
```

적용 정책은 다음과 같습니다.

| 경로 | Notebook Output |
| --- | --- |
| `notebooks/GH/` | Commit 시 제거 |
| `notebooks/JW/` | Commit 시 제거 |
| `notebooks/JY/` | Commit 시 제거 |
| `notebooks/final/` | 유지 |

`notebooks/final/`은 최종 결과의 표·그래프 등을 보존할 수 있도록 `nbstripout` 적용 대상에서 제외합니다.

> Windows에서 `nbstripout --install` 실행 시 `.gitattributes`의 인코딩 문제를 피하기 위해 `.gitattributes`는 UTF-8로 유지하고 주석은 영문으로 작성합니다.

---

## 1.3 작업 Branch 생성

`main`이나 `develop`에서 직접 개발하지 않습니다.

작업을 시작하기 전에 `develop`을 최신 상태로 갱신합니다.

```bash
git switch develop
git pull origin develop
```

이후 작업 단위로 새로운 Branch를 생성합니다.

브랜치는 아래 Git 컨벤션의 Type [`8. Git Convention`](#8-git-convention)을 사용하여 다음 형식을 따릅니다.

```text
type/간단한-설명
```

> Git 브랜치 이름에는 `:`를 사용할 수 없으므로, Commit Message의 `type: 설명` 형식과 달리 `/`를 사용합니다.

예:

```bash
git switch -c feat/data-preprocessing
git switch -c feat/eda
git switch -c fix/missing-value
git switch -c docs/update-readme
```

가능하면 사람 이름이 아니라 **작업 내용을 기준으로 Branch를 생성**합니다.

---

## 1.4 어디에서 작업해야 하나요?

### 개인 분석 / EDA / 실험

자신의 Notebook 디렉터리를 사용합니다.

```text
notebooks/
├── GH/
├── JW/
├── JY/
└── final/
```

예:

```text
notebooks/GH/01_eda.ipynb
notebooks/JW/01_preprocessing.ipynb
notebooks/JY/01_model_test.ipynb
```

다른 팀원의 개인 Notebook을 직접 수정하지 않는 것을 원칙으로 합니다.

### 공통으로 사용할 코드

Notebook에서 검증한 코드 중 반복 사용하거나 다른 팀원도 사용해야 하는 로직은 `src/`로 분리합니다.

```text
src/
├── crawling/
├── modeling/
└── preprocessing/
```

구분 원칙:

```text
Notebook = 탐색 / 실험 / EDA / 결과 확인
src/     = 재사용 가능한 공통 Python 코드
tests/   = src 코드 검증
scripts/ = 반복 실행 작업
```

---

## 1.5 Push 전 Local Check

Pull Request를 만들기 전에 다음 검사를 수행합니다.

### Ruff

```bash
ruff check .
```

### pytest

```bash
pytest -m "not integration and not gpu"
```

두 검사가 모두 통과하면 Commit 및 Push합니다.

```bash
git add .
git commit -m "feat: 데이터 전처리 로직 추가"
git push -u origin <type>/<task-name>
```

Commit Message는 다음 형식을 사용합니다.

```text
type: 간단한 설명
```

예:

```text
feat: 데이터 전처리 로직 추가
fix: 결측치 처리 오류 수정
docs: README 수정
```

자세한 Type 정의는 [`8. Git Convention`](#8-git-convention)을 참고합니다.

---

## 1.6 Pull Request

일반 작업은 다음 방향으로 Pull Request를 생성합니다.

```text
type/*
   ↓
develop
```

예: `feat/*`, `fix/*`, `docs/*`, `test/*`
자세한 Type 정의는 [`8. Git Convention`](#8-git-convention)을 참고합니다.

`develop`에 충분히 검증된 변경 사항을 반영한 뒤 최종적으로:

```text
develop
   ↓
 main
```

방향으로 통합합니다.

`main`과 `develop`에는 Repository Rule이 적용되어 있으므로 Pull Request 및 CI 등 설정된 조건을 만족한 뒤 Merge합니다.

---

# 2. 연구실 PC 사용 방법

연구실 PC는 최종 제출 서버가 아니라 **개인 PC에서 수행하기 어려운 데이터 분석과 연산을 지원하기 위한 공용 개발 자원**입니다.

필요하지 않은 작업은 개인 PC에서 진행해도 됩니다.

대용량 데이터나 연구실 연산 자원이 필요한 경우 SSH로 연구실 PC에 접속한 뒤 공용 Docker Container를 사용합니다.

```text
개인 PC
   │
   │ SSH
   ▼
연구실 PC
   │
   ▼
공용 Docker Container
```

Container 내부에서는 모든 팀원이 동일한 Linux 사용자 `stat` 계정을 공유합니다.

따라서 팀원별 작업 충돌을 방지하기 위해 **Git Workspace는 반드시 분리**합니다.

---

## 2.1 팀원별 Workspace

연구실 Container에서는 다음 구조를 사용합니다.

```text
/home/stat/workspace/
├── GH/
│   └── BigData_Analysis_Team-2/
├── JW/
│   └── BigData_Analysis_Team-2/
└── JY/
    └── BigData_Analysis_Team-2/
```

각 디렉터리의 Repository는 모두 동일한 원격 Repository를 사용합니다.

```text
https://github.com/romain1121/BigData_Analysis_Team-2.git
```

동일한 Container를 공유하더라도 각 Workspace는 별도의 Git Working Tree이므로 서로 다른 Branch를 사용할 수 있습니다.

공용 Repository 디렉터리 하나를 여러 명이 함께 수정하지 않습니다.

---

## 2.2 개인 PC의 작업을 연구실 PC에서 실행

개인 PC에서 작업한 코드를 GitHub에 Push합니다.

```bash
git push
```

이후 연구실 PC에서 자신의 Workspace로 이동하여 해당 Branch를 가져옵니다.

예:

```bash
cd /home/stat/workspace/<NAME>/BigData_Analysis_Team-2

git fetch origin
git switch <working-branch>
git pull
```

즉 GitHub를 개인 PC와 연구실 PC 사이의 코드 동기화 기준으로 사용합니다.

```text
개인 PC
   │
   │ push
   ▼
 GitHub
   │
   │ pull
   ▼
연구실 PC
```

### GitHub 인증 주의

연구실 Container에서는 모든 팀원이 동일한 `stat` 계정을 사용합니다.

따라서 개인 PAT, SSH Private Key 등 개인 GitHub 인증정보를 공용 Home Directory에 장기간 저장하지 않는 것을 권장합니다.

가능하면:

- **개인 PC:** Commit / Push / PR
- **연구실 PC:** Pull / 데이터 분석 / 연산

중심으로 사용합니다.

또한 연구실 PC에서 Git Commit을 해야 하는 경우 `git config --global user.name`처럼 전역 설정을 사용하지 말고, 각 Clone에서 `--local` 설정을 사용합니다.

---

## 2.3 공용 Python 환경

연구실 Container에는 팀 프로젝트용 Python 가상환경을 사용합니다.

```text
/home/stat/.venvs/bigdata-team2/
```

활성화:

```bash
source /home/stat/.venvs/bigdata-team2/bin/activate
```

Python 기준 버전:

```text
Python 3.11
```

팀 프로젝트의 공통 Dependency 변경은 개인이 임의로 Container의 기본 Python 환경에 설치하기보다 `requirements.txt` 또는 `requirements-dev.txt`를 변경하여 팀에 공유하는 것을 원칙으로 합니다.

---

## 2.4 공용 데이터

실제 연구 데이터는 GitHub에 저장하지 않습니다.

연구실 PC의 공용 데이터 경로:

```text
/home/stat/storage/data/
├── raw/
├── interim/
└── processed/
```

| Directory | 역할 |
| --- | --- |
| `raw/` | 수집한 원본 데이터 |
| `interim/` | 전처리 과정의 중간 결과 |
| `processed/` | 분석 및 모델링에 사용할 가공 데이터 |

특히 `raw/` 데이터는 가능한 한 직접 수정하지 않습니다.

프로젝트 코드에서는 연구실 PC의 절대경로를 직접 하드코딩하지 않고 `BIGDATA_DATA_DIR` 환경변수를 기준으로 접근합니다.

예:

```python
import os
from pathlib import Path

DATA_DIR = Path(os.environ["BIGDATA_DATA_DIR"])

RAW_DIR = DATA_DIR / "raw"
INTERIM_DIR = DATA_DIR / "interim"
PROCESSED_DIR = DATA_DIR / "processed"
```

---

# 3. 반드시 지켜야 할 협업 규칙

1. `main`, `develop`에서 직접 개발하지 않습니다.
2. 새로운 작업은 Git Convention에 따라 `type/*` Branch에서 수행합니다. (예: `feat/*`, `fix/*`, `docs/*`)
3. 개인 분석은 자신의 `notebooks/<name>/`에서 수행합니다.
4. 동일한 개인 Notebook을 여러 명이 동시에 수정하지 않습니다.
5. 반복 사용하거나 공유할 코드는 `src/`로 분리합니다.
6. 실제 연구 데이터는 GitHub에 Commit하지 않습니다.
7. 개인 Notebook에는 `nbstripout`을 적용합니다.
8. PR 전에 `ruff check .`와 `pytest`를 실행합니다.
9. GitHub Actions CI가 실패한 상태에서는 Merge하지 않습니다.
10. 연구실 Docker Container에서도 팀원별 Git Workspace를 사용합니다.
11. API Key, Password, `.env`, 개인 인증정보 등 민감정보를 Commit하지 않습니다.
12. 새로운 공통 Dependency가 필요하면 `requirements.txt` 또는 `requirements-dev.txt`에 반영합니다.
13. 연구실 Container의 공용 Python 환경을 임의로 변경하지 않습니다.

---

# 4. Project Structure

```text
BigData_Analysis_Team-2/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── configs/
│
├── notebooks/
│   ├── final/
│   ├── GH/
│   ├── JW/
│   └── JY/
│
├── scripts/
│
├── src/
│   ├── crawling/
│   ├── modeling/
│   └── preprocessing/
│
├── tests/
│
├── .gitattributes
├── .gitignore
├── pyproject.toml
├── README.md
├── requirements-dev.txt
└── requirements.txt
```

## 주요 디렉터리

### `notebooks/`

개인별 EDA, 실험, 분석 및 시각화를 수행합니다.

`notebooks/final/`은 최종 분석 결과 및 제출용 Notebook을 정리하는 공간입니다.

### `src/crawling/`

데이터 수집 관련 공통 코드를 관리합니다.

### `src/preprocessing/`

데이터 정제, 전처리, Feature Engineering 관련 공통 코드를 관리합니다.

### `src/modeling/`

모델 학습 및 평가 관련 공통 코드를 관리합니다.

### `tests/`

`src/`의 공통 코드에 대한 pytest를 작성합니다.

### `scripts/`

반복 실행할 필요가 있는 데이터 처리·학습·평가 작업을 Script로 관리합니다.

### `configs/`

공통 Hyperparameter나 실행 설정 등이 필요한 경우 사용합니다.

개인 PC에 종속되는 경로나 민감정보는 저장하지 않습니다.

---

# 5. Git Branch Strategy

```text
main
 ↑
develop
 ↑
type/*
```

### `main`

최종적으로 검증된 결과를 관리합니다.

### `develop`

팀원들의 작업 결과를 통합하는 Branch입니다.

### `type/*`

실제 개발 및 분석 작업 Branch입니다.

Git Convention의 Type을 Branch Prefix로 사용합니다.

예:

```text
feat/crawling
feat/data-preprocessing
feat/eda
fix/missing-value
test/preprocessing
docs/update-readme
ci/update-workflow
```

브랜치 이름은 다음 형식을 따릅니다.

```text
type/간단한-설명
```

하나의 개인 Branch를 장기간 유지하기보다 **작업 단위로 Branch를 생성하고 Merge한 뒤 종료**하는 방식을 권장합니다.

---

# 6. Continuous Integration

GitHub Actions를 통해 코드 품질과 기본 테스트를 자동 검증합니다.

CI는 `develop` 또는 `main`을 대상으로 하는 Pull Request와 해당 Branch에 대한 Push에서 실행됩니다.

현재 Pipeline:

```text
Checkout
   ↓
Python 3.11
   ↓
Install Dependencies
   ↓
Ruff
   ↓
Notebook Output Check
   ↓
pytest
   ↓
PASS / FAIL
```

## 6.1 Ruff

```bash
ruff check .
```

현재 주요 규칙:

- `E` — pycodestyle errors
- `F` — Pyflakes
- `I` — import sorting
- `B` — common bug patterns

`notebooks/`는 Ruff 검사 대상에서 제외합니다.

---

## 6.2 pytest

일반 Unit Test는 CI에서 실행합니다.

```bash
pytest -m "not integration and not gpu"
```

실제 데이터, 네트워크 등 외부 환경이 필요한 테스트는 다음 Marker를 사용합니다.

```python
import pytest


@pytest.mark.integration
def test_with_external_resource():
    ...
```

GPU가 필요한 테스트:

```python
@pytest.mark.gpu
def test_gpu_model():
    ...
```

`integration`, `gpu` Marker가 지정된 테스트는 기본 GitHub CI에서 제외하고 필요한 환경에서 별도로 실행합니다.

---

## 6.3 Notebook Output Check

CI는 다음 개인 Notebook 디렉터리에 Output이 남아 있는지 확인합니다.

```text
notebooks/GH/
notebooks/JW/
notebooks/JY/
```

`notebooks/final/`은 최종 결과 보존을 위해 검사 대상에서 제외합니다.

---

# 7. Dependency Management

## Runtime Dependencies

`requirements.txt`에는 프로젝트에서 직접 사용하는 공통 Library를 관리합니다.

현재 주요 범위:

- Crawling
- Data Analysis
- Machine Learning
- Visualization

연구실 Container의 전체 `pip list`를 그대로 관리하지 않고, **프로젝트가 실제로 사용하는 Dependency만 관리**합니다.

## Development Dependencies

`requirements-dev.txt`에는 `requirements.txt`를 포함하여 다음 도구를 관리합니다.

```text
ruff
pytest
nbstripout
```

새로운 공통 Library가 필요하다면 개인 환경에만 설치한 채 사용하지 말고 Repository의 Dependency 파일에도 반영합니다.

---

# 8. Git Convention

Branch와 Commit Message는 동일한 **Type 체계**를 사용합니다.

## 8.1 Branch Naming

Git 브랜치 이름에는 `:`를 사용할 수 없으므로 다음 형식을 사용합니다.

```text
type/간단한-설명
```

예:

```text
feat/data-preprocessing
fix/missing-value
docs/update-readme
test/preprocessing
ci/update-workflow
```

## 8.2 Commit Message

Commit Message는 다음 형식을 사용합니다.

```text
type: 간단한 설명
```

예:

```text
feat: 데이터 전처리 로직 추가
fix: 결측치 처리 오류 수정
docs: README 협업 가이드 수정
test: 전처리 단위 테스트 추가
ci: GitHub Actions 설정 수정
```

## 8.3 Type

| Type | 설명 | 비고 |
| :--- | :--- | :--- |
| **`feat`** | **새로운 기능 추가** | 사용자 또는 분석 흐름에 영향을 주는 새로운 기능·로직 |
| **`fix`** | **버그 수정** | 실행 결과에 영향을 주는 오류 수정 |
| **`docs`** | **문서 수정** | README.md, 주석 등 코드 로직과 무관한 문서 변경 |
| **`style`** | **코드 포맷팅** | **비즈니스·분석 로직 변경 없음**. 오타, 공백, 포맷 등의 변경 |
| **`refactor`** | **코드 리팩토링** | 결과는 같으나 변수명, 함수 구조 등 코드를 개선 |
| **`perf`** | **성능 개선** | 실행 시간 단축, 메모리 효율 개선 등 |
| **`test`** | **테스트 코드** | 테스트 코드 추가, 수정, 삭제 |
| **`build`** | **빌드 시스템·종속성 변경** | 패키지 설치·삭제, Dependency 및 빌드 관련 설정 변경 |
| **`ci`** | **CI 구성 파일 변경** | GitHub Actions 등 CI 설정 및 스크립트 변경 |
| **`chore`** | **기타 자잘한 수정** | `.gitignore` 등 소스 로직과 직접 관련 없는 관리 작업 |
| **`revert`** | **커밋 되돌리기** | 이전 Commit을 취소할 때 사용 |

### 예시

```text
Branch: feat/model-baseline
Commit: feat: 베이스라인 모델 학습 로직 추가

Branch: fix/date-parsing
Commit: fix: 수집 데이터 날짜 파싱 오류 수정

Branch: docs/update-readme
Commit: docs: 연구실 PC 사용 가이드 추가
```

---

# 9. Research Lab Environment

연구실에서 제공하는 공용 Docker Container의 확인된 환경은 다음과 같습니다.

| Item | Environment |
| --- | --- |
| OS | Debian GNU/Linux 12 (bookworm) |
| Linux User | `stat` |
| Python | 3.11.15 |
| Python Path | `/opt/conda/bin/python` |
| pip | 26.1.2 |
| Git | 2.39.5 |
| PyTorch | 2.7.1+cu118 |
| PyTorch CUDA Runtime | 11.8 |
| GPU | NVIDIA GeForce GTX 1060 6GB |

## GPU 상태

교수님께서는 연구실 GPU 자원도 Container에서 사용할 수 있도록 설정했다고 안내하셨습니다.

현재 Container 내부에서는 GPU Device와 Driver 정보가 확인됩니다.

```text
NVIDIA GeForce GTX 1060 6GB
NVIDIA Driver 535.309.01
```

다만 현재 점검에서는 다음 상태가 확인되었습니다.

```text
nvidia-smi
→ Failed to initialize NVML: Unknown Error

torch.cuda.is_available()
→ False
```

따라서 **GPU 사용은 현재 추가 점검 중이며, 아직 정상 사용 가능 상태로 확정하지 않습니다.**

GPU가 필요한 작업을 진행하기 전 다음을 확인합니다.

```bash
nvidia-smi
```

```bash
python - <<'PY'
import torch

print("Torch:", torch.__version__)
print("CUDA runtime:", torch.version.cuda)
print("CUDA available:", torch.cuda.is_available())
print("GPU count:", torch.cuda.device_count())

if torch.cuda.is_available():
    print("GPU:", torch.cuda.get_device_name(0))
PY
```

GPU 관련 문제가 있을 경우 PyTorch를 임의로 재설치하기 전에 연구실 PC의 Host / Docker GPU 전달 설정을 우선 확인합니다.

---

# 10. Troubleshooting

## Windows에서 `nbstripout` 설치 시 `UnicodeDecodeError`

다음과 같은 오류가 발생할 수 있습니다.

```text
UnicodeDecodeError: 'cp949' codec can't decode ...
```

`.gitattributes`에 UTF-8 한글 주석이 포함되어 있을 때 Windows Python이 CP949로 파일을 읽으며 발생할 수 있습니다.

`.gitattributes`는 UTF-8로 저장하고 주석은 영문으로 유지합니다.

---

## CI에서 빈 디렉터리를 찾지 못하는 경우

Git은 빈 Directory 자체를 추적하지 않습니다.

Repository에 빈 Directory를 유지해야 하는 경우 `.gitkeep` 파일을 둡니다.

예:

```text
scripts/.gitkeep
configs/.gitkeep
```

---

# 11. Summary

프로젝트는 다음 원칙으로 운영합니다.

```text
GitHub
= 코드 / Notebook / 버전 관리 / CI

Notebook
= 개인 분석 / EDA / 실험

src/
= 재사용 가능한 공통 코드

tests/
= 공통 코드 검증

연구실 PC
= 공용 데이터 / CPU·GPU 연산 자원

type/*
→ Pull Request
→ CI
→ develop
→ 최종 검증
→ main
```

Repository나 연구실 환경의 운영 방식이 변경될 경우 README도 함께 갱신합니다.
