# Draw Things 설정 및 사용 가이드

> **M5 (macOS)** 및 **Ubuntu Linux + RTX 5090 Laptop (24GB)** 환경 기준
> 작성일: 2026-10-20

---

## 목차

1. [Draw Things란?](#1-draw-things란)
2. [M5 (macOS) 설치 및 설정](#2-m5-macos-설치-및-설정)
3. [Ubuntu Linux + RTX 5090 Laptop 설치 및 설정](#3-ubuntu-linux--rtx-5090-laptop-설치-및-설정)
4. [Linux 서버를 M5에서 클라이언트로 연결](#4-linux-서버를-m5에서-클라이언트로-연결)
5. [사용 예시](#5-사용-예시)
6. [문제 해결](#6-문제-해결)

---

## 1. Draw Things란?

Draw Things는 **Stable Diffusion 기반 이미지 생성/편집 앱**입니다.

| 기능 | 설명 |
|------|------|
| txt2img | 텍스트 프롬프트로 이미지 생성 |
| img2img | 기존 이미지를 참고하여 새 이미지 생성 |
| Inpainting | 이미지 일부 영역만 다시 그리기 |
| Outpainting | 이미지 경계 확장 |
| ControlNet | 포즈/에지/깊이 등으로 구조 제어 |
| LoRA | 스타일/캐릭터 미세 조정 모델 지원 |
| Core ML / Metal | Apple Silicon GPU 가속 |
| CUDA | NVIDIA GPU 가속 (Linux 서버) |

> ⚠️ **영상 생성 앱이 아닙니다.** 이미지 생성 전용입니다.

### 시스템 요구 사항

| 플랫폼 | 최소 사양 | 권장 사양 |
|--------|-----------|-----------|
| macOS (M5) | Apple Silicon, 8GB RAM | 16GB+ RAM, 충분한 SSD |
| Linux + NVIDIA | CUDA 지원 GPU, NVIDIA 드라이버 550+ | RTX 5090 Laptop (24GB), 32GB+ RAM |

---

## 2. M5 (macOS) 설치 및 설정

### 2.1 사전 준비

M5 칩은 Apple Silicon이므로 Core ML / Metal 가속이 기본 지원됩니다.

```bash
# macOS 버전 확인 (Sonoma 14.0 이상 권장)
sw_vers

# Apple Silicon 확인
uname -m   # arm64가 출력되어야 합니다

# 메모리 확인
sysctl -n hw.memsize | awk '{print $1/1073741824 " GB"}'
```

### 2.2 Draw Things 앱 설치

#### 방법 1: Mac App Store (권장)

1. Mac App Store 열기
2. **"Draw Things: AI Generation"** 검색
3. **"받기"** 버튼 클릭하여 설치 (무료)

> App Store 버전이 가장 안정적이고 자동 업데이트가 됩니다.

#### 방법 2: 수동 다운로드

1. [https://drawthings.ai](https://drawthings.ai) 접속
2. macOS용 다운로드 링크에서 `.dmg` 파일 다운로드
3. 다운로드한 `.dmg` 파일 열기 → Applications 폴더로 드래그

### 2.3 초기 실행 설정

1. **Draw Things 앱 실행**
2. 첫 실행 시 권한 요청:
   - 🔒 **네트워크 접근 허용** — 모델 다운로드에 필요
   - 📁 **파일 접근 허용** — 이미지 저장에 필요
3. 언어 선택 (한국어 지원)

### 2.4 모델 다운로드 및 설정

#### 기본 모델 자동 다운로드

1. 앱 상단 모델 선택 드롭다운 클릭
2. **"Download Models"** 탭으로 이동
3. 권장 시작 모델:

| 모델명 | 용도 | 용량 |
|--------|------|------|
| **Stable Diffusion 1.5** | 빠른 시작, 범용 | ~4GB |
| **SDXL 1.0** | 고품질 이미지 | ~7GB |
| **DreamShaper 8** | 애니메이션+실사 혼합 | ~2GB |

4. **"Download"** 클릭 → 다운로드 완료 후 자동으로 활성화

#### 모델 저장 위치 확인

기본 경로:
```
~/Library/Containers/com.liuliu.draw-things/Data/Documents/Models/
```

원하는 경로로 변경:
1. **Settings** (⌘ + ,) → **Models Folder** → **Change**
2. SSD 공간이 넉넉한 폴더 지정 권장

### 2.5 macOS 성능 최적화 설정

```
Settings (⌘ + ,) → Compute Unit
```

| 설정값 | 설명 |
|--------|------|
| **All** (권장) | GPU + Neural Engine + CPU 자동 분배 |
| **CPU & GPU** | 메모리 부족 시 안정적 |
| **CPU & Neural Engine** | 특정 모델에서 빠름 |
| **CPU Only** | 비상용 (매우 느림) |

추가 설정:
```
Settings → Memory Usage: "Automatic" 유지
Settings → Text Encoder: "Automatic" 유지
```

### 2.6 첫 번째 이미지 생성 (M5)

1. 메인 화면의 **Prompt 입력창**에 다음 입력:

```
a cute cat sitting on a windowscore, afternoon sunlight, photorealistic, detailed fur
```

2. **Negative Prompt** 입력:

```
blurry, low quality, deformed, ugly, extra limbs
```

3. 우측 설정 확인:

| 항목 | 권장값 |
|------|--------|
| Sampler | DPM++ 2M Karras |
| Steps | 25~30 |
| Resolution | 512×512 (SD1.5) / 1024×1024 (SDXL) |
| CFG Scale | 7 |
| Seed | -1 (랜덤) |

4. **"Generate"** (또는 ⌘ + Enter) 클릭
5. 이미지가 우측 패널에 표시됩니다

### 2.7 이미지 저장

- 생성된 이미지 우클릭 → **"Save Image..."**
- 또는 드래그하여 Finder로 바로 이동

---

## 3. Ubuntu Linux + RTX 5090 Laptop 설치 및 설정

> **RTX 5090 Laptop GPU: 24GB GDDR7**, Compute Capability 12.0 (Blackwell)
> Linux에서 Draw Things는 **CUDA gRPC 서버**로 동작합니다 (GUI 없음)

### 3.1 NVIDIA 드라이버 설치

#### RTX 5090 Laptop 지원 드라이버 확인

RTX 50 시리즈(Blackwell)는 **드라이버 570 이상** 필요합니다.

```bash
# 현재 드라이버 확인
nvidia-smi

# 출력 예시 (정상):
# Driver Version: 595.71.05   CUDA Version: 12.8
# GPU: NVIDIA GeForce RTX 5090 Laptop GPU   24564MiB
```

#### 드라이버가 없거나 구버전인 경우 설치

```bash
# 시스템 업데이트
sudo apt update && sudo apt upgrade -y

# PPA 추가 (최신 드라이버)
sudo add-apt-repository ppa:graphics-drivers/ppa -y
sudo apt update

# 권장 드라이버 설치 (595 이상 권장)
sudo apt install nvidia-driver-595 -y

# 재부팅
sudo reboot
```

#### 설치 확인

```bash
nvidia-smi
```

정상 출력 확인 사항:
- `Driver Version: 595.x` 이상
- `CUDA Version: 12.8` 이상
- `RTX 5090 Laptop GPU` 인식
- `24564MiB` 메모리 표시

### 3.2 CUDA Toolkit 설치 (서버 빌드 시)

Docker 이미지를 사용할 때는 **호스트에 CUDA Toolkit 불필요**합니다.
소스에서 직접 빌드할 때만 필요합니다.

```bash
# CUDA Toolkit 12.8 설치 (Docker 사용 시 건너뜀)
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt install cuda-toolkit-12-8 -y

# 환경 변수 추가 (~/.bashrc)
echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

### 3.3 Docker + NVIDIA Container Toolkit 설치

Draw Things Linux 서버의 **가장 간단한 설치 방법**입니다.

```bash
# Docker 설치
sudo apt install docker.io -y
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker

# NVIDIA Container Toolkit 설치
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | \
  sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg

curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
  sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
  sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt update
sudo apt install nvidia-container-toolkit -y

sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

#### GPU 접근 확인

```bash
docker run --gpus all nvidia/cuda:12.8.1-base-ubuntu22.04 nvidia-smi
```

정상이면 컨테이너 내에서 RTX 5090 Laptop이 보여야 합니다.

### 3.4 Draw Things gRPC 서버 실행

#### Docker 이미지 가져오기

```bash
docker pull drawthingsai/draw-things-grpc-server-cli:latest
```

#### 서버 실행

```bash
docker run -d \
  --name draw-things-server \
  --gpus all \
  -p 50051:50051 \
  -v ~/draw-things-models:/models \
  --restart unless-stopped \
  drawthingsai/draw-things-grpc-server-cli:latest
```

#### 서버 상태 확인

```bash
# 로그 확인
docker logs -f draw-things-server

# GPU 사용률 확인 (별도 터미널)
nvidia-smi
```

### 3.5 소스에서 직접 빌드 (고급)

Docker 없이 네이티브로 실행하려면:

```bash
# 저장소 클론
git clone https://github.com/drawthingsai/draw-things-community.git
cd draw-things-community

# Bazel 설치
sudo apt install bazel-bootstrap -y

# 의존성 설치
./Scripts/install.sh

# 빌드
bazel build //Sources/DrawThingsCUDA:grpcServerCLI

# 실행
./bazel-bin/Sources/DrawThingsCUDA/grpcServerCLI
```

### 3.6 RTX 5090 Laptop 최적화 설정

#### 전력/성능 모드

```bash
# 팬/전력 제한 확인
nvidia-smi -q -d POWER

# 성능 모드 설정
sudo nvidia-smi -pm 1
sudo nvidia-smi -pl 150    # RTX 5090 Laptop TGP에 맞게 조정 (최대값 확인 필요)
```

#### 메모리 최적화 (24GB 활용)

RTX 5090 Laptop의 24GB VRAM 활용 팁:

| 작업 | 권장 해상도 | 예상 VRAM |
|------|-------------|-----------|
| SD 1.5 | 768×768 | ~4GB |
| SDXL | 1024×1024 | ~8GB |
| SDXL + ControlNet | 1024×1024 | ~12GB |
| SDXL + LoRA + ControlNet | 1024×1024 | ~14GB |
| SD3 / Flux | 1024×1024 | ~18GB |

#### 컨테이너 메모리 제한 해제

```bash
# /etc/docker/daemon.json 수정
sudo tee /etc/docker/daemon.json <<EOF
{
  "default-runtime": "nvidia",
  "runtimes": {
    "nvidia": {
      "path": "nvidia-container-runtime",
      "runtimeArgs": []
    }
  }
}
EOF
sudo systemctl restart docker
```

### 3.7 방화벽 설정 (외부 접속 시)

M5에서 접속하려면 포트 열기가 필요합니다:

```bash
# UFW 사용 시
sudo ufw allow 50051/tcp
sudo ufw reload

# 또는 iptables
sudo iptables -A INPUT -p tcp --dport 50051 -j ACCEPT
```

### 3.8 Linux 서버 자동 시작 설정

```bash
# Docker 컨테이너 자동 재시작 (이미 --restart unless-stopped 설정됨)

# systemd 서비스로도 등록 가능
sudo tee /etc/systemd/system/draw-things.service <<EOF
[Unit]
Description=Draw Things gRPC Server
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/bin/docker start draw-things-server
ExecStop=/usr/bin/docker stop draw-things-server
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable draw-things.service
```

---

## 4. Linux 서버를 M5에서 클라이언트로 연결

### 4.1 방법 1: Draw Things 앱에서 원격 GPU 사용 (권장)

1. M5의 Draw Things 앱 열기
2. **Settings** (⌘ + ,) → **Compute Unit** → **"Custom Server"** 선택
3. 서버 주소 입력:
   ```
   <Linux_노트북_IP>:50051
   ```
   예: `192.168.1.100:50051`
4. **"Test Connection"** 클릭
5. 연결 성공 시 M5의 이미지 생성이 RTX 5090 Laptop에서 처리됩니다

> 💡 **Linux IP 확인:** `ip addr show` 또는 `hostname -I`

### 4.2 방법 2: SSH 터널 (보안 강화)

```bash
# M5 터미널에서 SSH 터널 생성
ssh -L 50051:localhost:50051 user@<Linux_IP>

# 그 후 Draw Things에서 127.0.0.1:50051로 접속
```

### 4.3 방법 3: CLI로 직접 생성 (Linux 로컬)

Linux 서버에서 CLI로 이미지 생성:

```bash
# 컨테이너 내 CLI 실행 예시
docker exec -it draw-things-server \
  draw-things-cli \
  --prompt "a beautiful mountain landscape at sunset, photorealistic" \
  --negative-prompt "blurry, low quality" \
  --model sd_xl_base_1.0 \
  --width 1024 --height 1024 \
  --steps 30 --cfg-scale 7 \
  --output /models/output.png

# 호스트로 복사
docker cp draw-things-server:/models/output.png ~/output.png
```

---

## 5. 사용 예시

### 5.1 예시 1: 기본 텍스트→이미지 (txt2img)

**프롬프트:**
```
a majestic snow leopard walking through a snowy forest,
golden hour lighting, photorealistic, 8k, detailed fur texture
```

**네거티브 프롬프트:**
```
blurry, low quality, deformed, extra limbs, watermark, text
```

**설정:**
| 항목 | 값 |
|------|-----|
| Model | SDXL 1.0 |
| Sampler | DPM++ 2M Karras |
| Steps | 30 |
| CFG Scale | 7 |
| Size | 1024 × 768 |
| Seed | 1234567890 |

---

### 5.2 예시 2: 애니메이션 스타일

**프롬프트:**
```
1girl, silver hair, blue eyes, school uniform, cherry blossoms,
anime style, detailed face, soft lighting, masterpiece, best quality
```

**네거티브 프롬프트:**
```
lowres, bad anatomy, bad hands, text, error, missing fingers,
extra digit, fewer digits, cropped, worst quality, low quality
```

**설정:**
| 항목 | 값 |
|------|-----|
| Model | Anything V5 (애니메이션 특화) |
| Sampler | Euler a |
| Steps | 25 |
| CFG Scale | 7 |
| Size | 512 × 768 |
| LoRA | 애니메이션 스타일 LoRA (선택) |

---

### 5.3 예시 3: 이미지→이미지 (img2img)

1. 소스 이미지를 Draw Things에 드래그
2. **"img2img"** 모드 선택
3. Denoising Strength 설정:

| 값 | 효과 |
|----|------|
| 0.2~0.4 | 원본 유지, 미세 조정 |
| 0.4~0.6 | 중간 변환 |
| 0.6~0.8 | 큰 변환, 구조 유지 |

4. 프롬프트 입력:

```
oil painting style, thick brushstrokes, vibrant colors, impressionist
```

5. **"Generate"** 클릭

---

### 5.4 예시 4: Inpainting (부분 수정)

1. 생성된 이미지를 열기
2. **"Inpaint"** 모드 선택
3. 수정할 영역을 **브러시로 마스킹** (회색 표시)
4. 프롬프트 입력:

```
a red sports car
```

5. **"Generate"** 클릭 → 마스킹된 부분만 새 이미지로 변경

---

### 5.5 예시 5: ControlNet 사용

1. 기존 이미지 업로드
2. **ControlNet** 패널 열기
3. **Preprocessor** 선택: `Canny` (엣지 감지)
4. **Model** 선택: `control_v11p_sd15_canny`
5. 프롬프트 입력:

```
modern skyscraper at night, neon lights, cyberpunk style, rain
```

6. **"Generate"** 클릭 → 원본 이미지의 구조를 유지한 새 이미지 생성

---

### 5.6 예시 6: Linux RTX 5090에서 대량 생성

```bash
#!/bin/bash
# batch_generate.sh - 프롬프트 목록으로 대량 생성

PROMPTS=(
  "autumn forest path, golden leaves, misty morning"
  "underwater coral reef, tropical fish, sunlight rays"
  "space station orbiting earth, sci-fi, detailed"
  "cozy coffee shop interior, rain outside, warm lighting"
)

for i in "${!PROMPTS[@]}"; do
  echo "[$((i+1))/${#PROMPTS[@]}] Generating: ${PROMPTS[$i]}"
  docker exec draw-things-server \
    draw-things-cli \
    --prompt "${PROMPTS[$i]}" \
    --negative-prompt "blurry, low quality" \
    --model sd_xl_base_1.0 \
    --width 1024 --height 1024 \
    --steps 30 --cfg-scale 7 \
    --output "/models/batch_output_$((i+1)).png"
done

# 결과 호스트로 복사
for i in "${!PROMPTS[@]}"; do
  docker cp draw-things-server:/models/batch_output_$((i+1)).png ~/batch_output_$((i+1)).png
done

echo "All done!"
```

실행:
```bash
chmod +x batch_generate.sh
./batch_generate.sh
```

---

## 6. 문제 해결

### 6.1 M5 관련

| 문제 | 해결 |
|------|------|
| 모델 다운로드 실패 | Settings → Models Folder → SSD 여유 공간 확인 |
| 생성 매우 느림 | Settings → Compute Unit → **"All"**로 변경 |
| 메모리 부족 경고 | 더 작은 해상도 사용 또는 SD 1.5 모델로 변경 |
| 앱 충돌 | macOS 업데이트 확인, 앱 재설치 |

### 6.2 Linux 관련

| 문제 | 해결 |
|------|------|
| `nvidia-smi` 명령 없음 | 드라이버 재설치: `sudo apt install nvidia-driver-595` |
| Docker GPU 인식 실패 | `sudo nvidia-ctk runtime configure --runtime=docker` 후 재시작 |
| 컨테이너 실행 불가 | `docker logs draw-things-server`로 오류 확인 |
| RTX 5090 미식별 | 드라이버 570 이상 업데이트 필수 |
| CUDA 버전 불일치 | 최신 Docker 이미지 재다운로드: `docker pull ...:latest` |
| OOM (Out of Memory) | 해상도 낮추거나 배치 크기 1로 설정 |

### 6.3 네트워크 연결

| 문제 | 해결 |
|------|------|
| 원격 연결 실패 | 양쪽 방화벽에서 포트 50051 허용 확인 |
| IP 확인 | Linux: `hostname -I`, M5: `ipconfig getifaddr en0` |
| 동일 네트워크 확인 | 두 기기 모두 같은 Wi-Fi/LAN에 연결 |

### 6.4 유용한 명령어 모음

```bash
# === Linux 서버 관리 ===
docker ps                              # 실행 중인 컨테이너 확인
docker logs -f draw-things-server      # 실시간 로그
docker restart draw-things-server      # 서버 재시작
docker stop draw-things-server         # 서버 중지
docker exec -it draw-things-server bash  # 컨테이너 셸 진입

# === GPU 모니터링 ===
nvidia-smi                             # GPU 상태
watch -n 1 nvidia-smi                 # 1초 간격 모니터링

# === 디스크 정리 ===
docker system prune -a                 # 미사용 이미지 정리
du -sh ~/draw-things-models            # 모델 폴더 크기 확인
```

---

## 부록: 빠른 시작 체크리스트

### M5 (macOS)
- [ ] Mac App Store에서 Draw Things 설치
- [ ] 앱 실행 및 권한 허용
- [ ] SDXL 또는 SD 1.5 모델 다운로드
- [ ] Compute Unit을 "All"로 설정
- [ ] 테스트 이미지 생성

### Ubuntu + RTX 5090 Laptop
- [ ] NVIDIA 드라이버 570+ 설치 (`nvidia-smi` 확인)
- [ ] Docker + NVIDIA Container Toolkit 설치
- [ ] `docker run --gpus all`으로 GPU 접근 확인
- [ ] Draw Things gRPC 서버 Docker 이미지 pull 및 실행
- [ ] 포트 50051 방화벽 허용
- [ ] M5에서 원격 연결 테스트

---

*이 가이드는 Draw Things Community Edition 기준입니다. 최신 정보는 [GitHub](https://github.com/drawthingsai/draw-things-community) 및 [drawthings.ai](https://drawthings.ai)를 참고하세요.*
