# Expo Build Documentation

when you face npm expo prebuild error then run this command

create a file in the android folder

- local.properties

write this line in the local.properties file

sdk.dir=/Users/jibonhossen/Library/Android/sdk



when want to create an apk file localy then run this command

expo --profile production --platform android --local


or if you face any error then run this command

ANDROID_HOME=/Users/jibonhossen/Library/Android/sdk eas build --profile production --platform android --local




Build & Distribution Walkthrough
1. Build the APK
For the best size optimization (removing x86 architectures), use the manual Gradle build command:

cd android
./gradlew clean && ./gradlew assembleRelease
The output APK will be located at: 
android/app/build/outputs/apk/release/app-release.apk

NOTE

eas build --local may sometimes ignore architecture filters due to caching. The manual command above is more reliable for this specific optimization.

NOTE

This will generate an 
.apk
 file (not an App Bundle .aab). This file can be installed directly on Android devices.

2. Verify App Size
Once the build is complete, download the APK.

Expected Size: < 60 MB (Removed x86 architectures).
Assets: Your assets folder is only 3.1 MB.
Optimization: ProGuard, Resource Shrinking, and ABI Filters (ARM only) are enabled.
IMPORTANT

This APK will work on physical Android devices but NOT on standard x86 emulators.

3. Distribute via Website
To distribute this app on your website:

Upload: Upload the 
.apk
 file to your website's hosting.
Link: Create a download link:
<a href="path/to/your-app.apk" download>Download App</a>
Instructions for Users:
Tell users to tap "Download".
When opening the file, they may see a security warning.
Instruct them to allow "Install from unknown sources" if prompted.
4. How to Analyze APK Size
If you want to see exactly what is taking up space inside your APK:

Option A: Command Line (Quickest)
Since an APK is just a ZIP file, you can list its contents sorted by size:

unzip -v your-app.apk | sort -nr -k 3 | head -n 20
Look for large .so files in lib/x86 or lib/x86_64 - these are what we removed.

Option B: Android Studio (Visual)
Open Android Studio.
Drag and drop your 
.apk
 file into the window.
It will show a breakdown of file sizes by category (lib, assets, dex, etc.).
5. How to Apply This to Other Apps
To achieve this same < 60MB size on a new Expo app, follow these 3 steps:

Step 1: Install the Plugin
npx expo install expo-build-properties
Step 2: Configure app.json
Add this to your 
app.json
 under expo.plugins:

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
Step 3: Build Manually
Don't use eas build --local. Instead, run:

cd android
./gradlew clean && ./gradlew assembleRelease