name: Build Radiance

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  build:
    runs-on: windows-2022

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: Apply Scaling Fix
        shell: pwsh
        run: |
          $file = "src/services/DisplayService.cpp"
          $code = Get-Content $file -Raw

          $old = "const Put put = PutScaling(device, *scaling);"

          $new = @'
          if (change.modeAfter && nv_.Drives(device))
              Sleep(300);

          const Put put = PutScaling(device, *scaling);
          '@

          if (-not $code.Contains($old)) {
              throw "Patch target not found."
          }

          $code = $code.Replace($old, $new)
          Set-Content -Path $file -Value $code -Encoding utf8

      - name: Build Radiance
        shell: cmd
        run: call build.bat

      - name: Upload Radiance
        uses: actions/upload-artifact@v4
        with:
          name: Radiance-ScalingFix-x64
          path: build/Release/Radiance.exe
          if-no-files-found: error
