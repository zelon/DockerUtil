# 베이스 이미지 선택
FROM mcr.microsoft.com/windows/servercore:ltsc2022

# 작업 디렉토리 설정
WORKDIR C:/choco

# Chocolatey 설치 스크립트 실행
RUN powershell -NoProfile -ExecutionPolicy Bypass -Command \
    "[System.Net.ServicePointManager]::SecurityProtocol = 'Tls12'; \
    iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"

RUN choco install -y mc

# 환경 변수 설정 - PowerShell을 사용하여 기존 PATH에 추가
RUN powershell -Command "[Environment]::SetEnvironmentVariable('PATH', \"C:\Program files\Midnight Commander;$env:PATH\", [EnvironmentVariableTarget]::Machine)"

# 기본 명령어
CMD ["choco", "--version"]


# docker 빌드 명령어 예시:
# docker build -t chocolatey-windows .

# docker 실행 명령어 예시:
# docker run -it chocolatey-windows powershell