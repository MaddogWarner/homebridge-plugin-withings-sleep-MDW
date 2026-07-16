> [!WARNING]
> **This project is deprecated.**
> It is no longer maintained and will not receive further updates, bug fixes, or compatibility changes. It remains installable for now, but you should plan to migrate away — the repository may be archived and support removed entirely in a future update.

<p align="center">
  <img src="https://github.com/homebridge/branding/raw/latest/logos/homebridge-wordmark-logo-vertical.png" width="150" alt="Homebridge">
</p>

<h1 align="center">Withings Sleep Homebridge Plugin</h1>

<p align="center">
  Expose your Withings Sleep Analyzer to Apple HomeKit as a Switch and Occupancy Sensor, controlled via HTTP webhooks.
</p>

---

## How it works

The plugin starts a small HTTP server inside Homebridge and registers two HomeKit accessories:

- **Switch** — a binary on/off toggle
- **Occupancy Sensor** — a presence detection sensor (occupied/unoccupied)

Both accessories are controlled by calling HTTP GET endpoints on the local server. When your Withings Sleep Analyzer detects that you've got into or out of bed, you trigger one of these endpoints (via a script, automation, or the Withings webhook system) and HomeKit updates accordingly.

```text
Withings API / automation
        │
        │  GET http://<homebridge-host>:<port>/withings/on
        ▼
  Homebridge plugin
        │
        ├─ Switch → ON
        └─ Occupancy Sensor → OCCUPIED
```

---

## Prerequisites

