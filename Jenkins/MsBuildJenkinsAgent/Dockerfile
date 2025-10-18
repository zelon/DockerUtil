# escape=`

# Use the latest Windows Server Core 2022 image.
FROM jenkins/agent:jdk21-windowsservercore-ltsc2022

# Restore the default Windows shell for correct batch processing.
SHELL ["cmd", "/S", "/C"]

# https://learn.microsoft.com/en-us/visualstudio/install/build-tools-container?view=vs-2022
RUN `
    # Download the Build Tools bootstrapper.
    curl -SL --output vs_buildtools.exe https://aka.ms/vs/17/release/vs_buildtools.exe `
    `
    # Install Build Tools with the Microsoft.VisualStudio.Workload.AzureBuildTools workload, excluding workloads and components with known issues.
    && (start /w vs_buildtools.exe --quiet --wait --norestart --nocache `
        --installPath "%ProgramFiles(x86)%\Microsoft Visual Studio\2022\BuildTools" `
        --add Microsoft.NetCore.Component.SDK `
        --add Microsoft.VisualStudio.Workload.AzureBuildTools `
        --add Microsoft.VisualStudio.Component.Roslyn.Compiler `
        --add Microsoft.VisualStudio.Component.NuGet.BuildTools `
        --remove Microsoft.VisualStudio.Component.Windows10SDK.10240 `
        --remove Microsoft.VisualStudio.Component.Windows10SDK.10586 `
        --remove Microsoft.VisualStudio.Component.Windows10SDK.14393 `
        --remove Microsoft.VisualStudio.Component.Windows81SDK `
        || IF "%ERRORLEVEL%"=="3010" EXIT 0) `
    `
    # Cleanup
    && del /q vs_buildtools.exe

# Define the entry point for the docker container.
# This entry point starts the developer command prompt and launches the PowerShell shell.
#ENTRYPOINT ["C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\Common7\\Tools\\VsDevCmd.bat", "&&", "powershell.exe", "-NoLogo", "-ExecutionPolicy", "Bypass"]

# 작업 디렉토리 설정
WORKDIR C:/choco

# Chocolatey 설치 스크립트 실행
RUN `
    powershell -NoProfile -ExecutionPolicy Bypass -Command "[System.Net.ServicePointManager]::SecurityProtocol = 'Tls12'; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"

# build command
# docker build -t zelonion/msbuild-windows-servercore-2022:latest -m 2GB .

# run command with removing after run(--rm)
# docker run --name jenkins-windows-agent-1 --restart unless-stopped -i zelonion/msbuild-windows-servercore-2022:latest java -jar C:/ProgramData/Jenkins/agent.jar -url http://host.docker.internal:8080/ -secret abcd1234 -name "jenkins-win-agent1" -webSocket -workDir "c:\jenkins"