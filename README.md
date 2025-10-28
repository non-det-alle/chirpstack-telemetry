# ChirpStack Telemetry

Configurable telemetry setup for monitoring LoRaWAN traffic exposed by a ChirpStack network server.

Exported data corresponds to the `json` structure for [LoRaWAN frames](https://www.chirpstack.io/docs/chirpstack/features/frame-logging.html) produced by ChirpStack under *Tenants/\<your-tenant>/Applications/\<your-application>/Devices/\<your-device>/LoRaWAN Frames*.

> Note: One entry is created for each gateway reception, meaning that multiple entries may be produced per each packet. This is useful to be able to produce per-gateway performance metrics. Packets can be deduplicated in queries by grouping by `dev_eui` and `phy_payload.payload.fhdr.devaddr` (see examples in the provided [dashboard](./configuration/grafana/dashboards/example.json)).

The system is container-based and configured to use Docker Compose. Tested on ChirpStack v4.6.0.

## Architecture

```text
        _________________________
       |  (i) MQTT device        |
       |      discovery    ______|__________________________________________
 ______|_____             /      |        CHIRPSTACK TELEMETRY              \ 
|            |            |   ___v_______       __________       _________  |
|            | (ii) gRPC  |  |           |     |          |     |         | |
| ChirpStack |============|=>| Telemetry |====>| InfluxDB |====>| Grafana | |
|            |   frame    |  |  ingester |     |__________|     |_________| |
|____________|  streams   |  |___________|                                  |
                          |                                                 |
                          \_________________________________________________/

(i)  Subscription to ChirpStack's MQTT broker, monitored to be able to discover connected devices.
(ii) Multi-connection to ChirpStack's internal gRPC API streams to receive frame logs of discovered devices.

```

## Get started

- Place a ChirpStack API token in a new file named `.end.chirpstack-api-token` in the root directory of this repository.
- In the `configuration/telemetry-ingester/config.toml` file, configure `chirpstack.endpoint` and `mosquitto.endpoint`.
- Run `docker compose up` and you should be able to view an example dashboard on `localhost:3000` (default login `admin`/`admin`).