- [Homebridge](https://homebridge.io) **2.0 or later** (also compatible with 1.6+)
- **Node.js 22.12 or 24+**
- A Withings Sleep Analyzer (or any system that can call HTTP GET endpoints)

---

## Installation

### Via Homebridge Config UI X (recommended)

1. Open the Homebridge UI in your browser
2. Go to **Plugins** and search for `homebridge-plugin-withings-sleep-mdw`
3. Click **Install**
4. Restart Homebridge when prompted

### Via command line

```bash
npm install -g homebridge-plugin-withings-sleep-mdw
```

---

## Configuration

### Using Homebridge Config UI X

After installing the plugin, click **Settings** on the plugin card. The UI will present all available options with descriptions.

### Manual config.json

Add an entry to the `accessories` array in your Homebridge `config.json`:

```json
{
  "accessories": [
    {
      "accessory": "Withings Sleep",
      "name": "Withings Sleep",
      "port": 3000,
      "hosts": ["192.168.1.100"],
      "cert": "/path/to/cert.pem",
      "key": "/path/to/key.pem"
    }
  ]
}
```

### Configuration options

| Option     | Type    | Required | Default         | Description |
|------------|---------|----------|-----------------|-------------|
| `accessory`| string  | Yes      | —               | Must be `"Withings Sleep"` |
| `name`     | string  | Yes      | `"Withings Sleep"` | Display name in HomeKit |
| `port`     | integer | Yes      | `3000`          | Port the webhook server listens on |
| `hosts`    | array   | No       | `[]` (all)      | List of allowed source IP addresses or hostnames. Empty means all hosts are allowed. |
| `cert`     | string  | No       | —               | Absolute path to an SSL certificate file (PEM format). Required if `key` is set. |
| `key`      | string  | No       | —               | Absolute path to an SSL private key file (PEM format). Required if `cert` is set. |

> **Note:** `cert` and `key` must always be configured together. Setting one without the other will prevent the plugin from starting.

---

## Setting up Withings webhooks

The Withings API supports [notification webhooks](https://developer.withings.com/api-reference/#tag/notify) that fire when sleep events are recorded. When Withings sends a notification, you need something to translate it into a GET request to this plugin. Common approaches:

### Option 1: Script or cron job

Poll the Withings API on a schedule (e.g. every 5 minutes) and call the appropriate endpoint:

```bash
# Example: mark bed as occupied
curl http://192.168.1.x:3000/withings/on

# Example: mark bed as unoccupied
curl http://192.168.1.x:3000/withings/off
```

### Option 2: Home Assistant

Use a Home Assistant REST command or automation to call the endpoint when your Withings integration triggers a sleep state change.

### Option 3: IFTTT / Make (Integromat)

Use a webhook trigger from the Withings service and a Webhooks action to call this plugin's endpoint.

### Option 4: Direct integration

If you have a server or Raspberry Pi, use the [Withings API](https://developer.withings.com) to subscribe to sleep notifications and have your server relay them to this plugin.

---

## Available endpoints

The plugin exposes six GET endpoints. Replace `<host>` and `<port>` with your Homebridge server's IP address and the configured port (default `3000`).

| Endpoint                       | Effect |
|--------------------------------|--------|
| `GET /withings/on`             | Turn **both** the Switch and Occupancy Sensor **on** |
| `GET /withings/off`            | Turn **both** the Switch and Occupancy Sensor **off** |
| `GET /withings/switch/on`      | Turn the **Switch only** on |
| `GET /withings/switch/off`     | Turn the **Switch only** off |
| `GET /withings/occupancy/on`   | Turn the **Occupancy Sensor only** on (occupied) |
| `GET /withings/occupancy/off`  | Turn the **Occupancy Sensor only** off (unoccupied) |

All endpoints return HTTP 200 on success or HTTP 500 with a JSON error body on failure.

---

## SSL / TLS (optional)

To serve the webhook endpoint over HTTPS, provide paths to a certificate and private key:

```json
{
  "accessory": "Withings Sleep",
  "name": "Withings Sleep",
  "port": 3000,
  "cert": "/etc/ssl/certs/homebridge.pem",
  "key": "/etc/ssl/private/homebridge.key"
}
```

Both `cert` and `key` are required together — configuring one without the other will throw an error at startup. Self-signed certificates work fine for local use.

The endpoint URLs change to HTTPS once SSL is enabled:

```text
https://<homebridge-host>:3000/withings/on
```

---

## Host allowlist (security)

By default the plugin accepts requests from any IP address. To restrict access to specific hosts, provide a `hosts` array:

```json
{
  "hosts": ["192.168.1.100", "192.168.1.101"]
}
```

Requests from unlisted IPs will be rejected with an HTTP 500 error. This provides a basic layer of protection against unexpected sources triggering your HomeKit accessories.

> **Without a hosts list**, anyone on your network (or internet, if the port is exposed) can toggle your accessories. Use the hosts list or a firewall rule if this is a concern.

---

## Troubleshooting

### Plugin fails to start

Check the Homebridge log for the error message. Common causes:

- **Invalid port** — `port` must be an integer between 1 and 65535
- **SSL misconfiguration** — `cert` and `key` must both be set, and the paths must point to readable files
- **Port already in use** — another process is using the configured port; change `port` in config

### HomeKit accessories not updating

- Confirm the endpoint is reachable: `curl http://<homebridge-host>:<port>/withings/on`
- Check that your source IP is in the `hosts` allowlist (or that `hosts` is empty)
- Check the Homebridge log — the plugin logs each toggle at `info` level and errors at `error` level

### Accessories appear unresponsive in HomeKit

Restart Homebridge. If the plugin server failed to start (e.g. port conflict), the accessories will be registered in HomeKit but won't respond to webhook calls.

---

## Contributing

Issues and pull requests welcome at [github.com/MaddogWarner/homebridge-plugin-withings-sleep-MDW](https://github.com/MaddogWarner/homebridge-plugin-withings-sleep-MDW).

Maintained by [MadDogWarner](https://maddogwarner.com).

---

## Licence

[Apache 2.0](LICENSE) — maintained by [MadDogWarner](https://maddogwarner.com), forked from [dvcol/homebridge-plugin-withings-sleep](https://github.com/dvcol/homebridge-plugin-withings-sleep)
