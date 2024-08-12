# Install ASP.NET 8 on Ubuntu 22.04
Install packages
```bash
sudo apt update
sudo apt install ca-certificates libc6 libgcc-s1 libgssapi-krb5-2 libicu70 liblttng-ust1 libssl3 libstdc++6 libunwind8 zlib1g
```
Install SDK & .NET runtime
```bash
sudo apt-get install -y dotnet-sdk-8.0
sudo apt-get install -y aspnetcore-runtime-8.0
sudo apt-get install -y dotnet-runtime-8.0
```
Check again
```bash
dotnet --info
```
Output
```
.NET SDK:
 Version:           8.0.107
 Commit:            1bdaef7265
 Workload version:  8.0.100-manifests.43c23f91

Runtime Environment:
 OS Name:     ubuntu
 OS Version:  22.04
 OS Platform: Linux
 RID:         ubuntu.22.04-x64
 Base Path:   /usr/lib/dotnet/sdk/8.0.107/

.NET workloads installed:
 Workload version: 8.0.100-manifests.43c23f91
There are no installed workloads to display.

Host:
  Version:      8.0.7
  Architecture: x64
  Commit:       2aade6beb0

.NET SDKs installed:
  8.0.107 [/usr/lib/dotnet/sdk]

.NET runtimes installed:
  Microsoft.AspNetCore.App 8.0.7 [/usr/lib/dotnet/shared/Microsoft.AspNetCore.App]
  Microsoft.NETCore.App 8.0.7 [/usr/lib/dotnet/shared/Microsoft.NETCore.App]

Other architectures found:
  None

Environment variables:
  Not set

global.json file:
  Not found

Learn more:
  https://aka.ms/dotnet/info

Download .NET:
  https://aka.ms/dotnet/download
```
# Create new ASP.NET 8 project
```bash
dotnet new blazor -o pet-aspnet8-web
cd pet-aspnet8-web
dotnet watch
```
Build project
```bash
dotnet build
```
Publish project
```bash
dotnet publish -c Release -o /var/www/pet-aspnet8-web/ --runtime linux-x64
```
# Create the service file for .NET application
```bash
sudo nano /etc/systemd/system/pet-aspnet8-web.service
```
The following example is an .ini service file for the app:
```
[Unit]
Description=Example .NET Web API App running on Linux

[Service]
WorkingDirectory=/var/www/pet-aspnet8-web
ExecStart=/usr/bin/dotnet /var/www/pet-aspnet8-web/pet-aspnet8-web.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=pet-aspnet8-web
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_NOLOGO=true

[Install]
WantedBy=multi-user.target
```
Save the file and enable the service.
```bash
sudo systemctl enable pet-aspnet8-web.service
```
Start the service and verify that it's running.
```bash
sudo systemctl start pet-aspnet8-web.service
sudo systemctl status pet-aspnet8-web.service

◝ pet-aspnet8-web.service - Example .NET Web API App running on Linux
    Loaded: loaded (/etc/systemd/system/pet-aspnet8-web.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/pet-aspnet8-web.service
            └─9021 /usr/local/bin/dotnet /var/www/pet-aspnet8-web/pet-aspnet8-web.dll
```