# StreetPilot 2.37 Compatibility Patcher

Compatibility tooling for running a user-supplied copy of StreetPilot 2.37 on
selected newer Android devices.

> [!IMPORTANT]
> This is an independent compatibility project. It is not affiliated with,
> sponsored by, approved by, or endorsed by Garmin.

## Project status

The project is being released in stages.

| Release stage | Intended users | Installation experience |
| --- | --- | --- |
| **Current: Tech Preview** | Technically experienced testers | Requires ADB, Android debugging access, and manual file transfer |
| **In development: user-friendly releases** | General users | Intended to provide a guided patching, validation, and installation workflow |

The Tech Preview is an early public release for compatibility testing. It is
not the final form of the patcher. A user-friendly release is in development,
with later releases expected to reduce the
amount of manual ADB work, provide clearer validation and diagnostics, and make
device preparation easier.

No release date or compatibility with every Android device is guaranteed.

## What the patcher does

The patcher is designed to:

- accept a supported, user-supplied StreetPilot 2.37 APK;
- verify that the supplied APK matches a supported source version;
- apply the required Android compatibility changes locally;
- produce a patched APK for the user's own device; and
- provide release-specific installation and data-placement instructions.

Exact supported APK hashes, device requirements, patch behavior, and
installation steps are documented in each release's notes and included guide.

## What this project does not provide

This repository and its release packages do **not** contain or distribute:

- the original Garmin StreetPilot APK;
- Garmin maps, voices, vehicle files, traffic data, or other Garmin content;
- product keys, activation material, or signing keys belonging to another
  party; or
- a license to use any third-party software or data.

You must provide your own legally obtained copy of the supported application
and any required data. You are responsible for ensuring that your possession,
modification, installation, and use of those materials are lawful in your
jurisdiction.

## Downloading a release

Download the patcher only from this repository's
[Releases](../../releases) page.

For each release:

1. Read the release notes and confirm that your device and source APK are
   supported.
2. Download the release ZIP and its SHA-256 checksum file.
3. Verify the ZIP checksum before opening it.
4. Extract the complete ZIP to a new folder.
5. Read the included instructions before running any script or executable.

Do not download repackaged copies from third-party mirrors. The license permits
sharing a link to the official release page, but not redistributing the release
files themselves.

## Current Tech Preview requirements

The current Tech Preview is intended for users who are already comfortable
with Android debugging tools. Depending on the release and target device, you
may need:

- a Windows PC;
- Android Platform Tools (`adb`);
- USB debugging or wireless debugging enabled on the Android device;
- permission to install an older-target Android application through ADB;
- the exact supported StreetPilot 2.37 APK; and
- access to the storage location used for the application's required data.

Newer Android versions may restrict normal file-manager access to application
data directories. The Tech Preview therefore uses ADB for installation and,
when required, for transferring files to the correct application-owned
location.

Follow the instructions bundled with the particular release. Commands and file
locations may change as compatibility work progresses.

## User-friendly releases in development

The planned user-friendly workflow is intended to guide users through:

- selecting their own original APK;
- validating the APK before modification;
- creating the compatible APK locally;
- preparing the required data layout;
- installing the application with fewer manual steps; and
- diagnosing common device, permission, and connection problems.

Until such a release is published, users who are not comfortable with ADB
should wait rather than improvise installation commands.

## Safety and limitations

This project modifies application binaries and interacts with Android
installation and storage mechanisms. Before proceeding:

- back up the original APK and all application data;
- keep navigation data on separate backup media where practical;
- confirm that ADB is connected to the intended device;
- do not disconnect or power off the device during an active installation or
  file-transfer operation; and
- do not perform installation, testing, or troubleshooting while driving.

The patcher is experimental. A release may fail on an untested device, Android
build, screen configuration, or vendor-modified system. Successful operation on
one device does not guarantee operation on another.

## Reporting a problem

When opening an issue, include only the information needed to reproduce the
problem:

- patcher release version;
- device make and model;
- Android version;
- whether USB or wireless ADB was used;
- the step that failed;
- the exact error message; and
- a sanitized diagnostic log, if requested by the release guide.

Before posting, remove personal or identifying information such as usernames,
email addresses, computer names, local account paths, IP addresses, Wi-Fi
network names, device serial numbers, and unrelated application data.

Do **not** upload your original or patched StreetPilot APK, Garmin data, maps,
private signing material, or other proprietary files to an issue.

## Binary distribution and source availability

This repository is the official release and documentation hub for the patcher.
The patcher is distributed as compiled binaries; its source code is not
published in this repository.

## License

The distributed patcher, accompanying scripts, documentation, and patch data
are proprietary and are governed by the terms in [`LICENSE`](LICENSE), unless
a particular release expressly states otherwise.

The current license permits personal, non-commercial testing and evaluation of
an unmodified copy. It does not permit redistribution, mirroring, repackaging,
modification, sublicensing, or commercial distribution.

The project license applies only to material distributed by LegacyNavTools. It
does not grant any rights to Garmin software, maps, data, or trademarks.

## Trademark notice

Garmin and StreetPilot are trademarks of their respective owner. All other
product names and trademarks belong to their respective owners. Their use here
is solely to identify software compatibility.
