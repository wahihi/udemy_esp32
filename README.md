# udemy_esp32
Udemy IoT Application Development with the ESP32 Using the ESP-IDF Course Repository

https://www.udemy.com/course/iot-application-development-with-the-esp32-using-the-esp-idf/

# ESP32 IDF 프로젝트 빌드 가이드

이 가이드는 ESP32 IDF 프로젝트를 빌드하고 실행하는 방법을 설명합니다.

## 필수 구성 요소

- ESP-IDF가 시스템에 설치되어 있어야 합니다
- Git이 설치되어 있어야 합니다
- 적절한 USB 드라이버가 설치되어 있어야 합니다
- AWS IoT를 사용하는 경우 관련 인증서가 준비되어 있어야 합니다

## 프로젝트 구조

프로젝트는 다음과 같은 구조를 가져야 합니다:

```
your_project_directory/
├── CMakeLists.txt
├── main/
│   ├── CMakeLists.txt
│   ├── main.c
│   ├── rgb_led.c
│   ├── wifi_app.c
│   ├── http_server.c
│   ├── DHT22.c
│   ├── app_nvs.c
│   ├── wifi_reset_button.c
│   ├── sntp_time_sync.c
│   └── aws_iot.c
├── webpage/
│   ├── app.css
│   ├── app.js
│   ├── favicon.ico
│   ├── index.html
│   └── jquery-3.3.1.min.js
└── certs/
    ├── aws_root_ca_pem
    ├── certificate_pem_crt
    └── private_pem_key
```

## 빌드 단계

### 1. ESP-IDF 환경 설정

ESP-IDF 환경변수를 설정합니다:

```bash
cd $IDF_PATH
. ./export.sh
```

### 2. AWS IoT 컴포넌트 설치

프로젝트에서 AWS IoT를 사용하는 경우 다음 명령을 실행합니다:

```bash
cd your_project_directory
git clone https://github.com/espressif/esp-aws-iot.git
```

### 3. 인증서 설정

`certs` 디렉토리에 다음 파일들이 있는지 확인합니다:
- aws_root_ca_pem
- certificate_pem_crt
- private_pem_key

### 4. 프로젝트 구성

프로젝트 설정을 구성합니다:

```bash
cd your_project_directory
idf.py menuconfig
```

menuconfig에서 다음 항목들을 설정합니다:
- WiFi SSID 및 비밀번호
- AWS IoT 엔드포인트
- 기타 필요한 컴포넌트 설정

### 5. 빌드 및 플래시

프로젝트를 빌드하고 ESP32에 플래시합니다:

```bash
# 프로젝트 빌드
idf.py build

# ESP32에 플래시 (포트는 환경에 따라 다를 수 있음)
idf.py -p /dev/ttyUSB0 flash

# (선택사항) 시리얼 모니터링
idf.py -p /dev/ttyUSB0 monitor
```

## 문제 해결

### CMake 관련 설정 확인

메인 `CMakeLists.txt`에 다음 내용이 포함되어 있는지 확인:

```cmake
cmake_minimum_required(VERSION 3.5)
include($ENV{IDF_PATH}/tools/cmake/project.cmake)
set(EXTRA_COMPONENT_DIRS "esp-aws-iot")
project(your_project_name)
```

main 디렉토리의 `CMakeLists.txt`에는 다음 내용이 포함되어 있어야 합니다:

```cmake
idf_component_register(SRCS main.c rgb_led.c wifi_app.c http_server.c DHT22.c app_nvs.c wifi_reset_button.c sntp_time_sync.c aws_iot.c
                      INCLUDE_DIRS "."
                      EMBED_FILES webpage/app.css webpage/app.js webpage/favicon.ico webpage/index.html webpage/jquery-3.3.1.min.js)
target_add_binary_data(${COMPONENT_TARGET} "certs/aws_root_ca_pem" TEXT)
target_add_binary_data(${COMPONENT_TARGET} "certs/certificate_pem_crt" TEXT)
target_add_binary_data(${COMPONENT_TARGET} "certs/private_pem_key" TEXT)
```

### 일반적인 문제 해결

1. 빌드 실패 시:
   - 모든 소스 파일이 main 디렉토리에 있는지 확인
   - ESP-IDF 경로가 올바르게 설정되었는지 확인
   - 필요한 모든 컴포넌트가 설치되었는지 확인

2. 플래시 실패 시:
   - USB 연결 상태 확인
   - 올바른 포트를 사용하고 있는지 확인
   - USB 드라이버가 올바르게 설치되었는지 확인

3. AWS IoT 연결 문제:
   - 인증서 파일이 올바른 위치에 있는지 확인
   - AWS IoT 엔드포인트 설정이 올바른지 확인
   - WiFi 설정이 올바른지 확인

## 참고 사항

- 프로젝트 특성에 따라 추가적인 설정이 필요할 수 있습니다
- ESP-IDF 버전에 따라 일부 설정이나 명령어가 다를 수 있습니다
- 문제가 발생하면 ESP-IDF의 공식 문서를 참조하거나 GitHub 이슈를 확인하세요
