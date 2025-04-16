# remote

```yaml
name: Windows RDP

on: [workflow_dispatch]

jobs:
  build:
    runs-on: windows-latest

    steps:
    - name: Enable RDP
      run: |
        net user admin ltn123456
        net localgroup administrators admin /add
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
        Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

    - name: Download and Set up Ngrok
      run: |
        Invoke-WebRequest https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-stable-windows-amd64.zip -OutFile ngrok.zip
        Expand-Archive ngrok.zip -DestinationPath .
        .\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN

    - name: Start Ngrok Tunnel
      run: .\ngrok.exe tcp 3389

    - name: Keep Alive
      run: timeout /t 999999

```
