---
title: Git blog 사용법 정리
author: knutdarara
date: 2024-11-19
categories: ["git","git page"]
layout: post
---
# 처음 셋업을 위해서 필요한 정보 정리.
1. 현재 환경
   - 환경 셋업 방법 정리
2. git action
3. 테마 설정
   - 정보, 필요한 파일
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
nodeRegistration:
  criSocket: /run/containerd/containerd.sock
  kubeletExtraArgs:
    cgroup-driver: "cgroupfs"
------------------
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://download.opensuse.org/repositories/isv:/kubernetes:/core:/stable:/v1.33:/build/rpm/
enabled=1
gpgcheck=1
gpgkey=https://download.opensuse.org/repositories/isv:/kubernetes:/core:/stable:/v1.33:/build/rpm/repodata/repomd.xml.key
EOF

--------
sudo ctr image pull docker.io/cilium/cilium:v1.17.3
sudo ctr image pull docker.io/cilium/operator-generic:v1.17.3
sudo ctr image pull registry.k8s.io/pause:3.9=

sudo ctr image export cilium.tar docker.io/cilium/cilium:v1.17.3
sudo ctr image export operator.tar docker.io/cilium/operator-generic:v1.17.3
sudo ctr image export pause.tar registry.k8s.io/pause:3.9(

helm repo add cilium https://helm.cilium.io
helm pull cilium/cilium --version 1.17.3 --untar
tar czvf cilium-1.17.3.tgz cilium/

  
-------------------
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
kubernetesVersion: "v1.33.0"
networking:
  podSubnet: "10.244.0.0/16"


---------
# CLI 버전 수동 지정
CLI_VERSION=v0.15.19

# 다운로드
curl -LO https://github.com/cilium/cilium-cli/releases/download/${CLI_VERSION}/cilium-linux-amd64.tar.gz
curl -LO https://github.com/cilium/cilium-cli/releases/download/${CLI_VERSION}/cilium-linux-amd64.tar.gz.sha256sum

# 검증
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum

# 설치
sudo tar xzvf cilium-linux-amd64.tar.gz -C /usr/local/bin
---------
version = 2

[plugins]
  [plugins."io.containerd.grpc.v1.cri"]
    sandbox_image = "registry.k8s.io/pause:3.9"

    [plugins."io.containerd.grpc.v1.cri".containerd]
      snapshotter = "overlayfs"
      default_runtime_name = "runc"

      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]

        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
          runtime_type = "io.containerd.runc.v2"

          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            SystemdCgroup = false


이 구성을 보면 **완전한 클라우드 네이티브 인프라**를 구축하려는 의도 같네요. 각 요소들이 잘 결합되어 있어 **확장성, 관리 용이성, 고가용성** 등을 모두 고려한 형태입니다. 각 요소의 역할과 어떻게 연결될 수 있는지 구체적으로 설명드리겠습니다.

### 1. **OS & Kernel 설정**
   - **OS**: Ubuntu 24.04는 최신 안정 버전으로, **Kubernetes** 및 다양한 클라우드 네이티브 도구와 호환이 좋습니다.
   - **Kernel**: `6.8.0-55-generic` 커널 버전은 최신 리눅스 커널로, **Cilium**과 같은 최신 네트워크 솔루션을 지원합니다. Kubernetes와 함께 잘 동작합니다.

### 2. **RKE2 (Rancher Kubernetes Engine 2)**
   - **RKE2 v1.32.2+rke2r1**: 이 버전은 Rancher에서 제공하는 **경량화된 Kubernetes** 배포판으로, 간편하고 빠르게 Kubernetes 클러스터를 구축할 수 있습니다.
   - **RKE2**는 기존 **K8s**를 기반으로 하되, 클라우드 네이티브 환경에서 성능을 최적화하는 방향으로 개선되었습니다.  
   - **K8s**보다 더 간편하게 **보안, 구성 관리**와 **운영**을 할 수 있습니다.

### 3. **네트워크 설정: Cilium + NGINX**
   - **Cilium**: 최신 **L3/L4/L7** 네트워크 보안 및 서비스 메시 솔루션입니다. Kubernetes와 완벽히 통합되며, **고성능의 네트워크 정책**을 지원합니다. 특히 **eBPF**(Extended Berkeley Packet Filter)를 사용하여 성능이 뛰어나며, **네트워크 모니터링, 보안 정책, 로깅** 등을 제공합니다.
   - **NGINX**: **Ingress Controller**로 사용되어 클러스터 외부와의 트래픽을 처리합니다. 이를 통해 **외부 접속**과 **로드 밸런싱**을 효율적으로 관리할 수 있습니다.

