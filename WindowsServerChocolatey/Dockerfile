# 베이스 이미지 선택
FROM mcr.microsoft.com/windows/servercore:ltsc2025

# 작업 디렉토리 설정
WORKDIR C:/choco

# Chocolatey 설치 스크립트 실행
RUN powershell -NoProfile -ExecutionPolicy Bypass -Command \
    "[System.Net.ServicePointManager]::SecurityProtocol = 'Tls12'; \
    iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"

# 환경 변수 설정 (choco가 PATH에 자동 추가되지만 명시적으로 설정해도 안전)
ENV PATH="C:/ProgramData/chocolatey/bin;%PATH%"

# 기본 명령어
CMD ["choco", "--version"]


# docker 빌드 명령어 예시:
# docker build -t zelonion/choco-windows-servercore-2025 .