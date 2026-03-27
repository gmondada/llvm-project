# Android LLDB

This extension enables debugging of Android apps with a smooth and intuitive user experience. It is designed for debugging Swift on Android, but is not limited to Swift. It can be used for any language supported by LLDB.

## Configuration

The extension uses `lldb-dap`, an executable provided with the Swift toolchain. You need to set its path in `.vscode/settings.json`:

```json
{
  "lldb-dap.executable-path": "xxx/usr/bin/lldb-dap"
}
```

Replace `xxx` with the path to your toolchain. If you don’t know it, run:

```bash
swiftly use --print-location
```

Then you need to provide the ID of the app you want to debug. Create or edit `.vscode/launch.json` and add:

```json
{
  "configurations": [
    {
      "type": "lldb-dap-android",
      "request": "launch",
      "name": "My App",
      "androidComponent": "org.example.helloswift/.MainActivity"
    }
  ]
}
```

Replace `org.example.helloswift` with your app ID.

## Build and install the app

Before debugging, you need to connect an Android device or run an emulator, and it must be accessible via ADB. The extension cannot start the emulator or ADB for you yet.

Then, you need to build and install the app. If your project uses Gradle, run:

```bash
./gradlew assembleDebug
./gradlew installDebug -Pandroid.injected.testOnly=true
```

In theory, you could just run `./gradlew installDebug`, but due to a bug in `lldb-server` (see https://github.com/llvm/llvm-project/pull/173966 ), we need to make sure the last entry in the APK is not a shared object. These two commands do the trick.

## Run the debug session

In VS Code, set breakpoints in your Swift code, then go to the Debug tab and start the session with the ▶ button (or press F5). That's it.

## Additional options

The extension needs access to the Android NDK. In particular, it uses `lldb-server` provided with the NDK. If the Android SDK is installed in its default location and the NDK is installed as part of the SDK, the extension should find `lldb-server` automatically.

If not, you can set the path to the NDK in `.vscode/settings.json`:

```json
{
  "lldb-dap.executable-path": "xxx/usr/bin/lldb-dap",
  "lldb-dap.androidNDKPath": "/Users/me/Library/Android/sdk/ndk/27.1.12297006"
}
```

If you have multiple devices connected, you can specify the target device in `.vscode/launch.json`:

```json
{
  "configurations": [
    {
      "type": "lldb-dap-android",
      "request": "launch",
      "name": "My App",
      "androidComponent": "org.example.helloswift/.MainActivity",
      "androidDevice": "emulator-5554"
    }
  ]
}
```
