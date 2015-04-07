# Run gitlab-ci

Set `appId` and `appSecret` from you gitlab

```bash
etcdctl mkdir /services/gitlab-ci
etcdctl mkdir /services/nginx/gitlab-ci/appId ""
etcdctl set /services/nginx/gitlab-ci/appSecret ""
etcdctl set /services/nginx/gitlab-ci/servers/
etcdctl set /services/nginx/gitlab-ci/root 'gitlab-ci.example.com'
cat www-server.crt | etcdctl set /services/nginx/gitlab-ci/ssl-crt
cat www-server.key | etcdctl set /services/nginx/gitlab-ci/ssl-key
```

Run gitlab

```bash
feetctl list-machines -l
```

update in all `*.service` files `MachineID` to one of your machines than:

```bash
cd gitlab 
fleetctl start gitlab-ci.service gitlab-postgresql.service gitlab-redis.service
```

Test via nginx

```bash
curl https://gitlab-ci.appunite.net
```

## Connect nodes

```bash
fleetctl start gitlab-ci-runner\@{1..3}.service
fleetctl list-units
```

On every machine

```bash
fleetctl ssh 1210e283
docker exec --interactive --tty gitlab-ci-runner gitlab-ci-multi-runner setup
```



