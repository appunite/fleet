# Monitoring

Obrain api key from [datadog](https://app.datadoghq.com/account/settings#api)

```bash
fleetctl ssh 1210e283
  etcdctl set /ddapikey c0re0s070120131499294110c0re0sdd
exit

fleetctl submit monitor.service 
fleetctl start monitor
```

logs:

```bash
fleetctl ssh 1210e283
  docker logs dd-agent
exit
```