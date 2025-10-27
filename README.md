# 2025_laidd_drugtarget

## ARACNe 실습
ARACNe-AP(Algorithm for the Reconstruction of Accurate Cellular Networks - Adaptive Partitioning)를 사용하여 유전자 발현 데이터로부터 전사조절 네트워크를 추론
ARACNe-AP는 Mutual Information을 기반으로 전사인자(TF)와 타겟 유전자 간의 조절 관계를 식별하는 알고리즘

## 설치
### 1. 디렉토리 생성

```bash
mkdir -p ~/tools/aracne
cd ~/tools/aracne
```

### 2. 저장소 클론 및 빌드

```bash
git clone https://github.com/jyryu3161/ARACNe-AP.git
cd ARACNe-AP
ant main
```
> **참고**: 원본 ARACNe-AP 코드는 최신 Java 버전 등 빌드 환경의 차이로 인해 `build.xml` 파일 수정이 필요
> 본 저장소는 원본 코드를 fork하여 빌드 문제를 해결한 버전

### 3. 필요 환경
- Java Runtime Environment (JRE)
- Apache Ant (빌드용)
- 최소 5GB 메모리

## 사용법

### 입력 파일 준비

1. **발현 데이터 (`expr.txt`)**: UCSC Xena에서 다운받은 RNA-seq 데이터
2. **전사인자 리스트 (`tf_list.txt`)**: 전사인자 gene symbol 목록 (헤더 없이 한 줄에 하나씩)

### Threshold 계산

먼저 데이터 기반 MI threshold를 계산합니다:

```bash
java -Xmx5G -jar ./dist/aracne.jar \
  -e expr.txt \
  -o outputFolder \
  --tfs tf_list.txt \
  --pvalue 1E-8 \
  --seed 1 \
  --calculateThreshold
```

### 네트워크 추론 실행

계산된 threshold를 사용하여 네트워크를 추론합니다:

```bash
java -Xmx5G -jar ./dist/aracne.jar \
  -e expr.txt \
  -o outputFolder \
  --tfs tf_list.txt \
  --pvalue 1E-8 \
  --seed 1
```

### 주요 옵션

| 옵션 | 설명 |
|------|------|
| `-e` | 입력 발현 데이터 파일 |
| `-o` | 출력 디렉토리 |
| `--tfs` | 전사인자 리스트 파일 |
| `--pvalue` | p-value threshold (예: 1E-8) |
| `--seed` | 재현성을 위한 난수 시드 |
| `--calculateThreshold` | 데이터 기반 MI threshold 자동 계산 |

## 결과 해석

### 출력 파일

분석 결과는 `outputFolder`에 저장되며, 주요 출력 파일은 다음과 같습니다:

- 네트워크 파일: TF-target gene 간 조절 관계
- Threshold 파일: 계산된 MI threshold 값

### 네트워크 파일 형식

- **각 행**: TF–target gene 관계
- **값**: Mutual Information (MI) 스코어
- MI 스코어가 높을수록 TF와 타겟 유전자 간 조절 관계가 강함을 의미

## 참고 자료
- [ARACNe-AP 원본 저장소](https://github.com/califano-lab/ARACNe-AP)
- [수정된 코드 저장소](https://github.com/jyryu3161/ARACNe-AP)

