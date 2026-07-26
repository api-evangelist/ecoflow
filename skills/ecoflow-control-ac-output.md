---
name: Turn an EcoFlow device AC output on or off
description: Send a device function command to switch a bound EcoFlow device's AC output using the IoT Open Platform HTTP API.
api: openapi/ecoflow-iot-openapi.yml
operations: [getDeviceList, setQuota]
---

# Turn an EcoFlow device AC output on or off

Use this to change a device function (this example: AC output). The exact
`moduleType`/`operateType`/`params` shape is product-specific — confirm it
against the device's product docs before sending.

## Auth
Same signed-header scheme as all IoT Open Platform calls: `accessKey`, `nonce`,
`timestamp`, `sign` (HMAC-SHA256 over sorted params + accessKey/nonce/timestamp,
keyed by secretKey). Region-locked host (api.ecoflow.com or api-e.ecoflow.com).

## Steps
1. Call `getDeviceList` to confirm the target `sn` is bound and `online: 1`.
2. Call `setQuota` (PUT /iot-open/sign/device/quota) with a body naming the
   device `sn`, the product's `moduleType`, the `operateType` for AC output, and
   `params` carrying the desired state, e.g. `{ "enabled": 1 }` to enable.

## Rules
- This is a state-changing command. It is NOT documented as idempotent — do not
  blindly retry a timed-out command; re-read state with getAllQuota first.
- Verify the effect by re-reading the relevant quota key after the command.
- Non-`"0"` envelope `code` means the command was rejected; surface `message`.
- Commands can equivalently be published on the MQTT `/open/{account}/{sn}/set`
  topic (see skills/ecoflow-stream-telemetry-mqtt.md).