### 4. **Rancher UI**
   - **Rancher**는 **Kubernetes 클러스터 관리 도구**로, 여러 클러스터를 **단일 UI**에서 관리할 수 있게 해줍니다.  
   - UI를 통해 **클러스터 관리, 롤백, 리소스 배포**를 쉽게 할 수 있습니다.  
   - **Rancher**는 특히 **여러 K8s 클러스터**를 운영할 때 유용합니다.

### 5. **Rook-Ceph**
   - **Rook**는 **클라우드 네이티브 스토리지 솔루션**으로, **Ceph**를 Kubernetes 환경에 쉽게 통합할 수 있게 도와줍니다.
   - **Ceph**는 **분산 스토리지**로, 매우 확장 가능하고 고가용성 저장소를 제공합니다.
   - **Rook-Ceph**는 Kubernetes에서 **자동화된 스토리지 관리**를 통해 **블록 스토리지, 객체 스토리지**를 제공할 수 있게 해줍니다.

### 6. **Minio**
   - **Minio**는 **고성능 오브젝트 스토리지**로, AWS S3와 호환됩니다.
   - **Rook-Ceph**와 함께 사용할 수도 있고, **독립적인 오브젝트 스토리지**로도 사용할 수 있습니다.
   - 클라우드 네이티브 환경에서 **S3 호환 API**를 사용하는 **스토리지 서비스**로 Minio는 매우 유용합니다.

### 7. **LGTM & Alloy**
   - **LGTM**: **Continuous Integration (CI) 도구**로 코드 리뷰 및 품질 검사 도구입니다.
   - **Alloy**: **소프트웨어 품질 관리** 및 **보안 점검**을 위한 도구로, 개발 프로세스에 중요한 역할을 합니다.

### 8. **Harbor**
   - **Harbor**는 **컨테이너 레지스트리**로, Docker 이미지 저장소를 **관리**할 수 있는 시스템입니다.
   - Kubernetes 환경에서는 **CI/CD 파이프라인**과 함께 컨테이너 이미지를 배포하기 전에 **이미지 서명, 보안 스캔** 등을 할 수 있습니다.

### 9. **ArgoCD**
   - **ArgoCD**는 **GitOps 기반의 지속적 배포(CD)** 도구입니다.  
   - Git 리포지토리를 소스로 하여 Kubernetes 클러스터에 배포된 애플리케이션을 **자동으로 관리**하고 배포할 수 있게 합니다.
   - **배포 자동화** 및 **클러스터 동기화**를 지원하여 DevOps 환경에서 매우 중요한 역할을 합니다.

### 10. **GitLab**
   - **GitLab**은 **버전 관리 및 CI/CD 플랫폼**입니다.  
   - GitLab을 통해 **코드 저장소 관리**와 함께 **CI/CD 파이프라인**을 설정하여, **ArgoCD**와 연계해서 애플리케이션을 **자동으로 배포**할 수 있습니다.

### 11. **Istio (트래픽 이원화)**
   - **Istio**는 **서비스 메시**로, **서비스 간 트래픽 관리** 및 **보안**을 처리합니다.
   - **트래픽 이원화**(canary 배포, A/B 테스트 등) 등을 **자동화**할 수 있습니다.
   - Istio를 사용하면 **트래픽 라우팅, 인증/인가, 로깅, 모니터링** 등 많은 기능을 제공하여 **클러스터 내 서비스간 통신**을 보다 효율적으로 관리할 수 있습니다.

---

### 제안된 구조와 역할 정리

| 구성 요소         | 역할                                      |
|------------------|-------------------------------------------|
| **Ubuntu 24.04**  | 운영체제                                   |
| **RKE2**          | 경량화된 Kubernetes 클러스터               |
| **Cilium**        | 고성능 네트워크 보안 및 서비스 메시       |
| **NGINX**         | Ingress Controller 및 로드 밸런싱         |
| **Rancher UI**    | Kubernetes 클러스터 관리 UI               |
| **Rook-Ceph**     | 분산 스토리지 시스템 (블록/객체 스토리지) |
| **Minio**         | S3 호환 오브젝트 스토리지 서비스         |
| **Harbor**        | Docker 이미지 레지스트리                  |
| **ArgoCD**        | GitOps 기반 지속적 배포(CD)               |
| **GitLab**        | 버전 관리 및 CI/CD 파이프라인             |
| **Istio**         | 서비스 메시 및 트래픽 이원화              |

---

## 결론
이 구성을 사용하면 **확장성, 관리 효율성, 보안성, 그리고 지속적 배포**가 모두 강력히 지원되는 **클라우드 네이티브 환경**을 구축할 수 있습니다.  
**Istio와 ArgoCD**를 추가하면 **트래픽 라우팅, 자동화된 배포, 지속적 통합/배포**가 매우 효율적으로 이루어질 수 있습니다.  
이제 설치와 구성 단계를 하나씩 진행하면서, 각 요소를 연결하고 최적화하면 좋을 것 같습니다!