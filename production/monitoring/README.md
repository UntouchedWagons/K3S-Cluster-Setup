
# Projects in the monitoring Namespace

This namespace is by far the busiest, with Prometheus and Grafana being the anchor points. The values.yaml file for prometheus probably doesn't need to be as big as it is but whatever. I don't remember why I install Grafana seperately.

Prometheus has an issue about Alert Manager receivers having to be named null or else Alert Manager won't start which is wild.

## Monitoring of Bazarr, Lidarr, Prowlarr, Radarr, SabNZBd and Sonarr

Monitoring of these services is handled by [exportarr](https://github.com/onedr0p/exportarr) containers and is pretty straight forward.

## Monitoring of iDRAC (Dell PowerEdge servers)

Monitoring of my PowerEdge servers is handled by [idrac_exporter](https://github.com/mrlhansen/idrac_exporter). Redfish has to be enabled in the idrac and a suitable user has to be created. Since iDRACs are pretty slow the scrape interval can't be particularly fast and the process is prone to timing out which can cause issues. Information reported isn't consistent either, my R730XD's power consumption is reported but not my R630.

## Monitoring of generic linux machines

Very little is actually needed, node-exporter needs to be installed and running on whatever you want monitored and that's basically it.

## Monitoring of a UPS via Network UPS Tools (aka NUT)

Monitoring of my UPS is handled via [prometheus-nut-exporter](https://github.com/hon95/prometheus-nut-exporter), there is an alternative exporter but I found this one worked better somehow. There's even a sample Grafana dashboard!

## Monitoring of nvidia GPUs

This one can be a bit tricky if a node has multiple GPUs and time slicing hasn't been set up but the [nvidia_gpu_exporter](https://github.com/utkuozdemir/nvidia_gpu_exporter) will get you on the right track, comes with a grafana dashboard too.

## Monitoring of OpnSense

Another straightforward one, it can be rolled into the generic Linux job above but I have it separate since OpnSense runs on some flavour of BSD. Requires the node-exporter plugin in OpnSense

## Monitoring of qBitTorrent

Downloading Linux ISOs is serious business and requires six 9's of uptime so use [prometheus-qbittorrent-exporter](https://github.com/esanchezm/prometheus-qbittorrent-exporter) to make sure everything's running.

