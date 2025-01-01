# SQL Server 2022 Windows container dockerfile
## Warning: Restarting windows container causes the machine key to change and hence if you have any encryption configured then restarting SQL On Windows containers
## breaks the encryption key chain in SQL Server. 

# Download the SQL Developer from the following location https://www.microsoft.com/en-us/sql-server/sql-server-downloads and extract the .box and .exe files using the option: "Download Media"

FROM mcr.microsoft.com/windows/servercore:ltsc2025

ENV sa_password="_" \
    attach_dbs="[]" \
    ACCEPT_EULA="_" \
    sa_password_path="C:\ProgramData\Docker\secrets\sa-password"

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# make install files accessible
COPY start.ps1 /
COPY SQLServer2022-DEV-x64-ENU.box /
COPY SQLServer2022-DEV-x64-ENU.exe /

WORKDIR /

RUN Start-Process -Wait -FilePath .\SQLServer2022-DEV-x64-ENU.exe -ArgumentList /qs, /x:setup ; \
        .\setup\setup.exe /q /ACTION=Install /INSTANCENAME=MSSQLSERVER /FEATURES=SQLEngine /UPDATEENABLED=0 /SQLSVCACCOUNT='NT AUTHORITY\NETWORK SERVICE' /SQLSYSADMINACCOUNTS='BUILTIN\ADMINISTRATORS' /TCPENABLED=1 /NPENABLED=0 /IACCEPTSQLSERVERLICENSETERMS ; \
        Remove-Item -Recurse -Force SQLServer2022-DEV-x64-ENU.exe, SQLServer2022-DEV-x64-ENU.box, setup

RUN stop-service MSSQLSERVER ; \
        set-itemproperty -path 'HKLM:\software\microsoft\microsoft sql server\mssql16.MSSQLSERVER\mssqlserver\supersocketnetlib\tcp\ipall' -name tcpdynamicports -value '' ; \
        set-itemproperty -path 'HKLM:\software\microsoft\microsoft sql server\mssql16.MSSQLSERVER\mssqlserver\supersocketnetlib\tcp\ipall' -name tcpport -value 1433 ; \
        set-itemproperty -path 'HKLM:\software\microsoft\microsoft sql server\mssql16.MSSQLSERVER\mssqlserver\' -name LoginMode -value 2 ;

HEALTHCHECK CMD [ "sqlcmd", "-Q", "select 1" ]

CMD .\start -sa_password $env:sa_password -ACCEPT_EULA $env:ACCEPT_EULA -attach_dbs \"$env:attach_dbs\" -Verbose

# To build the image run the following command
# docker build -t mssql2022-win-zelon .

# To run the container run the following command
# docker run -d -p 1433:1433 -v C:/temp/:C:/temp/ -e sa_password=Abcd1234! -e ACCEPT_EULA=Y mssql2022-win-zelon