# Ubiquiti UniFi Access — the published developer API

Written 2026-10-13 for TinkerCommons. Source of truth: Ubiquiti's own document **"UniFi Access API
Documentation"**, 194 pages, served from Ubiquiti's asset host at
https://assets.identity.ui.com/unifi-access/api_reference.pdf (downloaded and read in full; change log
runs to Access v4.2.16). Ubiquiti says the same PDF is downloadable inside the Access app under
**Access > Settings > General > Advanced > API Token**. Secondary sources used only to confirm behaviour:
the `unifi-access` Python package (MIT, v0.1.7, June 2026), the `hjdhjd/unifi-access` TypeScript library,
the Home Assistant `hass-unifi-access` integration, and the openHAB binding.

Everything below is from the Ubiquiti PDF unless marked **[community]** or **[inference]**.

## 1. The one-paragraph version

UniFi Access exposes a local REST API on the console at `https://<console-ip>:12445/api/v1/developer/`,
authenticated with a bearer token you mint in the Access app with chosen permission scopes. It covers
users, visitors, user groups, access policies and schedules, NFC cards, PINs, Touch Pass, doors and door
groups, remote unlock, lock rules, emergency mode, devices, system logs, and **outbound webhooks signed
with HMAC-SHA256**, plus a WebSocket for doorbell notifications. Requires Access **1.9.1 or later**
(some endpoints need 2.2.10, 3.3.10, or 4.2.16). **The API disappears if the site is upgraded to
UniFi Identity Enterprise.** The console's TLS certificate on 12445 is self-signed; you can upload your
own. There is no documented rate limit beyond the existence of a 429 response.

## 2. Setup facts

| Item | Value |
|---|---|
| Token | Access > Settings > General > Advanced > API Token > Create New: name, validity period, permission scopes. Shown once. |
| Host | the console's LAN IP or a DNS name you map to it; **port 12445**, HTTPS only |
| Header | `Authorization: Bearer <token>` |
| Response envelope | `{"code": "SUCCESS" \| "<CODE_...>", "msg": "...", "data": ...}` |
| HTTP codes | 200, 400, 401, 402, 403, 429, 500/502/503/504 |
| TLS | self-signed by default; `POST /api_server/certificates` uploads your own, `DELETE` removes it |
| Minimum version | Access 1.9.1 (token API); `user_email` needs 1.22.16; webhooks need 2.2.10; lock rules 1.24.6; WebSocket notifications 1.20.11; third-party card import and the newer webhook events 3.3.10 |
| Hard limit | **"The API is not available after upgrading to Identity Enterprise."** Error code `CODE_OTHERS_UID_ADOPTED_NOT_SUPPORTED`. |
| Community caveat | Home Assistant users report plain UniFi Identity (free tier) sometimes still works, Enterprise does not. **[community]** |

Permission scopes seen in the document (chosen per token): `view:user`, `edit:user`, `view:visitor`,
`edit:visitor`, `view:policy`, `edit:policy`, `view:credential`, `edit:credential`, `view:space`,
`edit:space`, `view:device`, `edit:device`, `view:system_log`, `view:webhook`, `edit:webhook`,
`edit:api_server`.

## 3. Endpoint inventory (98 documented endpoints)

All paths are under `/api/v1/developer`. Scope in brackets.

**Users** [view:user / edit:user]
- `POST /users` register · `PUT /users/:id` update · `GET /users/:id` · `GET /users` (paged) · `DELETE /users/:id` · `GET /users/search`
- `PUT /users/:id/access_policies` assign policies · `GET /users/:id/access_policies`
- `PUT /users/:id/nfc_cards` assign card · `PUT /users/:id/nfc_cards/delete` unassign
- `PUT /users/:id/pin_codes` assign PIN · `DELETE /users/:id/pin_codes`
- `PUT /users/:user_id/touch_passes/:touch_pass_id` · `DELETE …` · `PUT /users/touch_passes/assign` (batch)
- `PUT /users/:id/license_plates` · `DELETE /users/:user_id/license_plates/:license_plate_id`
- `POST /users/:id/avatar`
- User schema: `id, first_name, last_name, full_name, alias, user_email, email_status, phone, employee_number, onboard_time, nfc_cards[{id,token}], license_plates[], pin_code{token}, access_policy_ids[], access_policies[], status {ACTIVE, PENDING, DEACTIVATED}, touch_pass[]`

**User groups** [view:user / edit:user]
- `POST /user_groups` · `GET /user_groups` · `GET /user_groups/:id` · `PUT /user_groups/:id` · `DELETE /user_groups/:id`
- `POST /user_groups/:id/users` add · `POST /user_groups/:id/users/delete` remove · `GET /user_groups/:id/users` · `GET /user_groups/:id/users/all`
- `PUT /user_groups/:id/access_policies` · `GET /user_groups/:id/access_policies`

