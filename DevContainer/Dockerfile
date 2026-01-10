# escape=`

# https://mcr.microsoft.com/en-us/artifact/mar/dotnet/sdk/tags
FROM mcr.microsoft.com/dotnet/sdk:10.0.101-windowsservercore-ltsc2022

# Restore the default Windows shell for correct batch processing.
SHELL ["cmd", "/S", "/C"]

# https://learn.microsoft.com/ko-kr/visualstudio/install/build-tools-container?view=visualstudio

# Download the Build Tools bootstrapper.
RUN curl -SL --output vs_buildtools.exe https://aka.ms/vs/stable/vs_BuildTools.exe

# workloads 목록: https://learn.microsoft.com/ko-kr/visualstudio/install/workload-component-id-vs-build-tools?view=visualstudio
# Visual Studio Installer 에서 '자세히 -> 구성 내보내기' 선택하면 설치된 workloads 목록을 얻을 수 있다
# Install Build Tools with the Microsoft.VisualStudio.Workload.AzureBuildTools workload, excluding workloads and components with known issues.
RUN (start /w vs_buildtools.exe --quiet --wait --norestart --nocache `
        --installPath "%ProgramFiles%\Microsoft Visual Studio\BuildTools" `
        --add Microsoft.VisualStudio.Workload.AzureBuildTools `
        --remove Microsoft.VisualStudio.Component.Windows10SDK.10240 `
        --remove Microsoft.VisualStudio.Component.Windows10SDK.10586 `
        --remove Microsoft.VisualStudio.Component.Windows10SDK.14393 `
        --remove Microsoft.VisualStudio.Component.Windows81SDK `
        || IF "%ERRORLEVEL%"=="3010" EXIT 0) `
    # Cleanup
    && del /q vs_buildtools.exe

#RUN choco install -y mc

RUN mkdir C:\Temp

# Chocolatey 설치 스크립트 실행
RUN powershell -NoProfile -ExecutionPolicy Bypass -Command `
    "[System.Net.ServicePointManager]::SecurityProtocol = 'Tls12'; `
    iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"

RUN powershell -NoProfile -ExecutionPolicy Bypass -Command "choco install -y --no-progress mc vim git python314 nodejs rust neovim"

RUN npm install -g @anthropic-ai/claude-code `
    npm install -g @google/gemini-cli

COPY _vimrc /Users/ContainerAdministrator
COPY .gitconfig /Users/ContainerAdministrator
COPY Powershell-Profile /Users/ContainerAdministrator/Documents/WindowsPowerShell/Microsoft.PowerShell_profile.ps1

ENV CLAUDE_CONFIG_DIR=C:/Users/ContainerAdministrator/.claude

WORKDIR C:/dev-container-workdir

# Define the entry point for the docker container.
# This entry point starts the developer command prompt and launches the PowerShell shell.
ENTRYPOINT ["C:\\Program Files\\Microsoft Visual Studio\\BuildTools\\Common7\\Tools\\VsDevCmd.bat", "&&", "powershell.exe", "-NoLogo", "-ExecutionPolicy", "Bypass"]

# docker build example:
# docker build -t dev-container -m 2GB .

# docker run example:
#  - claude 인증 정보를 저장해놓고 계속 다시 인증하지 않게 하기 위해서 .claude 디렉토리는 volume 에 저장한다
# docker run --rm -it -v D:/git/dev-container:C:/dev-container-workdir -v dev-container-volume:C:/Users/ContainerAdministrator/.claude -e GEMINI_API_KEY=$env:GEMINI_API_KEY dev-container
