---
title: Azure Batch AI 서비스 개요 | Microsoft Docs
description: Managed Azure Batch AI 서비스를 사용하여 인공 지능 (AI) 및 기타 기계 학습 모델을 GPU 및 CPU 클러스터에서 교육하는 방법에 대해 알아보십시오.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: timlt
editor: ''

ms.assetid: 
ms.service: batch-ai
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2017
ms.author: asutton
ms.custom: 

---
# Azure에서 Batch AI란 무엇인가요?
Batch AI는 데이터 과학자와 인공 지능 연구원이 GPU를 지원하는 VM을 비롯하여 Azure 가상 시스템 클러스터에서 AI 및 기타 기계 학습 모델을 학습 할 수있게 해주는 관리 형 서비스입니다. 작업 요구 사항을 설명하고, 입력을 찾고 출력을 저장할 위치를 지정하고, 배치 AI가 나머지 작업을 처리합니다.
 
## 왜 Batch AI를 사용해야하나요? 
강력한 인공 지능 알고리즘을 개발하는 것은 계산 집약적이고 반복적 인 프로세스입니다. 데이터 과학자와 인공 지능 연구원은 점점 더 큰 데이터 세트로 작업하고 있습니다. 그들은 더 많은 레이어를 가진 모델을 개발하고 있으며, 하이퍼 파라미터 튜닝에 대한 네트워크 설계에 대한 더 많은 실험을 통해이를 수행합니다. 이를 효율적으로 수행하려면 모델 당 여러 개의 CPU 또는 GPU가 필요하며, 실험을 병렬로 실행하고 교육 데이터, 로그 및 모델 출력을위한 공유 스토리지가 있어야합니다.
 
![Batch AI process](media/overview/batchai-context.png)

데이터 과학자와 인공 지능 연구원은 자신의 분야에서 전문가이지만 규모에 따른 인프라 관리는 방해가 될 수 있습니다. AI를 대규모로 개발하려면 VM의 클러스터 프로비저닝, 소프트웨어 및 컨테이너 설치, 작업 대기열 할당, 작업 우선 순위 지정 및 스케줄 지정, 실패 전달, 데이터 공유, 비용 공유를위한 리소스 확장 및 도구 및 워크 플로우와의 통합이 필요합니다. 배치 AI가 이러한 작업을 처리합니다.
 
## Batch AI가 무엇인가요? 
Batch AI는 AI 교육 및 테스트에 특화된 자원 관리 및 작업 스케줄링을 제공합니다. 주요 기능은 다음과 같습니다.

