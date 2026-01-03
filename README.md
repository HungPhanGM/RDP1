name: RDP greenboyMC - Intro The Flash (Giảm Lag FULL MAX Fix Lỗi)

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: windows-latest
    timeout-minutes: 360

    steps:
      - name: Download & Extract Ngrok
        run: |
          Invoke-WebRequest -Uri "https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-windows-amd64.zip" -OutFile ngrok.zip
          Expand-Archive ngrok.zip -DestinationPath .

      - name: Auth Ngrok
        env:
          NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}
        run: .\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN

      - name: Giảm Lag FULL MAX + Fast Intro (An Toàn 2026)
        run: |
          # Enable RDP
          Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0
          Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

          # User greenboyMC
          New-LocalUser "greenboyMC" -Password (ConvertTo-SecureString -AsPlainText "W1nd0ws-P4ssw0rd-2025!" -Force) -FullName "greenboyMC"
          Add-LocalGroupMember -Group "Administrators" -Member "greenboyMC"
          Add-LocalGroupMember -Group "Remote Desktop Users" -Member "greenboyMC"

          # Intro flash nhanh + tắt hết visual nặng
          reg add "HKCU\Control Panel\Desktop" /v UserPreferencesMask /t REG_BINARY /d 9012038010000000 /f
          reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects" /v VisualFXSetting /t REG_DWORD /d 3 /f
          reg add "HKCU\Control Panel\Desktop\WindowMetrics" /v MinAnimate /t REG_SZ /d 0 /f
          reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v TaskbarAnimations /t REG_DWORD /d 0 /f
          reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize" /v EnableTransparency /t REG_DWORD /d 0 /f
          reg add "HKCU\Software\Microsoft\Windows\DWM" /v EnableAeroPeek /t REG_DWORD /d 0 /f

          # Tắt service nặng full
          @("SysMain","WSearch","DiagTrack","wuauserv","bits","WindowsSearch") | ForEach-Object {
            Stop-Service -Name $_ -Force -ErrorAction SilentlyContinue
            Set-Service -Name $_ -StartupType Disabled -ErrorAction SilentlyContinue
          }

          # Power plan High Performance
          powercfg -setactive 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c

          # RDP giảm lag FULL MAX an toàn (TurboRemoteFX + BetterRDP 2026)
          New-Item -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Force | Out-Null
          Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "MaxCompressionLevel" -Value 0 -Type DWord  # No compression
          Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "AVCHardwareEncodePreferred" -Value 1 -Type DWord
          Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "AVC444ModePreferred" -Value 1 -Type DWord
          Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fEnableRemoteFX" -Value 1 -Type DWord
          Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -Name "DWMFRAMEINTERVAL" -Value 10 -Type DWord
          Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "MaxFrameRate" -Value 60 -Type DWord  # 60 FPS unlock

      - name: Start Ngrok Asia & Hiện IP Siêu Nhanh
        run: |
          Write-Host "=================================================="
          Write-Host "RDP greenboyMC - Giảm Lag FULL MAX Fix Lỗi + IP Nhanh"
          Write-Host "Username: greenboyMC"
          Write-Host "Password: W1nd0ws-P4ssw0rd-2025!"
          Write-Host "No Compression | 60 FPS | RemoteFX Full | Region AP"
          Write-Host "=================================================="

          Start-Process -WindowStyle Hidden -FilePath ".\ngrok.exe" -ArgumentList "tcp 3389 --region ap"

          $attempts = 0
          $maxAttempts = 5
          $tunnelUrl = $null
          while ($attempts -lt $maxAttempts -and -not $tunnelUrl) {
            Start-Sleep -Seconds 6
            $attempts++
            try {
              $tunnels = Invoke-RestMethod http://localhost:4040/api/tunnels
              $tcpTunnel = $tunnels.tunnels | Where-Object { $_.proto -eq "tcp" }
              if ($tcpTunnel) { $tunnelUrl = $tcpTunnel.public_url }
            } catch {}
          }

          if ($tunnelUrl) {
            $hostPort = $tunnelUrl -replace "tcp://", ""
            Write-Host "🚀 IP HIỆN RỒI - CONNECT NGAY BRO! 🚀"
            Write-Host "Address: $hostPort"
            Write-Host "Host: $($hostPort.Split(':')[0])"
            Write-Host "Port: $($hostPort.Split(':')[1])"
            Write-Host "=================================================="
          } else {
            Write-Host "Chưa thấy IP, run lại workflow nhé!"
          }

          Start-Sleep -Se
