# Warrior Developer — How to Convert this Folder into an APK

This folder is a complete Android Studio project. Follow these steps to build `WarriorDeveloper.apk`.

## 1. Install Android Studio
- Download from https://developer.android.com/studio
- Install with default options (it will also install the Android SDK).

## 2. Open the Project
1. Launch **Android Studio**.
2. Click **File → Open** (or "Open an Existing Project" on the welcome screen).
3. Select the folder `WarriorDeveloper` (the one containing `settings.gradle`).
4. Click **OK**. Android Studio will start "Gradle Sync".
5. If it asks to install missing components (SDK 34, Build Tools, etc.), click **Install** and let it finish. This can take 5–15 minutes the first time.

## 3. (Optional) Change the VPS IP
If your VPS IP/port ever changes, edit:
```
app/src/main/java/com/warrior/developer/KeyActivity.java
```
Change this line:
```java
public static final String VPS_BASE = "http://80.241.210.193:5001";
```

## 4. Build the APK
There are two ways:

### A. Build → Build APK (Debug, easiest)
1. Top menu: **Build → Build Bundle(s) / APK(s) → Build APK(s)**
2. Wait until the green popup says **"APK(s) generated successfully"**.
3. Click **locate** → it opens the folder, e.g.
   `WarriorDeveloper/app/build/outputs/apk/debug/app-debug.apk`
4. Rename it to `WarriorDeveloper.apk` and transfer it to your phone.

### B. Build a signed Release APK (recommended for distribution)
1. **Build → Generate Signed Bundle / APK**
2. Choose **APK** → Next
3. Click **Create new...** to make a keystore (fill in name, password — remember them!)
4. Choose **release** variant → Finish.
5. Output: `app/build/outputs/apk/release/app-release.apk`

## 5. Install on Phone
- Copy the APK to your phone (USB / Telegram / Drive).
- On the phone: **Settings → Security → Allow install from unknown sources** for your file manager.
- Tap the APK → Install.
- Open **Warrior Developer** → enter the key you created on the dashboard → Validate.

## 6. How it works
- First launch → asks for **Key**.
- On Validate, the app sends `{key, device_id}` to `http://80.241.210.193:5001/api/validate`.
- VPS replies with `email`, `password`, and binds the device.
- The app saves credentials in private SharedPreferences (so it never asks again on this phone).
- It opens the VISA URL with `?view=free`, helps click the Cloudflare Turnstile checkbox (does **not** bypass it — uses the same approach as the hafiz consultant helper script: it just clicks the visible "I am human" box once it's rendered), then auto-fills email + password and submits the form to the exact URL `…/WORK_National_VISA?view=free`.

## Troubleshooting
- **"Gradle sync failed"** → File → Invalidate Caches → Invalidate and Restart.
- **"SDK not found"** → File → Project Structure → SDK Location → set Android SDK path (default `~/Android/Sdk` or `C:\Users\<you>\AppData\Local\Android\Sdk`).
- **Network error in app** → make sure VPS is running (`systemctl status warrior`) and port 5001 is open in firewall (`ufw allow 5001/tcp`).
- **Cloudflare not solving** → Turnstile sometimes needs a real human tap. The app will still wait and let you tap once if needed; after that it auto-fills.
