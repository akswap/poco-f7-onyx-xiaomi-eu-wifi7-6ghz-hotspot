# POCO F7 (onyx) Xiaomi.eu Wi-Fi 7 + 6 GHz

Exact-build Magisk module pack for **POCO F7 (onyx)** running:

- **ROM:** Xiaomi.eu
- **Build:** `ONYX_OS3.0.305.0.WOLCNXM`
- **Android:** 16
- **Wi-Fi chipset:** Qualcomm WCN7750

> [!CAUTION]
> This is **not a universal module**. Do not install it on another device, ROM, Xiaomi.eu build, or vendor firmware. Keep a working recovery/ADB setup and a full backup before flashing.

## What works

Verified on the target build:

- Wi-Fi 7 / IEEE 802.11be client mode
- Wi-Fi 7 hotspot on 5 GHz and 6 GHz
- 6 GHz scanning and connection
- 320 MHz 6 GHz hotspot
- Qualcomm WCN7750 support
- Automatic 6 GHz hotspot TX-power recovery
- Camera and hotspot working together with the included build-matched components

Real-device verification showed:

- `wifiStandard=8` / 802.11be
- 6 GHz hotspot on channel 133 (6615 MHz)
- 320 MHz channel width
- 24 dBm reported after TX-power recovery
- Intel BE200 client connected as 802.11be

## Downloads and installation order

Download both ZIP files from the latest GitHub release.

1. Flash **`Magisk-6GHz-WiFi7-Hotspot-UI-v11.3-Hybrid-ACS-No-BW-Miui-EU.zip`** in Magisk.
2. Reboot.
3. Flash **`onyx_wcn7750_wifi7_standalone_fix_v32y_magisk.zip`** in Magisk.
4. Reboot again.
5. Turn Wi-Fi and hotspot off/on once, then test.

The V32Y companion expects the UI module ID `magisk_6ghz_ui_v2`; keep both modules enabled.

## Package checksums

| File | Size | SHA-256 |
|---|---:|---|
| `Magisk-6GHz-WiFi7-Hotspot-UI-v11.3-Hybrid-ACS-No-BW-Miui-EU.zip` | 93,317,345 bytes | `eca57d452a82218127fb6e5fefdea36a1ea6b716efe4f6658d49642740f1570d` |
| `onyx_wcn7750_wifi7_standalone_fix_v32y_magisk.zip` | 10,423,054 bytes | `2365ab869a8d5c271a51047525aca190def5fce698cf15e102d289fa3843511e` |

Verify on Windows:

```powershell
certutil -hashfile "Magisk-6GHz-WiFi7-Hotspot-UI-v11.3-Hybrid-ACS-No-BW-Miui-EU.zip" SHA256
certutil -hashfile "onyx_wcn7750_wifi7_standalone_fix_v32y_magisk.zip" SHA256
```

## Verification

Run as root:

```sh
cmd overlay lookup --user 0 com.android.wifi.resources \
  com.android.wifi.resources:bool/config_wifiSoftapIeee80211beSupported

cmd overlay lookup --user 0 com.android.wifi.resources \
  com.android.wifi.resources:bool/config_wifi11beSupportOverride

cmd overlay lookup --user 0 com.android.wifi.resources \
  com.android.wifi.resources:bool/config_wifi6ghzSupport

cmd overlay lookup --user 0 com.android.wifi.resources \
  com.android.wifi.resources:bool/config_wifiSoftap6ghzSupported

dumpsys wifi | grep -i -E "Ieee80211beEnabled|mCurrentSoftApInfoMap"
iw dev wlan1 info
```

Expected hotspot indicators include:

```text
wifiStandard=8
channel 133 (6615 MHz), width: 320 MHz
txpower 24.00 dBm
```

The selected 6 GHz channel can vary with ACS, country configuration, congestion, and firmware policy.

## Troubleshooting

### Hotspot starts at 8 dBm

Check:

```sh
iw dev wlan1 info
```

The included module should restore automatic power after hotspot startup. For a one-session test:

```sh
iw dev wlan1 set txpower auto
```

Do not force a fixed transmit power. Regulatory and SAR limits still apply.

### Camera closes after a few seconds

This pack is for the exact Xiaomi.eu build above. Do not mix hostapd or Wi-Fi shared libraries from crDroid, Infinity-X, another Xiaomi.eu release, or another vendor build. If the camera fails, disable both modules and reboot.

### 6 GHz is not visible

Confirm WPA3 is used, set an allowed country/channel on the router, trigger a new scan, and check:

```sh
cmd wifi get-country-code
iw reg get
cmd wifi start-scan
sleep 12
cmd wifi list-scan-results | awk 'NR==1 || ($2 >= 5925 && $2 <= 7125)'
```

## Uninstall / rollback

1. Disable or remove **both** modules in Magisk.
2. Reboot.
3. If Android cannot boot, use recovery or ADB to remove their folders under `/data/adb/modules/`, then reboot.

## Important notes

- 6 GHz availability and transmit power are controlled by local regulations, firmware, SAR policy, and the access point.
- Reported PHY link speed is not the same as real TCP/UDP throughput.
- Binary components remain property of their respective owners. This repository only documents and packages the tested device-specific modification.
- Flashing system modifications is at your own risk.
