# Home Assistant Configuration for a Solar Smart Home

This is my https://www.home-assistant.io/ configuration tree
for a smart home with solar panels, KNX, a Dimplex heat pump
and a go-e wallbox.

## Dimplex Heat Pump

The heatpump is a [Dimplex LIK8 TES](https://dimplex.de/dimplex/waermepumpen/li-lik/lik8tes),
with the [NWPM Touch](https://dimplex.atlassian.net/wiki/spaces/DW/pages/3021504513/Modbus+TCP+Anbindung) 
module installed to connect it to Modbus TCP. The `NWPM Touch` is a re-branded
[Carel pCOWeb](https://www.carel.com/product/pcoweb-card) card.

[The Dimplex Wiki](https://dimplex.atlassian.net/wiki/spaces/DW/pages/3021504513/Modbus+TCP+Anbindung)
has lots of useful information, incl. a complete list of the registers and coils available
through Modbus TCP.

> [!NOTE]  
> This repository assumes firmware `L20.3` for the heat pump (WPM) and version `A2.1.5 - B2.1.5` for pCOWeb.
> Firmware versions of the heat pump and pCOWeb differ in their address mappings, so YMMV.

`modbus.yaml` configures [HA's modbus integration](https://www.home-assistant.io/integrations/modbus/)
to fetch the values from the pCOWeb card via the usual TCP port 502. To limit the load on the card
the configuration only fetches values every few minutes and uses the `virtual_count` feature to
perform bulk fetches for the two register types ("analog" and "integeger") and coils the card supports.
This yields a lot of entities like `sensor.heatpump_float`, `sensor.heatpump_integer_23` and `sensor.heatpump_coil_10` (the first value has no numeric suffix, the later ones just get consecutive counts).

> [!TIP]
> You need to a value for `heatpump_ip` in `secrets.yaml`.

`template.yaml` then maps these values to human-readable entities like "heatpump_warm_water_temp" and
assigns (German) (friendly) names to them.

> [!TIP]
> If you need to debug the modbus communication, enable the `logger:` section in `configuration.yaml`