# Expo Build Documentation

This guide covers troubleshooting prebuild errors, building the APK (locally and manually), distributing the app, and optimizing app size.

## 1. Troubleshooting Prebuild Errors

If you encounter an error during `npm expo prebuild`, follow these steps:

1.  Create a file named `local.properties` in the `android` folder.
2.  Add the following line to `android/local.properties`:

    ```properties
    sdk.dir=/Users/jibonhossen/Library/Android/sdk
    ```

> [!TIP]
> For more details, check this article: [Building an APK from Expo: Everything You Need to Know](https://dev.to/khokon/building-an-apk-from-expo-everything-you-need-to-know-c7i)

---

## 2. Building the APK

### Option A: EAS Local Build (Standard)

To create an APK file locally using EAS, run:

```bash
expo --profile production --platform android --local
```

**If you face errors**, try specifying the Android SDK path explicitly:

```bash
ANDROID_HOME=/Users/jibonhossen/Library/Android/sdk eas build --profile production --platform android --local
```

### Option B: Manual Gradle Build (Optimized Size)

For the best size optimization (specifically to remove x86 architectures), use the manual Gradle build command.

> [!NOTE]
> `eas build --local` may sometimes ignore architecture filters due to caching. The manual command below is more reliable for this specific optimization.

1.  Navigate to the android directory and build:

    ```bash
    cd android
    ./gradlew clean && ./gradlew assembleRelease
    ```

2.  **Output Location**:
    The generated APK will be located at:
    `android/app/build/outputs/apk/release/app-release.apk`

> [!IMPORTANT]
> This generates an `.apk` file (not an App Bundle `.aab`). This file can be installed directly on physical Android devices but **will NOT work on standard x86 emulators**.

---

## 3. Verify App Size

Once the build is complete, verify the optimization:

*   **Expected Size**: < 60 MB (with x86 architectures removed).
*   **Assets**: Check that your assets folder is optimized (e.g., ~3.1 MB).
*   **Optimization**: Ensure ProGuard, Resource Shrinking, and ABI Filters (ARM only) are enabled.

---

## 4. Distribute via Website

To distribute this app directly from your website:

1.  **Upload**: Upload the `.apk` file to your website's hosting.
2.  **Link**: Create a download link in your HTML:

    ```html
    <a href="path/to/your-app.apk" download>Download App</a>
    ```

3.  **Instructions for Users**:
    *   Tell users to tap "Download".
    *   If they see a security warning, instruct them to allow "Install from unknown sources".

---

## 5. How to Analyze APK Size

If you want to see exactly what is taking up space inside your APK:

### Option A: Command Line (Quickest)
Since an APK is just a ZIP file, you can list its contents sorted by size:

```bash
unzip -v your-app.apk | sort -nr -k 3 | head -n 20
```
*Look for large `.so` files in `lib/x86` or `lib/x86_64` - these are what should be removed for optimization.*

### Option B: Android Studio (Visual)
1.  Open Android Studio.
2.  Drag and drop your `.apk` file into the window.
3.  It will show a breakdown of file sizes by category (`lib`, `assets`, `dex`, etc.).

---

## 6. How to Apply This to Other Apps

To achieve this same < 60MB size on a new Expo app, follow these 3 steps:

### Step 1: Install the Plugin

```bash
npx expo install expo-build-properties
```

### Step 2: Configure app.json

Add `expo-build-properties` to your `app.json` under `expo.plugins`:

```json
[
  "expo-build-properties",
  {
    "android": {
      "enableProguardInReleaseBuilds": true,
      "enableShrinkResourcesInReleaseBuilds": true,
      "extraGradleProps": {
        "reactNativeArchitectures": "armeabi-v7a,arm64-v8a"
      }
    }
  }
]
```

### Step 3: Build Manually

Do not use `eas build --local`. Instead, run:

```bash
cd android
./gradlew clean && ./gradlew assembleRelease
```