**Visitors** [view:visitor / edit:visitor]
- `POST /visitors` · `GET /visitors/:id` · `GET /visitors` · `PUT /visitors/:id` · `DELETE /visitors/:id`
- NFC, PIN, QR code and licence-plate assign/unassign, same shape as users, plus `GET /credentials/qr_codes/download/:visitor_id`

**Access policies, schedules, holiday groups** [view:policy / edit:policy]
- `POST /access_policies` (name, resources[{type: door|door_group, id}], schedule_id) · `PUT /:id` · `DELETE /:id` · `GET /:id` · `GET /access_policies`
- `POST /access_policies/schedules` (name, week_schedule Sunday–Saturday, holiday_group) · `PUT`, `GET`, `GET all`, `DELETE`
- `POST /access_policies/holiday_groups` · `PUT`, `DELETE`, `GET`, `GET all`

**Credentials** [view:credential / edit:credential]
- `POST /credentials/pin_codes` generate a PIN
- `POST /credentials/nfc_cards/sessions` wake a reader and start enrolment (`device_id`, `reset_ua_card`) · `GET /credentials/nfc_cards/sessions/:id` poll for the card token · `DELETE …/sessions/:id`
- `GET /credentials/nfc_cards/tokens` · `GET /credentials/nfc_cards/tokens/:token` · `PUT …/:token` · `DELETE …/:token`
- `POST /credentials/nfc_cards/import` CSV of third-party card IDs, uppercase hex, with unique alias (3.3.10+). Error text says unsupported card types must "use a UA Card".
- Touch Pass: `GET /credentials/touch_passes`, `/search`, `/assignable`, `GET /:id`, `PUT /:id`, purchase endpoint

**Space: doors and door groups** [view:space / edit:space]
- `GET /door_groups/topology` · `POST /door_groups` · `GET /door_groups` · `GET /:id` · `PUT /:id` · `DELETE /:id`
- `GET /doors` · `GET /doors/:id` — fields `id, name, full_name, floor_id, type, is_bind_hub, door_lock_relay_status (lock|unlock), door_position_status (open|close)`
- `PUT /doors/:id/unlock` remote unlock; optional `actor_id` + `actor_name` (both or neither) to label the actor in logs and webhooks, and `extra` passthrough object echoed in the webhook. Query `reader_id`, `entry_method` (in|out), `control_cmd` (open|close|stop, UA Hub Gate only).
- `PUT /doors/:id/lock_rule` temporary rule (keep unlocked, keep locked, custom minutes, reset) · `GET /doors/:id/lock_rule`
- `PUT /doors/settings/emergency` lockdown / evacuation · `GET` status

**Devices** [view:device / edit:device]
- `GET /devices` · `GET /devices/:device_id/settings` access-method settings (NFC, PIN, face, mobile, QR…) · `PUT …/settings` · `POST /devices/:device_id/doorbell` trigger intercom

**System log** [view:system_log]
- `POST /system/logs?page_num&page_size` with body `topic` ∈ {critical, door_openings, updates, device_events, admin_activity, visitor}, optional `since`, `until`, `actor_id`
- `POST /system/logs/export` · resource lookups for log rendering
- Each entry: `event {type, display_message, result, published, tag}`, `actor {id, type, display_name}`, `authentication {credential_provider, issuer}`, `target[] {type, id, display_name}`

**UniFi Identity** — invitations and resource assignment; not relevant to TinkerCommons.

**Notifications**
- `GET /devices/notifications` **WebSocket** (`wss://host:12445/...`, bearer header) — doorbell events `access.remote_view`, `access.remote_view.change` with reason codes 105–108, 400.
- Webhooks: `GET /webhooks/endpoints` · `POST /webhooks/endpoints` (name, endpoint URL, events[], optional custom headers) → returns the endpoint's **secret** · `PUT /:id` · `DELETE /:id`

## 4. Webhooks, exactly as Ubiquiti documents them

Events you can subscribe to:

| Event | Fires when |
|---|---|
| `access.door.unlock` | **any** door unlock: NFC, PIN, mobile, remote, call. Payload carries `actor {id, name, type}`, `object {authentication_type, policy_id, policy_name, reader_id, result "Access Granted"}`, `location`, `device` |
| `access.device.dps_status` | door position sensor changed |
| `access.device.emergency_status` | lockdown / evacuation changed |
| `access.doorbell.incoming`, `.completed`, `.incoming.REN` | intercom and request-to-enter |
| `access.unlock_schedule.activate` / `.deactivate` | 3.3.10+ |
| `access.temporary_unlock.start` / `.end` | 3.3.10+ |
| `access.visitor.status.changed` | 3.3.10+ |
| `access.data.device.remote_unlock` | shown in examples; a remote-unlock variant |