* 장기 실행 일괄 처리 작업, 반복적 인 실험 및 대화식 교육 실행
* GPU 또는 CPU를 사용하는 VM 클러스터의 자동 또는 수동 확장
* VM 간 및 원격 액세스 용 SSH 통신 구성
* [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), [TensorFlow](https://www.tensorflow.org/), and [Chainer](https://chainer.org/) 와 같은 널리 사용되는 툴킷에 최적화 된 구성으로 Deep Learning 또는 기계 학습 프레임 워크를 지원합니다.
* 클러스터를 공유하고 우선 순위가 낮은 VM 및 예약 인스턴스를 활용하는 우선 순위 기반 작업 큐
* Azure 파일 및 관리되는 NFS 서버를 포함한 유연한 저장소 옵션
* 원격 파일 공유를 VM 및 선택적 컨테이너에 마운트
* VM 오류 발생시 작업 상태 제공 및 재시작
* Azure Storage의 스트리밍을 포함하여 출력 로그, stdout, stderr 및 모델에 대한 액세스
Azure [command-line interface](/cli/azure/overview) (CLI), SDKs for [Python](https://github.com/Azure/azure-sdk-for-python), [C#](https://www.nuget.org/packages/Microsoft.Azure.Management.BatchAI/1.0.0-preview) 및 Java, Azure 포털에서 모니터링 및 Microsoft AI 도구와의 통합

Batch AI SDK는 교육 파이프 라인을 관리하고 도구와 통합하기위한 스크립트 또는 응용 프로그램 작성을 지원합니다. 현재 SDK는 Python, C #, Java 및 REST API를 제공합니다.
Batch AI는 제어 평면 작업 (생성, 나열, 가져 오기, 삭제)에 Azure Resource Manager를 사용합니다. Azure Active Directory는 인증 및 역할 기반 액세스 제어에 사용됩니다.
 
## Batch AI를 어떻게 사용하나요?
Batch AI를 사용하려면 *클러스터* 및 *작업*을 정의하고 관리하십시오.
 
**Clusters** 에서는 계산의 요구사항을 설정합니다: 
* 실행하려는 Azure 지역
* 사용할 VM의 제품군 및 크기 - 예를 들어 네 개의 NVIDIA K80 GPU가 포함 된 NC24 VM
* VM의 수 또는 자동 크기 조절을위한 최소 및 최대 수
* VM 이미지 - 예 : Ubuntu 16.04 LTS 또는 [Microsoft Deep Learning Virtual Machine] (https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* Azure 파일 또는 Batch AI에서 관리하는 NFS 서버와 같이 마운트 할 원격 파일 공유 볼륨
디버깅을 위해 대화식 로그인을 사용하도록 VM에 구성 할 수있는 사용자 이름 및 SSH 키 또는 암호

**Jobs** 설정 내용: 
* 사용할 클러스터 및 지역
* 작업에 필요한 VM 수
* 시작할 때 작업에 전달할 디렉토리를 입력 및 출력합니다. 일반적으로 클러스터 설정 중에 마운트 된 공유 파일 시스템을 사용합니다.
* 소프트웨어 또는 설치 스크립트를 실행하기위한 선택 사항 컨테이너
* AI 프레임 워크 특정 구성 또는 작업을 시작하는 명령 줄 및 매개 변수

클러스터 및 작업을위한 [Azure CLI](/cli/azure/overview)  및 구성 파일과 함께 배치 AI를 사용하여 시작하십시오. 이 방법을 사용하여 필요할 때 클러스터를 빠르게 만들고 작업을 실행하여 네트워크 디자인이나 하이퍼 매개 변수를 시험해보십시오.

Batch AI를 사용하면 여러 GPU와 병렬로 쉽게 작업 할 수 있습니다. 여러 GPU에서 작업을 확장해야하는 경우 Batch AI는 VM간에 안전한 네트워크 연결을 설정합니다. InfiniBand를 사용하면 Batch AI가 드라이버를 구성하고 작업의 노드에서 MPI를 시작합니다.


## 데이터 관리
Batch AI는 교육 스크립트, 데이터 및 출력에 유연한 옵션을 제공합니다.

* 초기 실험 및 작은 데이터 세트에는 **로컬 디스크**를 사용하십시오. 이 시나리오에서는 SSH를 통해 가상 시스템에 연결하여 스크립트를 편집하고 로그를 읽을 수 있습니다.
* **Azure 파일**을 사용하여 여러 작업에서 교육 데이터를 공유하고 출력 로그와 모델을 단일 위치에 저장하십시오.
* 대규모 NFS 및 교육용 VM을 지원하려면 **NFS 서버**를 설치하십시오. Batch AI는 Azure Storage에 백업 된 디스크를 사용하여 NFS 서버를 특별한 클러스터 유형으로 설정할 수 있습니다.
* **병렬 파일 시스템**은 데이터 및 병렬 학습을위한 확장 성을 제공합니다. Batch AI는 병렬 파일 시스템을 관리하지 않지만 예제 배포 템플릿은 Lustre, Gluster 및 BeeGFS에서 사용할 수 있습니다.

## 다음 단계
* [Azure CLI](quickstart-cli.md) 또는 [Python](quickstart-python.md)을 사용하여 첫 번째 Batch AI 교육 작업을 시작하십시오.
* 다양한 프레임 워크에 대한 샘플 [training recipes](https://github.com/Azure/BatchAI)를 확인하십시오.

