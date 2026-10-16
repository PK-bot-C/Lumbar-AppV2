[README.md](https://github.com/user-attachments/files/33527968/README.md)
# Lumbar app - Android + iPhone

One app, two ways to run it.

| | Android | iPhone |
|---|---|---|
| A. Web page (`web-build/index.html` on https) | Chrome - works | Safari / Chrome do NOT work (no Web Bluetooth). Use the free **Bluefy** browser |
| B. Native app (this project, built with Capacitor) | works | works (needs a Mac + Xcode to build) |

The firmware does not change. The ESP32 works with both.

## A. Fastest: web page

1. Host `web-build/index.html` over https (GitHub Pages, as before).
2. Android: open the link in Chrome, tap CONNECT.
3. iPhone: install **Bluefy** from the App Store (free), open the same link in Bluefy, tap CONNECT.

Bluefy is a third-party browser. Keep the screen on while adjusting; iOS suspends Bluetooth in the background (the firmware stops the pumps within about half a second if the phone goes quiet).

## B. Native app for both phones

You need a computer with Node.js 20 or newer. For iPhone you also need a Mac with Xcode.

1. Copy this folder to your computer, open a terminal in it:
   ```
   npm install
   npm run build
   ```
2. (Optional) change `appId` in `capacitor.config.json` (for example `com.yourname.lumbar`) BEFORE adding a platform.

### Android
```
npx cap add android
npx cap sync android
npx cap open android
```
In Android Studio: plug in the phone (USB debugging on) and press Run. The first launch asks for Bluetooth / Nearby devices (and Location on some versions). Allow them. For a shareable APK: Build > Build Bundle(s) / APK(s) > Build APK(s).

### iPhone (Mac + Xcode only)
```
npx cap add ios
```
Open `ios/App/App/Info.plist` and add inside the top-level `<dict>`:
```
<key>NSBluetoothAlwaysUsageDescription</key>
<string>Lumbar uses Bluetooth to connect to the airbag controller.</string>
```
Without it the app crashes the moment it uses Bluetooth. Then:
```
npx cap sync ios
npx cap open ios
```
In Xcode: select the App target > Signing & Capabilities > Team (your Apple ID) and make the Bundle Identifier unique. Plug in the iPhone, turn on Developer Mode (Settings > Privacy & Security), choose the iPhone and press Run. On the phone trust the developer (Settings > General > VPN & Device Management).

- A free Apple ID: the app stops opening after 7 days, then you run it from Xcode again.
- Apple Developer Program (about US$99 per year): longer installs and TestFlight for sharing.
- The iOS Simulator has no Bluetooth. Test on a real iPhone.
- Needs iOS 16 or newer (the layout uses container query units).

### After you change the app
```
npm run sync
```
then press Run again in Android Studio or Xcode.

## Files
- `index.html`, `src/main.js` - the app. `main.js` picks Web Bluetooth in a browser and the native Bluetooth plugin inside the Capacitor app.
- `web-build/index.html` - ready-to-host single file (same as `npm run build` output).
- `capacitor.config.json`, `vite.config.js`, `package.json` - project settings.
