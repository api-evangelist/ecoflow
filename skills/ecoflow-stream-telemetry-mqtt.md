---
name: Stream EcoFlow device telemetry over MQTT
description: Obtain the MQTT certificate and subscribe to live quota and status updates for EcoFlow devices in real time.
api: openapi/ecoflow-iot-openapi.yml
operations: [getMqttCertification]
---

# Stream EcoFlow device telemetry over MQTT

Use this for continuous, low-latency device telemetry instead of polling
getAllQuota.

## Steps
1. Call `getMqttCertification` (GET /iot-open/sign/certification) with the
   standard signed headers. The response `data` gives `url` (broker host, e.g.
   `mqtt.ecoflow.com`), `port` (`8883`), `protocol` (`mqtts`),
   `certificateAccount` and `certificatePassword`.
2. Connect to the broker over TLS (mqtts, port 8883) using certificateAccount /
   certificatePassword. Use mqtt-e.ecoflow.com for Europe accounts.
3. Subscribe to `/open/{certificateAccount}/{sn}/quota` for live property updates
   and `/open/{certificateAccount}/{sn}/status` for online/offline changes.
4. To command a device, publish to `/open/{certificateAccount}/{sn}/set` and read
   acknowledgements from `/open/{certificateAccount}/{sn}/set_reply`.

## Rules
- The certificate is per-account; one connection streams all bound devices via
  their per-`sn` topics.
- Payloads are JSON; quota updates carry a partial `param` map — merge deltas
  into your last-known state rather than replacing it.
- See asyncapi/ecoflow-mqtt-asyncapi.yml for the full channel/message contract.
