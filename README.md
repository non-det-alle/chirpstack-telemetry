# ChirpStack Telemetry

Configurable telemetry setup for monitoring LoRaWAN traffic logged by a ChirpStack network server into its Redis database.

Exported data corresponds to the `json` structure for LoRaWAN [frames](https://www.chirpstack.io/docs/chirpstack/features/frame-logging.html) produced by ChirpStack under *Tenants/\<your-tenant>/Applications/\<your-application>/Devices/\<your-device>/LoRaWAN Frames*.

> Note: One InfluxDB entry is created for each gateway *reception*, meaning that multiple entries can be produced per unique packet. This is useful to be able to produce gateway-wise performance metrics. In queries, single packets can be obtained by de-duplicating unique values of `frame_log_id` (or also by grouping by `dev_eui` and `phy_payload.payload.fhdr.devaddr` and de-duplicating by `phy_payload.payload.fhdr.f_cnt`, see examples in the provided Grafana [dashboard](./configuration/grafana/dashboards/example.json) on `localhost:3000`, more info below).

The system is container-based and configured to use Docker Compose. Tested on ChirpStack v4.6.0.

## Architecture

```text
                                 _________________________________________________
       ____________             /               CHIRPSTACK TELEMETRY              \ 
      |            |            |   ___________       __________       _________  |
      |            |   Redis    |  |           |     |          |     |         | |
      | ChirpStack |============|=>| Telemetry |====>| InfluxDB |====>| Grafana | |
      |            |   frame    |  |  ingester |     |__________|     |_________| |
      |____________|   stream   |  |___________|                                  |
                                |                                                 |
                                \_________________________________________________/
```

## Get started

- Make sure your Redis instance has its port reachable, usually its `6379`. This means that if you used Docker Compose to run your ChirpStack deployment, you have to declare the port under the `redis` service. Encrypted communication is not supported, so you should install this component on the same host of your ChirpStack server.
- Check the `configuration/telemetry-ingester/config.toml` file and edit `chirpstack.endpoint` and `mosquitto.endpoint` if they differ from you `localhost` ip.
- Run `docker compose up` and you should be able to view an example dashboard on `localhost:3000` (default login `admin`/`admin`).

### More details

Read [telemetry-ingester/README.md](telemetry-ingester/README.md).
