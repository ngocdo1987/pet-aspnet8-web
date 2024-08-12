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