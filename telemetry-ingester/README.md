# Telemetry ingester

This function reads uplink and downlink frame logs from the Redis database used by Chirpstack for temporary data storage ([see](https://www.chirpstack.io/docs/chirpstack/features/frame-logging.html)). Logs are then formatted and stored in a InfluxDB time series database for telemetry and monitoring purposes.

## Direct install

Normally this program should be run in combination with InfluxDB using the provided Docker compose setup. In case you want to run the telemetry ingester locally for testing or development purposes, execute the following steps from this directory:

- (optional but highly suggested) Create and activate a local Python virtual environment with `python -m venv .venv && source .venv/bin/activate`
- Install the provided lorawan-decoder package with `pip install lorawan-decoder/`
- Install the other external dependencies with `pip install -r requirements.txt`
- Edit the configurations in the `config.toml` file to adjust your Redis and InfluxDB endpoints

You should now be able to run the telemetry ingester with `python start.py -c .`
