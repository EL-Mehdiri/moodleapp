# EWA Learning – Mobile App Build Guide (Android & iOS)

This documentation explains how to build, customize, sign, and publish the **EWA Learning Mobile App**, based on the Moodle Mobile source code, for both **Android** and **iOS**.
It includes all fixes, issues, and solutions we discovered during setup.

---

# 📦 Project Setup

## 🔧 Requirements
### **Global**
- Node.js **v18** (recommended)
- npm
- Ionic CLI
- Cordova CLI
- Git
- Moodle site with mobile services enabled

### **Android**
- Android Studio
- Android SDK 33–36
- Java JDK 17
- Keystore file

### **iOS**
- macOS with Xcode installed
- Apple Developer Account
- iPhone or Simulator

---

# 📁 Project Structure
Clone the Moodle Mobile base app and duplicate it to keep a clean backup:

```sh
git clone https://github.com/moodlehq/moodleapp.git
cp -R moodleapp ewa-learning
```

All customization takes place inside **ewa-learning/**.

---

# 🎨 App Customization

## 1. Update `moodle.config.json`
Example:

```json
{
  "app_id": "ma.ewa.learn",
  "appname": "EWA Learning",
  "versioncode": 51001,
  "versionname": "5.1.0",
  "siteurl": "https://learn.ewa.ma",
  "onlyallowlistedsites": true,
  "sites": [{ "url": "https://learn.ewa.ma" }],
  "forceLogin": true,
  "forceLoginLogo": true,
  "showTopLogo": "visible"
}
```

---

## 2. Update `config.xml`
Important fields:

```xml
<widget id="ma.ewa.learn" version="5.1.0">
    <name>EWA Learning</name>
    <preference name="AndroidWindowSplashScreenAnimatedIcon" value="resources/android/splash/splash.xml" />
</widget>
```

---

## 3. Changing Icons & Splash

### ✔️ Generate icons & splash automatically:
Place images here:

```
resources/icon.png
resources/splash.png
```

Then run:

```sh
ionic cordova resources android --force
ionic cordova resources ios --force
```

### ✔️ Custom splash using vector
Modify:

```
resources/android/splash/splash.xml
```

This is the **correct way** because Cordova regenerates the Android project.

---

# 💡 Fix for the First-Splash Showing Moodle Logo
If the very first splash still shows Moodle:

1. Delete platforms:

```sh
rm -rf platforms/android
rm -rf platforms/ios
```

2. Regenerate resources:

```sh
ionic cordova resources android --force
 ionic cordova resources ios --force
```

3. Re-add platform:

```sh
ionic cordova platform add android
ionic cordova platform add ios
```

---

# 🖌 Changing App Colors (Theme)

Edit:

```
src/theme/globals.variables.scss
```

Replace Moodle colors with your brand:

```scss
$primary: #26396A !default;
$secondary: #CCCC40 !default;
$tertiary: #CC2E2B !default;
$darkgray: #101010 !default;
$gray1: #5C5C5C !default;
$gray2: #888888 !default;
$gray3: #AAAAAA !default;
$gray4: #E7E7E7 !default;
$gray5: #F3F3F3 !default;
```

Also update theme light & dark:

```
src/theme/theme.light.scss
src/theme/theme.dark.scss
```

---

# 📱 Android Build Guide

## 1. Clean Platforms
```sh
rm -rf platforms/android
rm -rf plugins
npm install
```

## 2. Add platform
```sh
ionic cordova platform add android
```

If you get image errors:
→ ensure all icons exist in `resources/android/icon`

## 3. Build release AAB
```sh
ionic cordova build android --release
```

AAB location:

```
platforms/android/app/build/outputs/bundle/release/app-release.aab
```

---

# 🔐 Signing the Android App

## 1. Use your existing upload key

Example location:
```
/Users/ad/MoodleApp/moodleapp/my-release-key.keystore
```

## 2. Sign the AAB:
```sh
jarsigner -verbose  -sigalg SHA256withRSA  -digestalg SHA-256  -keystore /path/to/your.keystore  platforms/android/app/build/outputs/bundle/release/app-release.aab  aliasName
```

---

# 🚀 Upload to Google Play

### If package names match → upload directly
The package must be identical:

```
ma.ewa.learn
```

Upload in:

```
Play Console → Existing app → Internal testing → Add new release
```

---

# 📱 iOS Build Guide

## 1. Add iOS platform
```sh
ionic cordova platform add ios
```

## 2. Build iOS project
```sh
ionic cordova build ios
```

If error about missing simulator:

Open:

```
Xcode → Settings → Platforms
```

Download iOS 18.x simulator.

Then open the project:

```
platforms/ios/EWA Learning.xcworkspace
```

---

# 🍏 Publishing to App Store

## 1. Open Xcode
- Select **Team (Apple Developer)**
- Set **Bundle Identifier**: `ma.ewa.learn`
- Set **Version** and **Build Number**

## 2. Archive
```
Product → Archive
```

## 3. Upload to App Store Connect
Use:

- Xcode Organizer
- OR Transporter app

---

# 🔔 Push Notifications Setup

You have two options:

---

## OPTION 1 – Use Moodle Official Push Service
Fastest, simplest.

You need to apply for a free key:
👉 https://apps.moodle.com

Enable in Moodle:

```
Site administration → Messaging → Notification settings → Mobile
```

---

## OPTION 2 – Self‑host AirNotifier (Advanced)

### Using Docker:
```sh
docker compose up -d
```

Access UI:
```
http://localhost:8801
```

Set Moodle FCM keys and generate **AppID**.

Add to Moodle:

```
Site administration → Server → Messaging → Notifications server
```

---

# 🐛 Common Errors & Fixes

### ❌ `smallicon.png not found`
→ You must manually create these icons inside `resources/android/icon/`.

---

### ❌ First splash still shows Moodle
→ You forgot to delete `platforms/` and regenerate.

---

### ❌ iOS build error "device not found"
→ Install simulator:

Xcode → Settings → Platforms → Download iOS simulator

---

### ❌ Google Play "invalid signature"
→ Must always use the **same keystore** as the first release.

---

# 🎉 DONE — Your App is Fully Working!

This documentation reflects all fixes, issues, and solutions i discovered while customizing, building, signing, fixing splash, applying theme, and publishing the Android version, and preparing for iOS publishing.
