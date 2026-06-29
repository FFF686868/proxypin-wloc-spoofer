<h1 align="center">ProxyPin WLOC Response Rewriter</h1>

<p align="center">
  <img src="assets/cover.jpg" alt="ProxyPin WLOC Response Rewriter cover">
</p>

<p align="center">
  <a href="README.zh-CN.md">中文文档</a> · English
</p>

<p align="center">
  <a href="https://github.com/FFF686868/proxypin-wloc-spoofer/stargazers"><img alt="GitHub stars" src="https://img.shields.io/github/stars/FFF686868/proxypin-wloc-spoofer?style=for-the-badge&logo=github&label=STARS&color=0891b2"></a>
  <a href="https://github.com/FFF686868/proxypin-wloc-spoofer/network/members"><img alt="GitHub forks" src="https://img.shields.io/github/forks/FFF686868/proxypin-wloc-spoofer?style=for-the-badge&logo=github&label=FORKS&color=0284c7"></a>
  <a href="LICENSE"><img alt="License" src="https://img.shields.io/badge/LICENSE-MIT-16a34a?style=for-the-badge"></a>
  <a href="https://github.com/wanghongenpin/proxypin"><img alt="ProxyPin script" src="https://img.shields.io/badge/PROXYPIN-SCRIPT-334155?style=for-the-badge"></a>
  <a href="https://linux.do"><img alt="Linux.do" src="https://img.shields.io/badge/LINUX.DO-COMMUNITY-334155?style=for-the-badge"></a>
</p>

ProxyPin script for authorized iOS location testing. It intercepts Apple WLOC
responses for `gs-loc-cn.apple.com/clls/wloc` and `gs-loc.apple.com/clls/wloc`,
then rewrites latitude/longitude values inside the original binary response.

This is not an Apple CVE, not a remote exploit, and not an iOS permission
bypass. It only works on a device where the user has explicitly installed and
trusted the ProxyPin CA certificate and routes traffic through ProxyPin.

## Disclaimer

This project is provided for authorized testing, research, and QA use only.

By using this code, you agree that:

- You will only test devices, apps, accounts, and networks you own or have
  explicit permission to test.
- You are responsible for complying with local laws, platform rules, and
  service terms.
- The authors are not responsible for misuse, service abuse, account bans,
  data loss, legal consequences, or any other damage caused by this code.
- This project is provided "as is", without warranty of any kind.

Do not use this project to deceive services, bypass rules, falsify production
location data, or interfere with devices or networks without permission.

## What It Does

- Preserves the original binary WLOC request body.
- Handles gzip-compressed Apple WLOC responses.
- Rewrites location fields in the original protobuf-like WLOC payload.
- Returns debug headers such as `X-WLOC-ProxyPin`, `X-WLOC-Patched-Locations`,
  and `X-WLOC-Error`.

## Requirements

This repository is only a JavaScript script for ProxyPin. It is not a standalone
app, proxy server, or iOS profile.

You need:

- ProxyPin with script support.
- An iOS test device.
- ProxyPin CA installed and trusted on that device.
- HTTPS capture enabled in ProxyPin.

## Files

- `proxypin_wloc_compat_v2.js`: ProxyPin JavaScript script.
- `proxypin-scripts.json`: ProxyPin import file with the script and URL rules.
- `leaflet-test.html`: Standalone Leaflet test page for local browser checks.
- `LICENSE`: MIT license for this project.
- `NOTICE`: Third-party notice for pako.

## Usage

Use the script locally in ProxyPin. Do not rely on a remote script URL.

Chinese step-by-step tutorial with screenshots: [ProxyPin WLOC tutorial](docs/zh-CN/tutorial.md).

### Import JSON

1. In ProxyPin, open the script management page.
2. Import `proxypin-scripts.json`.
3. Make sure the imported script is enabled.
4. Make sure HTTPS capture is enabled and the ProxyPin CA is trusted.
5. Open `https://www.baidu.com/` through the proxied device.
6. Pick a location on the Leaflet map and tap `Save`.
7. Trigger iOS location on the test device.

<p align="center">
  <img src="docs/assets/zh-CN/tutorial/wloc-location-picker.jpg" alt="WLOC Location Picker" width="420">
</p>

The import file already matches these URLs:

   - `www.baidu.com/*`
   - `gs-loc-cn.apple.com/clls/wloc`
   - `gs-loc.apple.com/clls/wloc`

### Manual Script Setup

If you do not want to import JSON:

1. Open `proxypin_wloc_compat_v2.js`.
2. Copy the full script content.
3. In ProxyPin, create a new local script.
4. Match the URLs listed above.
5. Paste the script into ProxyPin.

The script is working when the intercepted WLOC response contains headers like:

```text
X-WLOC-ProxyPin: v5.4.2
X-WLOC-Origin-Status: 200
X-WLOC-Patched-Locations: 1
```

If `X-WLOC-Origin-Status` is `400`, the Apple server rejected the original
request before the response could be rewritten.

## Change Coordinates

The recommended flow is to open `https://www.baidu.com/`, pick a location on
the Leaflet map, and tap `Save`. The selected coordinates are stored in
ProxyPin `context.session` and reused by later WLOC responses.

If no location has been saved yet, the script falls back to these constants:

```js
var TARGET_LONGITUDE = 113.94114;
var TARGET_LATITUDE = 22.544577;
var TARGET_ACCURACY = 25;
```

## Scope

This project is intended for:

- Authorized device testing.
- QA workflows that need repeatable iOS network-location behavior.
- Security research in a controlled local proxy environment.

## Notes

This modifies Apple network-location WLOC responses. It does not directly modify
GPS hardware readings, and iOS may prefer real GPS when GPS signal is strong.

## References

- ProxyPin project: <https://github.com/wanghongenpin/proxypin>
- ProxyPin script documentation: <https://github.com/wanghongenpin/proxypin/wiki/Script>
- pako gzip library: <https://github.com/nodeca/pako>
