---
name: Read EcoFlow device status
description: Discover the EcoFlow devices on an account and read a device's live battery, input/output and switch state via the IoT Open Platform HTTP API.
api: openapi/ecoflow-iot-openapi.yml
operations: [getDeviceList, getAllQuota]
---

# Read EcoFlow device status

Use this to answer "what EcoFlow devices do I have and what is their current state".

## Auth
Every request needs four headers: `accessKey`, `nonce` (fresh random per request),
`timestamp` (ms), and `sign`. `sign` is the hex HMAC-SHA256, keyed by your
`secretKey`, over the ASCII-sorted request parameters concatenated with
`accessKey`, `nonce` and `timestamp`. Use `api.ecoflow.com` for global/US
accounts and `api-e.ecoflow.com` for Europe accounts — accounts are region-locked.

## Steps
1. Call `getDeviceList` (GET /iot-open/sign/device/list) to get every bound
   device's `sn`, `productName` and `online` flag.
2. Pick the target device `sn`.
3. Call `getAllQuota` (GET /iot-open/sign/device/quota/all?sn=<sn>) to get the
   full flat map of quota keys (e.g. `pd.soc` = battery %, `inv.outputWatts`,
   `inv.inputWatts`, temperatures, switch states). Quota keys vary by product.

## Rules
- Check the envelope `code`: `"0"` is success; anything else is an error with
  a `message` (see errors/ecoflow-problem-types.yml).
- A device with `online: 0` will return stale/empty quota — report it as offline.
- These are read-only operations; no idempotency concerns.
