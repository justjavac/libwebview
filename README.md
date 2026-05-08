# libwebview

Static and shared `webview` build outputs for MoonBit.

## Local build

Windows:

```powershell
cmake -S . -B build -G Ninja -D CMAKE_BUILD_TYPE=Release
cmake --build build --config Release
cmake --install build --config Release --prefix stage
```

Build the shared library by toggling the wrapper options:

```powershell
cmake -S . -B build-shared -G Ninja -D CMAKE_BUILD_TYPE=Release `
  -D LIBWEBVIEW_BUILD_SHARED_LIBRARY=ON `
  -D LIBWEBVIEW_BUILD_STATIC_LIBRARY=OFF
cmake --build build-shared --config Release
cmake --install build-shared --config Release --prefix stage-shared
```

macOS:

```bash
cmake -S . -B build -G Ninja -D CMAKE_BUILD_TYPE=Release \
  -D CMAKE_OSX_ARCHITECTURES="x86_64;arm64" \
  -D CMAKE_OSX_DEPLOYMENT_TARGET=11.0 \
  -D LIBWEBVIEW_BUILD_SHARED_LIBRARY=ON \
  -D LIBWEBVIEW_BUILD_STATIC_LIBRARY=OFF
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

- `libwebview-static-linux-x64`
- `libwebview-static-windows-x64`
- `libwebview-static-macos-universal`
- `libwebview-shared-linux-x64`
- `libwebview-shared-windows-x64`
- `libwebview-shared-macos-universal`

Each artifact contains headers, library/runtime outputs, CMake package files, and a `BUILD_INFO.txt` file with the pinned upstream revision and link notes.

## Link requirements

Final consumers still need platform system dependencies:

- Linux: `pkg-config --cflags --libs gtk+-3.0 webkit2gtk-4.1` and `-ldl`
- Windows: `advapi32 ole32 shell32 shlwapi user32 version`
- macOS: `-framework WebKit -ldl`
