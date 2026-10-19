# Tech Preview v0.9.0: Windows / ApplePie installation guide

This guide covers `StreetPilot_ApplePie_Patcher_TechPreview_release_v0.9.0.zip`.
It expands the instructions inside that ZIP; it does not change the patcher
executable or the already-published release asset.

> [!IMPORTANT]
> This is an ADB-dependent technical preview. Normal APK installation through
> the Android File Manager is not supported. Do not install the unpatched
> original APK. Patch it on the PC, install the generated APK with ADB, and
> transfer the Garmin data before launching StreetPilot.

## 1. Requirements and limitations

- A Windows PC with enough free space for the downloaded archives, their
  extracted contents, and a backup of any existing application data.
- The v0.9.0 patcher ZIP from this project's GitHub release.
- Recent [Android SDK Platform Tools for Windows](https://developer.android.com/tools/releases/platform-tools).
  Extract the whole package, not just `adb.exe`; keep its supporting DLLs.
- A target ApplePie device with Android debugging access and enough free
  internal storage for the application and navigation data.
- Support for 32-bit ARM applications on that device. An ARM64 processor alone
  does not prove that the installed Android system supports ARM32 applications.
- The exact supported source APK and navigation data that you are entitled to
  use.
- A trusted local Wi-Fi network for wireless ADB, or a supported USB debugging
  connection. Do not expose ADB through a public IP address or router port
  forwarding.

The package documents testing on **Exploter ApplePie AI-996B**. AI-996, Plus,
Plus 2.0, U, Rocket Plus, other ApplePie variants, and unrelated Android devices
are not confirmed compatible by this release. Root and VMOS Pro are not part of
this procedure.

Perform the entire installation while parked. Do not operate the PC or debug
the device while driving.

## 2. Download the patcher and original materials

### Patcher: GitHub

Open the [v0.9.0 Tech Preview release](https://github.com/NavRevival-Lab/streetpilot-237-patcher/releases/tag/v0.9.0-tech-preview)
and download this exact asset:

```text
StreetPilot_ApplePie_Patcher_TechPreview_release_v0.9.0.zip
```

Do not select GitHub's automatic **Source code** archives. Those contain the
repository snapshot at the tag, not the patcher executable.

The ZIP contains:

- `StreetPilot_ApplePie_Patcher.exe`
- `StreetPilot_ApplePie_Patcher_README.txt`
- `SHA256SUMS.txt`

The internal `SHA256SUMS.txt` lists the executable's hash. It is not a checksum
of the outer ZIP. The ZIP checksum is provided below and in the
[separate checksum file](../checksums/StreetPilot_ApplePie_Patcher_TechPreview_release_v0.9.0.zip.sha256).

### Original application and data: external MediaFire folder

The project maintainer supplied this folder link for the original materials:

[MediaFire: Garmin folder](https://www.mediafire.com/folder/nbl3er5zopqie/Garmin)

Look for the supported original APK named
`StreetPilot_2.37(2019.10.23).apk` and the archives holding the Garmin data.
Download only materials you are authorized to use. Download all required parts
if an archive is split into multiple volumes, then extract with a tool that
supports that archive format. The v0.9.0 patcher itself must still come from
GitHub, not a third-party copy.

> [!NOTE]
> The MediaFire folder's current file listing could not be independently
> verified when this guide was prepared. Contents and filenames may change,
> and the link may become unavailable. Treat the APK name above as the
> required input name, not a guarantee that it is currently present. Verify
> the actual APK hash below. If the required file or a complete archive is
> missing, stop and report that problem; do not substitute a different APK.

This link is not an official Garmin distribution channel. Availability at an
external URL is not proof of permission to use or redistribute the files. The
project's license does not license the original APK, maps, voices, activation
material, or other third-party content. Do not bypass activation or use another
device's identity/registration material.

## 3. Prepare the Windows working folders and verify the downloads

Use a simple path without spaces, for example `C:\StreetPilotTP`:

| Local location | What to put there |
| --- | --- |
| `C:\StreetPilotTP\platform-tools\` | The complete extracted Platform Tools package, including `adb.exe` |
| `C:\StreetPilotTP\patcher\` | All three files from the patcher ZIP |
| `C:\StreetPilotTP\patcher\StreetPilot_2.37(2019.10.23).apk` | A copy of the original APK, kept alongside the patcher |
| `C:\StreetPilotTP\data\Garmin\` | The actual extracted Garmin data, as explained in section 5 |

Keep your original downloads and an untouched backup separately. Work on
copies. Do not run the patcher from inside the ZIP preview in Explorer.

Open **PowerShell**, not Command Prompt. All commands below use PowerShell
syntax. Keep this PowerShell window open throughout the procedure; variables
do not automatically carry over to a new window. Paths are examples: change
them if you chose another location.

First verify the downloaded release ZIP. Enter its full path when prompted;
the command does not assume a personal Downloads-folder path:

```powershell
$zipPath = Read-Host 'Enter the full path to the downloaded patcher ZIP (without surrounding quotes)'
$expectedZip = 'd0f483c3d54be9399e646518bee0e0475f67d2b8ff28865f7be30d38bd623768'
$actualZip = (Get-FileHash -Algorithm SHA256 -LiteralPath $zipPath).Hash.ToLowerInvariant()
if ($actualZip -ne $expectedZip) {
    throw 'Release ZIP checksum mismatch. Stop; do not run this copy.'
}
'Release ZIP checksum: PASS'
```

After extracting the ZIP and preparing the example folders, verify the source
APK and patcher executable:

```powershell
$root = 'C:\StreetPilotTP'
$adb = Join-Path $root 'platform-tools\adb.exe'
$originalApk = Join-Path $root 'patcher\StreetPilot_2.37(2019.10.23).apk'
$patcherExe = Join-Path $root 'patcher\StreetPilot_ApplePie_Patcher.exe'

if (-not (Test-Path -LiteralPath $adb)) { throw 'adb.exe not found. Check your Platform Tools extraction path.' }

$expectedSource = 'b71ca8080ae11eceb37034d2aa0d15547b5e556958bff45f4c3533915d7e5105'
$actualSource = (Get-FileHash -Algorithm SHA256 -LiteralPath $originalApk).Hash.ToLowerInvariant()
if ($actualSource -ne $expectedSource) {
    throw 'Unsupported source APK. Do not rename another APK to force compatibility.'
}

$expectedExe = 'cdba73d0f818f687404d741ab7999cff5cd7900348b0c0bf854febc5d8f7cee2'
$actualExe = (Get-FileHash -Algorithm SHA256 -LiteralPath $patcherExe).Hash.ToLowerInvariant()
if ($actualExe -ne $expectedExe) { throw 'Patcher executable checksum mismatch.' }

'Source APK and patcher executable checksums: PASS'
& $adb version
```

If any command fails, stop and correct that problem before continuing. Do not
disable antivirus, SmartScreen, or other system security protections to force
an unverified executable to run. A matching checksum establishes that you have
the documented bytes, not that every device or execution environment is safe.

## 4. Create the compatibility APK on the PC

1. Double-click `StreetPilot_ApplePie_Patcher.exe` in the extracted patcher
   folder.
2. When prompted, select your verified
   `StreetPilot_2.37(2019.10.23).apk`.
3. Wait for the patcher to finish and report success.
4. Confirm that `StreetPilot_2.37_ApplePie_Compat.apk` has been created.

Because the input APK and patcher are in the same example folder, the expected
output location is:

```text
C:\StreetPilotTP\patcher\StreetPilot_2.37_ApplePie_Compat.apk
```

Do not transfer or install the original input APK by mistake. Do not close the
patcher during processing. If it reports an unsupported input, go back to the
source-hash check; do not edit or rename other binaries to bypass validation.

## 5. Extract and normalize the Garmin data layout

Extract the data archives downloaded from MediaFire on the PC. An archive may
wrap its contents in extra directories such as `Android`, `data`,
`com.garmin.android.apps.gmobilext`, or `files`. Locate the **actual Garmin
folder**, not just the outer archive folder.

Prepare one top-level folder at `C:\StreetPilotTP\data\Garmin`. Immediately
inside it, the data should have this general layout, depending on the materials
you are entitled to use:

| Example PC path | Contents |
| --- | --- |
| `C:\StreetPilotTP\data\Garmin\.System\` | Map data such as `gmapprom.img` and `gmapbmap.img`, if supplied |
| `C:\StreetPilotTP\data\Garmin\Text\` | Interface language resources, if supplied |
| `C:\StreetPilotTP\data\Garmin\Voice\` | Spoken-guidance resources, if supplied |
| Other folders directly inside `Garmin` | For example `Help`, `POI`, `Vehicle`, or `Themes`, if supplied |

Preserve filenames and subdirectories, including the leading dot in `.System`.
Do not rename it to `System`. Use Explorer's hidden-item display or inspect the
folder in PowerShell with `-Force`. The archive itself is not the data folder:
do not push an unextracted ZIP and expect StreetPilot to read it.

```powershell
$garminLocal = Join-Path $root 'data\Garmin'
Get-ChildItem -LiteralPath $garminLocal -Force
Get-ChildItem -LiteralPath (Join-Path $garminLocal '.System') -Force
```

The second command must find the intended map-data directory. If it is absent,
check the archive contents and extraction level before proceeding. Do not
create a second nested `Garmin\Garmin` directory, and do not mix unrelated
versions or another device's registration data into your working copy.

## 6. Connect ADB to the intended ApplePie device

### Option A: wireless debugging with a pairing code

1. Connect the Windows PC and ApplePie to the same trusted Wi-Fi network.
2. On the ApplePie, enable **Developer options**. On many Android builds this
   means opening Settings > About device and tapping Build number seven times;
   vendor menus may differ. Do not change unrelated engineering/factory settings.
3. Open Developer options and enable **Wireless debugging**. Approve the
   network prompt.
4. Choose **Pair device with pairing code**. Keep that dialog open and note its
   IP address, pairing port, and pairing code.
5. In your existing PowerShell window, run:

```powershell
$pairTarget = Read-Host 'Enter the IP:PAIRING_PORT displayed by the pairing-code dialog'
& $adb pair $pairTarget
```

Enter the code only when ADB prompts. Do not put it in an issue or public log.
After successful pairing, return to the main Wireless debugging screen and
note its **IP address & Port**, which is the connection endpoint:

```powershell
$serial = Read-Host 'Enter the IP:CONNECTION_PORT from the main Wireless debugging screen'
& $adb connect $serial
& $adb devices -l
& $adb -s $serial get-state
```

The pairing port and connection port are often different. Use the connection
endpoint for `$serial` and every later `-s` argument. The target must appear in
the device list with state `device`, and `get-state` must report `device`.

If Wireless debugging/pairing is unavailable on this vendor firmware, do not
guess a port or assume that port 5555 is enabled. Use a supported USB debugging
connection or seek device-specific instructions.

### Option B: supported USB debugging

Enable USB debugging in Developer options, use a data-capable connection
supported by the device, and approve the debugging authorization on its screen.
Do not assume every ApplePie USB port exposes a USB debugging interface.

```powershell
& $adb devices -l
$serial = Read-Host 'Enter the exact USB device serial from the list'
& $adb -s $serial get-state
```

Continue only when the selected device reports `device`, not `offline` or
`unauthorized`.

### Confirm the target and 32-bit support

```powershell
& $adb -s $serial shell getprop ro.product.model
& $adb -s $serial shell getprop ro.build.version.release
& $adb -s $serial shell getprop ro.product.cpu.abilist
& $adb -s $serial shell getprop ro.product.cpu.abilist32
& $adb -s $serial shell df -h /sdcard
```

Confirm that this is the intended ApplePie, there is enough storage, and the
reported ABI information includes 32-bit ARM support such as `armeabi-v7a` or
`armeabi`. If only `arm64-v8a` is available, stop: this preview does not turn a
32-bit application into a native 64-bit application. If a vendor reports
inconsistent ABI information, do not treat this check as proof of compatibility.

## 7. Install the generated APK using ADB

Back up any existing StreetPilot installation and data before changing it. This
procedure does not automatically uninstall or clear an existing application.

Use the output APK from section 4:

```powershell
$pkg = 'com.garmin.android.apps.gmobilext'
$compatApk = Join-Path $root 'patcher\StreetPilot_2.37_ApplePie_Compat.apk'
if (-not (Test-Path -LiteralPath $compatApk)) { throw 'Generated compatibility APK not found.' }

& $adb -s $serial install --bypass-low-target-sdk-block $compatApk
```

Wait until ADB reports **Success**. A progress message alone is not proof of a
completed installation. Do not open the application yet.

The low-target-SDK flag is Android's documented testing option for installing
older-target apps. It does not add 32-bit support, override organizational
device policy, or guarantee that a vendor will allow the app to launch.

If StreetPilot is already installed, use the following **only if you intend to
update it and have a backup**:

```powershell
& $adb -s $serial shell am force-stop $pkg
& $adb -s $serial install -r --bypass-low-target-sdk-block $compatApk
```

An in-place update requires a compatible signing certificate. If you see
`INSTALL_FAILED_UPDATE_INCOMPATIBLE`, stop and back up your existing setup.
Do not uninstall as an automatic retry: uninstalling can remove the app's data
directory. Resolve the signing/install-lineage difference deliberately first.

## 8. Transfer the extracted Garmin folder with ADB

The exact required device path is:

```text
/sdcard/Android/data/com.garmin.android.apps.gmobilext/files/Garmin/
```

Keep StreetPilot stopped while copying. These commands are intended for a new
installation or a deliberate replacement with backed-up data. ADB push can
overwrite same-named destination files; do not run it blindly over a working
installation.

```powershell
$filesRemote = '/sdcard/Android/data/com.garmin.android.apps.gmobilext/files'
$garminRemote = "$filesRemote/Garmin"

& $adb -s $serial shell am force-stop $pkg
& $adb -s $serial shell mkdir -p $filesRemote
& $adb -s $serial push $garminLocal "$filesRemote/"
```

Push the **top-level Garmin folder into the parent `files` directory**. Do not
append `Garmin` to the push destination in the example above. This avoids
accidentally ending up with `files/Garmin/Garmin`.

Wait for ADB to report a completed transfer without errors. A large map archive
can take a long time over Wi-Fi. Do not disconnect, sleep the PC, or power off
the device mid-transfer.

Verify the result:

```powershell
& $adb -s $serial shell ls -la $garminRemote
& $adb -s $serial shell ls -la "$garminRemote/.System"
```

The first listing should show the extracted top-level resources; the second
should show the map files you intended to transfer. A directory listing verifies
placement, not complete byte-for-byte integrity of every transferred file.

These locations are wrong for this preview:

- `/sdcard/Garmin/`
- `/sdcard/Android/data/com.garmin.android.apps.gmobilext/Garmin/` (missing `files`)
- `/sdcard/Android/data/com.garmin.android.apps.gmobilext/files/Garmin/Garmin/`

If ADB reports `Permission denied`, stop and record the exact failing path. Do
not use root, broad `chmod`, or delete the Android/data tree as a generic fix.
Vendor storage policies can differ and require a device-specific solution.

## 9. First launch and basic checks

After confirming **both** a successful install and the correct data layout:

1. Open StreetPilot from the ApplePie launcher.
2. Read any Android warning or permission prompt. Approve only permissions you
   understand and wish to grant. Do not disable system security globally.
3. Select your preferred interface language and voice from the resources you
   supplied, when available.
4. Allow time outdoors for the first GNSS fix; poor reception in a garage is
   not by itself an application failure.
5. While parked, check map display, location updates, route calculation, and
   spoken guidance.
6. If you use media playback, test whether guidance lowers media volume as
   expected. Test Android/Google voice input separately if that service is
   available on your device; it can require its own permissions, language
   setup, and connectivity.

If Android still refuses to open the app after a successful ADB installation,
capture the exact wording and device build information. Do not assume that
another security/target-SDK bypass will solve a vendor launch restriction.

After testing, disable wireless/USB debugging when it is no longer needed. For
a wireless connection, disconnect this ADB session:

```powershell
& $adb disconnect $serial
```

For a USB connection, unplug the cable after operations finish. If you reopen
PowerShell later, redefine the variables and reconnect; wireless ports can
change after rebooting or toggling debugging.

## 10. Troubleshooting

| Symptom | What to check |
| --- | --- |
| Patcher rejects the input | Verify the source APK SHA-256 from section 3. Matching the filename is not enough. |
| `adb.exe` is not found | Check the extraction path; avoid accidentally nesting `platform-tools\platform-tools`. |
| Pairing succeeds but installation cannot connect | Use the connection port from the main Wireless debugging screen, not the pairing-dialog port. |
| `unauthorized` or `offline` | Approve authorization on the device or reconnect using its current endpoint. Stop until `get-state` reports `device`. |
| More than one device is connected | Use the intended device's exact `$serial` with `-s`, as all device commands above do. |
| `INSTALL_FAILED_DEPRECATED_SDK_VERSION` | Confirm that the command used the generated APK and `--bypass-low-target-sdk-block`. |
| The bypass flag is reported unsupported | Record the complete error, update official Platform Tools, and check the device's Android version. Do not substitute a File Manager install for this preview. |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | Existing and generated apps have incompatible signers. Preserve data before planning a reinstall. |
| `INSTALL_FAILED_NO_MATCHING_ABIS` | Check ARM32 userspace support; this patcher is not an ARM64 conversion. |
| `INSTALL_FAILED_INSUFFICIENT_STORAGE` or an incomplete push | Check device free space and archive sizes. Do not delete unrelated data automatically. |
| Data files cannot be found, or languages/maps are missing | Confirm extraction, `.System`, `Text`, and `Voice` placement. Check for an extra archive wrapper or `Garmin/Garmin` level. |
| `Permission denied` under Android/data | Preserve the exact failing command/path and request device-specific help; do not apply broad ownership/permission changes. |
| Blank screen, crash, or Android launch restriction | Report the model, Android/build version, install result, and sanitized error/log. Compatibility is not guaranteed on untested hardware. |

## 11. Reporting a problem without exposing private data

Open a [project issue](https://github.com/NavRevival-Lab/streetpilot-237-patcher/issues)
with the patcher version, model, Android version, connection method, failed step,
and exact error. State whether the source APK and release ZIP matched the hashes
above. Do not publish the APK or navigation data.

For a launch failure, an optional **read-only** log capture is:

```powershell
$logPath = Join-Path $root 'StreetPilot_launch_log.txt'
& $adb -s $serial logcat -d -v threadtime | Out-File -FilePath $logPath -Encoding utf8
```

Capture it while debugging is still connected, shortly after reproducing the
failure. Logcat may contain unrelated apps, locations, account data, device
identifiers, and network details. Review and redact it locally before sharing;
do not upload an unreviewed full device log. Redact personal paths, email
addresses, IP addresses, serials, Wi-Fi names, and pairing codes. Do not post
private keys, activation material, maps, original APKs, or patched APKs.

## References and scope

- [Android SDK Platform Tools](https://developer.android.com/tools/releases/platform-tools)
- [Android Debug Bridge: wireless pairing, targeted commands, and transfers](https://developer.android.com/tools/adb)
- [Android 14: minimum installable target API and the testing install flag](https://developer.android.com/about/versions/14/behavior-changes-all#minimum-target-api-level)
- [Patcher license](../LICENSE)

Package-specific filenames, hashes, tested hardware, and storage placement are
based on the v0.9.0 release's bundled README and checksum record. The new online
guide and checksum file do not alter the old release tag or its automatic source
archives. Consult the main-branch version of this guide for these additions.
