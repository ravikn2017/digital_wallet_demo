# React Native iOS Development Rules

Applies when editing `mobile/ios/**`, `mobile/.env*`, or debugging iOS device connectivity.

## API Base URLs (API Gateway — Phase 4+)

| Target | URL |
|--------|-----|
| iOS Simulator | `http://localhost:8080` |
| Physical device (e.g. iPhone 14) | `http://<Mac-LAN-IP>:8080` |

Find Mac LAN IP: `ipconfig getifaddr en0`

- Clients talk to the **gateway** only — not auth `:8081` or wallet `:8082` directly
- Document URL changes in `.env.development` / `.env.device` — never hardcode IPs in source
- Physical device `localhost` refers to the phone itself, not your Mac

## Xcode Workflow

- Run `pod install` in `mobile/ios/` before first build and after native dependency changes
- Always open `.xcworkspace`, never `.xcodeproj` alone
- Run Metro bundler (`cd mobile && npm start`) before Xcode Run
- iPhone 14 is the minimum test target; verify on both simulator and physical device

## Backend Prerequisites

Before mobile testing, ensure backend is running:

```bash
cd backend && docker compose up -d
cd auth-service && mvn spring-boot:run
# also: wallet-service, api-gateway
```

API Gateway must be reachable on port **8080**.

## App Transport Security (Dev)

Keep `NSAllowsLocalNetworking` in `Info.plist` for local HTTP during development. Remove or restrict for production builds.

```xml
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsLocalNetworking</key>
  <true/>
</dict>
```

## Physical Device Checklist

1. Connect iPhone via USB (or Wi-Fi debugging)
2. Enable Developer Mode (Settings → Privacy & Security)
3. Trust developer certificate on first launch
4. Mac and iPhone on the **same Wi-Fi network**
5. Mac firewall allows inbound connections on port **8080** (gateway)

## Related Rules

- Shared RN conventions: `mobile.md`
- Android setup: `mobile-android-dev.md`
