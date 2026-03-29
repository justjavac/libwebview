# libwebview

Static `webview` build outputs for MoonBit.

## Local build

Windows:

```powershell
cmake -S . -B build -G Ninja -D CMAKE_BUILD_TYPE=Release
cmake --build build --config Release
cmake --install build --config Release --prefix stage
```

macOS:

```bash
cmake -S . -B build -G Ninja -D CMAKE_BUILD_TYPE=Release \
  -D CMAKE_OSX_ARCHITECTURES="x86_64;arm64" \
  -D CMAKE_OSX_DEPLOYMENT_TARGET=11.0
cmake --build build --config Release
cmake --install build --config Release --prefix stage
```

Installed files are written to `stage/`:

- `include/webview/...`
- `lib/webview.lib` on Windows
- `lib/libwebview.a` on macOS
- `lib/cmake/webview/...`

## GitHub Actions

The workflow lives in `.github/workflows/ci.yml`.

It runs on:

- every push
- pull requests
- manual `workflow_dispatch`

Uploaded artifacts:

- `libwebview-linux-x64`
- `libwebview-windows-x64`
- `libwebview-macos-universal`

Each artifact contains headers, the static library, CMake package files, and a `BUILD_INFO.txt` file with the pinned upstream revision and link notes.

## Link requirements

Final consumers still need platform system dependencies:

- Linux: `pkg-config --cflags --libs gtk+-3.0 webkit2gtk-4.1` and `-ldl`
- Windows: `advapi32 ole32 shell32 shlwapi user32 version`
- macOS: `-framework WebKit -ldl`
