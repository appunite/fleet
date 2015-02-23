# Setup diest router

Setup etcd:

```bash
etcdctl mkdir /services/nginx/x_deis/
etcdctl mkdir /services/nginx/x_deis/servers/
etcdctl set /services/nginx/x_deis/root *.example.com
cat www-server.crt | etcdctl set /services/nginx/x_deis/ssl-crt
cat www-server.key | etcdctl set /services/nginx/x_deis/ssl-key
```

Update deis-router:

```bash
fleetctl destroy deis-router@{1..3} deis-router@
fleetctl start dies-router@{1..3}
```