# Run gitlab-ci

Set `appId` and `appSecret` from you gitlab

```bash
etcdctl mkdir /services/gitlab-ci
etcdctl mkdir /services/nginx/gitlab-ci/appId ""
etcdctl set /services/nginx/gitlab-ci/appSecret ""
etcdctl set /services/nginx/gitlab-ci/secretsSessionKeyBase ""
etcdctl set /services/nginx/gitlab-ci/secretesDbKeyBase ""
etcdctl set /services/nginx/gitlab-ci/servers/
etcdctl set /services/nginx/gitlab-ci/root 'gitlab-ci.example.com'
cat www-server.crt | etcdctl set /services/nginx/gitlab-ci/ssl-crt
cat www-server.key | etcdctl set /services/nginx/gitlab-ci/ssl-key
```

Run gitlab

```bash
feetctl list-machines -l
```

update in all `\*.service` files `MachineID` to one of your machines than:

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
docker run \
	--privileged \
	--volume /var/run/docker.sock:/var/run/docker.sock \
	--volume $(which docker):/bin/docker \
	--volume /opt/gitlab-ci-runner/etc:/etc/gitlab-runner \
	--volume /opt/gitlab-ci-runner/home:/home/gitlab-runner \
	--tty --interactive --rm \
	--workdir=/home/gitlab-runner \
	ayufan/gitlab-ci-multi-runner:latest register --limit 10 --docker-image jacekmarchwicki/android --url "https://gitlab-ci.appunite.net/" --tag-list android --executor docker --non-interactive --registration-token <YOUR_TOKEN> --description coreos1-android
```

## Update

```bash
fleetctl destroy gitlab-ci-runner\@{1..3}.service
fleetctl start gitlab-ci-runner\@{1..3}.service
fleetctl destroy gitlab-ci.service
fleetctl start gitlab-ci.service
```
