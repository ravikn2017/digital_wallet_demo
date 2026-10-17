# React Native Android Development Rules

Applies when editing `mobile/android/**`, `mobile/.env*`, or debugging Android connectivity.

## API Base URLs (API Gateway — Phase 4+)

| Target | URL |
|--------|-----|
| Android Emulator | `http://10.0.2.2:8080` |
| Physical device | `http://<Mac-LAN-IP>:8080` |

- **Never use `localhost` on the Android emulator** — `10.0.2.2` is the emulator alias for the host machine
- Clients talk to the **gateway** only — not auth `:8081` or wallet `:8082` directly
- Document URL changes in env config — never hardcode IPs in Kotlin, Java, or TypeScript source
- Physical device and Mac must be on the **same Wi-Fi network**

## Android Studio Workflow

- Open `mobile/android/` in Android Studio; run Gradle sync before first build
- Verify device connectivity: `adb devices`
- Run Metro bundler (`cd mobile && npm start`) before Android Studio Run
- Launch via CLI: `cd mobile && npm run android`
- Test on both AVD emulator and physical device before marking auth work complete

## Backend Prerequisites

Before mobile testing, ensure backend is running:

```bash
cd backend && docker compose up -d
cd auth-service && mvn spring-boot:run
# also: wallet-service, api-gateway
```

API Gateway must be reachable on port **8080**.

## Cleartext HTTP (Dev Only)

Android blocks plain HTTP by default. Use `network_security_config.xml` for dev domains (`localhost`, `10.0.2.2`, Mac LAN IP). Remove or restrict for production builds.

Reference in `AndroidManifest.xml`:

```xml
<application android:networkSecurityConfig="@xml/network_security_config" ... >
```

## Environment

- **JDK 17** required for Gradle builds
- Set `JAVA_HOME` if Gradle build fails
- Ensure `ANDROID_HOME` and platform-tools are on `PATH`:

```bash
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator:$ANDROID_HOME/platform-tools
```

## Physical Device Checklist

1. Enable Developer Options (tap Build Number 7 times)
2. Enable USB Debugging
3. Connect via USB; confirm with `adb devices`
4. Add Mac LAN IP to `network_security_config.xml` for device testing

## Related Rules

- Shared RN conventions: `mobile.md`
- iOS setup: `mobile-ios-dev.md`