Delivery: HTTPS POST of a JSON object `{event, event_object_id, receiver_id, save_to_history, data}`.
Your endpoint must answer within **5 seconds** (Ubiquiti: "handle asynchronous processing logic, as
there is currently a 5-second timeout"). Reply `{"code":"SUCCESS"}`.

Verification: header **`Signature: t=<unix seconds>,v1=<hex>`** where
`v1 = HMAC-SHA256(secret, "<t>" + "." + raw_body)`. Compare with a constant-time equality. Ubiquiti
ships reference verifiers in Go and Rust in the PDF. Python equivalent:

```python
import hmac, hashlib
def verify(sig_header: str, body: bytes, secret: str, max_skew_s: int = 300) -> bool:
    parts = dict(p.split("=", 1) for p in sig_header.split(","))
    t, v1 = parts["t"], bytes.fromhex(parts["v1"])
    expected = hmac.new(secret.encode(), f"{t}.".encode() + body, hashlib.sha256).digest()
    return hmac.compare_digest(expected, v1)   # add a timestamp-skew check for replay protection
```

The document does not say whether failed deliveries are retried. **[inference]** Assume no retry and
treat the webhook as a hint; the authoritative record is what TinkerCommons decides when a credential is
checked, not what Ubiquiti reports afterwards.

## 5. What this means for TinkerCommons

Decision context (Scott, 2026-10-13): door hardware support is limited to two or three providers,
starting with Ubiquiti because LPM runs it; TinkerCommons does **not** ingest Ubiquiti's logs as its
record. It keeps its own access events.

**How the adapter should work [inference, for the spec]**

- **Direction of truth:** TinkerCommons is the system of record for who may enter. UniFi Access is the
  thing on the wall. The adapter pushes state *to* UniFi: create or deactivate the user, assign or remove
  the NFC card or PIN, put the user in the right access policy. It never reads UniFi's log as the ledger.
- **Mapping:** one TinkerCommons `Person` ↔ one UniFi `user` (store the UniFi `id`); one `Credential`
  (fob/card/PIN) ↔ one UniFi NFC token or PIN; one `AccessDevice` of kind `door` ↔ one UniFi `door` id;
  one membership-state × badge-set ↔ one UniFi `access_policy` (built from `door` or `door_group`
  resources plus a `schedule`). Overdue or lapsed → `status DEACTIVATED` or policies removed; both are
  one API call.
- **Enrolment:** staff taps "enrol fob" in TinkerCommons → adapter calls `POST /credentials/nfc_cards/sessions`
  with the reader's `device_id` → member holds the fob to the reader → adapter polls the session, gets the
  token, binds it to the user and records the credential locally. Third-party fobs can be bulk-imported by
  hex ID (3.3.10+), which matters if LPM already has fobs issued.
- **Events:** subscribe the adapter's HTTPS endpoint to `access.door.unlock` and `access.device.dps_status`
  only. Verify the HMAC. Use the event to stamp an `AccessEvent` row with `source=unifi` and to show
  "last seen" on the member, not to decide anything. If the webhook is missed, nothing breaks.
- **Remote unlock:** `PUT /doors/:id/unlock` with `actor_id`/`actor_name` set to the TinkerCommons staff
  user, so UniFi's own log shows who did it. Use `extra` to pass the TinkerCommons event id for
  reconciliation.
- **Tool interlocks:** UniFi Access has no concept of a machine. It can drive a relay on a UA Hub as a
  "door", so a laser's power contactor can be a UniFi door with its own policy. That is an acceptable v0.5
  path for a few machines; the ESP32 reference firmware handles the rest.
- **Network placement:** the API is LAN-only on port 12445. A hosted TinkerCommons cannot reach it without
  a tunnel or an on-site agent. Self-hosted at LPM, it is just another machine on the LAN. Webhooks go the
  other way and need the console to reach the TinkerCommons endpoint over HTTPS with a certificate it
  trusts, or an HTTP endpoint on the LAN.
- **Do not depend on:** Identity Enterprise sites (API gone), the undocumented internal API the community
  libraries use for websocket door events, or door *locking* via API (Home Assistant notes it is not
  supported; only unlock and lock rules are).

**Other providers to cover later** (from `04-doors-payments-migration-hosting.md`): Kisi (public API,
signed webhooks, used by coworking platforms) and one of Verkada or Genea. Paxton, HID and Salto are
closed to independent integrators and are out.

## 6. Open questions to verify on LPM's actual console before v0.5

1. Access version on the console, and whether it is on plain UniFi Identity or Identity Enterprise.
2. Whether LPM's fobs are UA cards or third-party (decides enrolment path vs CSV import).
3. Whether the console can reach a LAN HTTPS endpoint (certificate handling for the webhook receiver).
4. Rate limiting in practice: the document names a 429 but no numbers.
5. Whether `access.door.unlock` fires for denied attempts (the example shows "Access Granted"; the
   system-log topic `door_openings` is the fallback for denials).
