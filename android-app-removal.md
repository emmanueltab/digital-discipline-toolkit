# Android App Removal

Removing or disabling apps on Android eliminates mobile access points entirely. A browser on your phone can bypass every layer of protection you have set up on your computer, so addressing mobile separately is essential.

There are two approaches depending on your level of access.

---

## Option 1: Built-in Settings (No tools required)

Some apps can be disabled directly from Android settings without any extra tools.

1. Go to **Settings → Apps**
2. Find the app you want to disable
3. Tap **Disable** or **Uninstall**

This works for most user-installed apps. System apps may not have a disable option depending on your device and Android version.

---

## Option 2: ADB (Android Debug Bridge)

ADB gives you full control over system apps that cannot be removed through settings. This works without rooting your device.

### Prerequisites

Install ADB on your computer:
```bash
sudo apt install adb
```

Enable Developer Options on your Android device:
1. Go to **Settings → About Phone**
2. Tap **Build Number** 7 times
3. Go back to **Settings → Developer Options**
4. Enable **USB Debugging**

Connect your device via USB and verify the connection:
```bash
adb devices
```

Your device should appear in the list. Accept the authorization prompt on your phone if it appears.

---

### Uninstalling apps via ADB

To remove an app for the current user without deleting it from the system:
```bash
adb shell pm uninstall -k --user 0 com.package.name
```

Replace `com.package.name` with the actual package name of the app.

### Finding a package name
```bash
adb shell pm list packages | grep keyword
```

Replace `keyword` with part of the app name. For example:
```bash
adb shell pm list packages | grep youtube
adb shell pm list packages | grep browser
```

---

### Restoring an app

If you need to restore an app you removed:
```bash
adb shell cmd package install-existing com.package.name
```

---

## Examples

### YouTube

Remove:
```bash
adb shell pm uninstall -k --user 0 com.google.android.youtube
```
Restore:
```bash
adb shell cmd package install-existing com.google.android.youtube
```

### Google Search

Remove:
```bash
adb shell pm uninstall -k --user 0 com.google.android.googlequicksearchbox
```
Restore:
```bash
adb shell cmd package install-existing com.google.android.googlequicksearchbox
```

### Google Play Store

Remove:
```bash
adb shell pm uninstall -k --user 0 com.android.vending
```
Restore:
```bash
adb shell cmd package install-existing com.android.vending
```

Note: Removing the Play Store prevents installing new apps from the store. Remove this last after installing everything you need.

### Chrome

Remove:
```bash
adb shell pm uninstall -k --user 0 com.android.chrome
```
Restore:
```bash
adb shell cmd package install-existing com.android.chrome
```

---

## Common packages reference

| App | Package Name |
|-----|-------------|
| YouTube | com.google.android.youtube |
| Chrome | com.android.chrome |
| Google Play Store | com.android.vending |
| Google Search | com.google.android.googlequicksearchbox |
| Instagram | com.instagram.android |
| TikTok | com.zhiliaoapp.musically |
| Twitter/X | com.twitter.android |
| Google Play Games | com.google.android.play.games |

---

## Tips

- Remove apps one at a time and test after each removal to avoid breaking dependencies
- Keep a list of everything you remove so you can restore if needed
- Remove the Play Store last after you have removed everything else
- A factory reset will restore all removed apps

---

## Limitations

- ADB removals are per user, not permanent — a factory reset restores everything
- Some apps are dependencies for others and removing them can cause issues
- Does not affect mobile browsers unless you remove them specifically
