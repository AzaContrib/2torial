<img class="header-logo" src="/img/sega/wacca/reverse/logo.webp">
# Troubleshooting

--8<-- "docs/snippets/common/data_warning.md"

### My game shows `System Error: DLL was not found`

!!! tip ""

    If the missing DLL is `XINPUT1_3.dll`, `X3DAudio1_7.dll`, or `XAPOFX1_5.dll`, install the
    [DirectX End-User Runtime](https://www.microsoft.com/en-us/download/details.aspx?id=35), then reboot and try again.

<img src="/img/sega/wacca/reverse/troubleshooting/directx-missing.webp">

### My game shows `DLL failed to load inside target process`

!!! tip ""

    Run `App\bin\launch.bat` as administrator.

    If you want the batch file to request admin automatically, add this to the top of `launch.bat`, before the existing commands:

    ```batch
    set "params=%*"
    cd /d "%~dp0" && ( if exist "%temp%\getadmin.vbs" del "%temp%\getadmin.vbs" ) && fsutil dirty query %systemdrive% 1>nul 2>nul || ( echo Set UAC = CreateObject^("Shell.Application"^) : UAC.ShellExecute "cmd.exe", "/k cd ""%~sdp0"" && ""%~s0"" %params%", "", "runas", 1 >> "%temp%\getadmin.vbs" && "%temp%\getadmin.vbs" && exit /B )
    ```

<img src="/img/sega/wacca/reverse/troubleshooting/dll-load-failed.webp">

### My game crashes with `(Low Level) Fatal Error`

!!! tip ""

    This usually means files from multiple WACCA versions were mixed together while applying updates.

    Rebuild the data from a clean base and apply the updates in order. For WACCA Reverse, update to `3.07.01` in sequence:

    `3.00` -> `3.01` -> `3.02` -> `3.03` -> `3.04` -> `3.05` -> `3.06` -> `3.07.00` -> `3.07.01`

<img src="/img/sega/wacca/reverse/troubleshooting/low-level-fatal-1.webp">
<img src="/img/sega/wacca/reverse/troubleshooting/low-level-fatal-2.webp">

### My game shows `ERROR CODE 4103 - Unexpected Error Occurred`

!!! tip ""

    Check the command prompt behind the game first. `4103` is a generic error screen, so the useful message is usually in the console log.

??? info "Capturing logs from `amdaemon` for troubleshooting"

    To assist with troubleshooting, a script can be used to capture logs from `amdaemon`. Create a file named
    `amdaemontest.bat` in `App\bin`, then paste the code block below into the file.

    ```batch
    @echo off
    cls
    echo Attempting to run AM Daemon ...
    echo Window should close after 30 seconds
    echo Log will be generated at amdaemontest.txt
    call :sub >amdaemontest.txt
    exit /b

    :sub
    pushd %~dp0
    start /b "AM Daemon" /min inject -d -k mercuryhook.dll amdaemon.exe -f -c config.json config_lan_install_client.json config_lan_install_server.json config_video_clone.json config_video_dual.json config_video_clone_flip.json config_video_dual_flip.json config_region_exp.json config_region_chn.json config_region_jpn.json
    ping 127.0.0.1 -n 31 > nul && taskkill /F /im amdaemon.exe
    ```

    Double-click it to run. The script should run for 30 seconds, and you will get a file
    named `amdaemontest.txt` in `App\bin`, which you can send to help people troubleshoot your issue.

    If your `launch.bat` uses a different region config, match the `amdaemon.exe` command here to the one in your `launch.bat`.

### The console says `No such service is known`

!!! tip ""

    This means `amdaemon` did not start correctly.

    Check that `launch.bat` and the `[vfs]` paths in `segatools.ini` are correct. Any folder configured as `amfs`, `option`, or `appdata` must exist.

    Some Windows 11 setups have also been reported to fail before `amdaemon` fully starts. If the paths are correct and the issue persists, try testing the same data on Windows 10.

<img src="/img/sega/wacca/reverse/troubleshooting/no-such-service.webp">

??? info "Example Windows 11 `amdaemon` failure"

    <img src="/img/sega/wacca/reverse/troubleshooting/amdaemon-windows11.webp">

### The console says `Load Icf Failed`

!!! tip ""

    Check the `amfs` path in `segatools.ini` and make sure it contains `ICF1`.

    If `ICF1` is missing, copy it from your `3.07.01` data into the folder configured as `amfs`.

<img src="/img/sega/wacca/reverse/troubleshooting/icf-load-failed.webp">

### My game shows `Unknown Error Code 1201`

!!! tip ""

    Install the FTDI LED driver. WACCA expects `ftd2xx.dll` to be available for the LED device.

    Follow the FTDI driver steps in the setup guide, then start the game again.

<img src="/img/sega/wacca/reverse/troubleshooting/error-1201-ftdi.webp">

### My game shows `ERROR CODE 1906-1 ALLNet authentication failed`

!!! tip ""

    If you are using your own private server, check that the address returned by ALLNet PowerOn is reachable from the game PC.

    If you are not maintaining your own server, switch to a known working public server and recheck your network configuration.

<img src="/img/sega/wacca/reverse/troubleshooting/allnet-1906-1.webp">

### Network self-test is skipped or network test passes but the game stays grey network

!!! tip ""

    If network self-test is skipped, check whether an offline flag is enabled in your patch or server configuration.

    If the network test passes but the game still stays grey network, check `App\WindowsNoEditor\Mercury\Config\DefaultHardware.ini`.
    If `bEnableErrorWatcher` was changed to `false`, set it back to `true` and restart the game.

### My screen position is too high or too low

!!! tip ""

    Adjust `ViewportTopRate2D` and `ViewportTopRate3D` in `DefaultGameUserSettings.ini`.

    These values are ratios from `0` to `1` that control the top offset. `ViewportTopRate2D` affects UI elements, and
    `ViewportTopRate3D` affects the note field.

### Notes drift out of sync on AMD GPUs

!!! tip ""

    If notes are synced at the start of a song but drift later, enable **Frame Rate Target Control** and **Enhanced Sync** in AMD Radeon Settings.

    These settings can also help:

    ```ini
    [/Script/Mercury.MercuryGameUserSettings]
    bUseVSync=False
    FullscreenMode=0
    LastConfirmedFullscreenMode=0
    PreferredFullscreenMode=0
    FrameRateLimit=60.000000
    bSmoothFrameRate=False
    ```

<img src="/img/sega/wacca/reverse/troubleshooting/amd-frame-settings.webp">
