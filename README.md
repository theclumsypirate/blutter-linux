# Blutter on Linux
Flutter Mobile Application Reverse Engineering Tool by Compiling Dart AOT Runtime

Currently the application supports only Android libapp.so (arm64 only).
Also the application is currently work only against recent Dart versions.


## Environment Setup
This application uses C++20 Formatting library. It requires very recent C++ compiler such as g++>=13, Clang>=16.

I recommend using Linux OS (only tested on Deiban sid/trixie) because it is easy to setup.

## Usage
Extract "lib" directory from apk file
```
python3 blutter.py path/to/app/lib/arm64-v8a out_dir
```
The blutter.py will automatically detect the Dart version from the flutter engine and call executable of blutter to get the information from libapp.so.

If the blutter executable for required Dart version does not exists, the script will automatically checkout Dart source code and compiling it.

## Prebuilt Linux Release (no build needed)
Prebuilt `tar.gz` archives are published on the [latest Release](../../releases/latest) page by the
`build-linux` workflow (manual dispatch only, auto-incremented tag such as `v1.0.0`).
The archive name encodes the Dart version, target OS and architecture:

```
blutter-linux-x64-dart<dart_version>-<target_os>-<target_arch>-<tag>.tar.gz
```

Pick the archive that matches the Dart version of your `libapp.so`
(use `python3 extract_dart_info.py` or check the workflow run summary if unsure).
No compiler is needed, only a Python 3 interpreter and the Capstone/ICU
runtime libraries (same packages as in Environment Setup, minus the build tools).

```
tar -xzf blutter-linux-x64-dart3.8.1-android-arm64-v1.0.0.tar.gz
mkdir -p input/arm64-v8a out
# put libapp.so + libflutter.so into input/arm64-v8a/
python3 blutter.py input/arm64-v8a out
```

The `bin/` directory already contains the prebuilt `blutter_dartvm*` executable
for that Dart version, so the analysis starts immediately without compiling.
Results (classes, functions, assemblies) are written to `out/`.

## Output files
- **asm/\*** libapp assemblies with symbols
- **blutter_frida.js** the frida script template for the target application
- **objs.txt** complete (nested) dump of Object from Object Pool
- **pp.txt** all Dart objects in Object Pool


## Directories
- **bin** contains blutter executables for each Dart version in "blutter_dartvm\<ver\>\_\<os\>\_\<arch\>" format
- **blutter** contains source code. need building against Dart VM library
- **build** contains building projects which can be deleted after finishing the build process
- **dartsdk** contains checkout of Dart Runtime which can be deleted after finishing the build process
- **external** contains 3rd party libraries for Windows only
- **packages** contains the static libraries of Dart Runtime
- **scripts** contains python scripts for getting/building Dart
