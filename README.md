# Warrior Developer - Android Browser Bot

This folder is an **Android Studio project**. You will open it in Android Studio and click **Build → Build APK** to produce `WarriorDeveloper.apk`.

## What it does

1. App launches → shows **"Enter Key"** screen (orange Warrior theme).
2. User types a key (e.g. `1`) → taps **Validate**.
3. APK calls `POST http://80.241.210.193:5001/api/validate` with `{key, device_id}`.
4. VPS returns the client's email + password + target URL.
5. App stores authorization in SharedPreferences → device is now bound (no key prompt next time).
6. App opens the SuperSaaS VISA login URL inside a built-in WebView.
7. JavaScript helper:
   - Watches for the Cloudflare Turnstile checkbox and clicks it (human-like, not a bypass).
   - Waits for the SuperSaaS login form, fills email + password.
   - Rewrites form action to include `?view=free` and submits.

## Convert this folder into `WarriorDeveloper.apk`

### Step 1 — Install Android Studio
Download from <https://developer.android.com/studio> (Windows / Mac / Linux). During first launch, let it install the Android SDK (API 34 recommended).

### Step 2 — Open the project
1. Launch Android Studio.
2. Choose **Open** (not "New Project").
3. Select the unzipped `WarriorDeveloper` folder.
4. Android Studio will say *"Gradle sync"* — accept any prompt to install missing SDK / build tools. Wait until the bottom status bar says "Gradle sync finished".

### Step 3 — Build the APK
1. Top menu: **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
2. After a minute you will see a popup: **"APK(s) generated successfully"** with a **locate** link.
3. Click **locate** → it opens `app/build/outputs/apk/debug/app-debug.apk`.
4. Rename it to **`WarriorDeveloper.apk`** and send it to your phone.

### Step 4 — Install on Android
1. Copy the APK to your phone (Telegram / Email / USB).
2. On phone, tap the APK → allow "Install from unknown sources" if asked.
3. The icon **Warrior Developer** appears in the app drawer.

### Step 5 — Build a signed Release APK (optional but recommended for sharing)
1. **Build → Generate Signed Bundle / APK → APK**.
2. Click **Create new keystore** → save it somewhere safe (you'll reuse this for every update).
3. Enter passwords + alias (e.g. `warrior`).
4. Choose **release** → **V1 + V2 signature** → Finish.
5. Output ends up in `app/release/app-release.apk`.

## Files of interest

| File | Purpose |
|---|---|
| `app/src/main/java/com/warrior/developer/KeyActivity.java` | Key entry + VPS validation + auto-skip when already authorized |
| `app/src/main/java/com/warrior/developer/BrowserActivity.java` | WebView + Cloudflare-checkbox helper + login auto-fill |
| `app/src/main/AndroidManifest.xml` | App config, internet permission, cleartext traffic for your VPS |
| `app/src/main/res/xml/network_security_config.xml` | Allows HTTP to `80.241.210.193:5001` |
| `app/src/main/res/layout/activity_key.xml` | Key screen UI |
| `app/src/main/res/layout/activity_browser.xml` | WebView screen |

## Change the VPS IP later

Edit `KeyActivity.java`:
```java
public static final String VPS_BASE = "http://80.241.210.193:5001";
```

## Troubleshooting

- **"Gradle sync failed"** → File → Sync Project with Gradle Files, or update the Gradle plugin if Studio asks.
- **"SDK location not found"** → File → Project Structure → SDK Location → set Android SDK path.
- **APK installs but says "Connection failed"** → make sure your VPS firewall allows port 5001 (`ufw allow 5001/tcp`) and `python3 brain.py` is running.
- **Browser shows blank page** → the Cloudflare check is normal. The injected script clicks the checkbox automatically; just wait 5–10 s.
