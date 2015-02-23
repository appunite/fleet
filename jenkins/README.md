# Run jenkins

Setup your ssh keys.
Login to some machine via ssh `fleetctl ssh <machine-id>` and save certs:

```bash
etcdctl mkdir /services/nginx/jenkins/
etcdctl mkdir /services/nginx/jenkins/servers/
etcdctl set /services/nginx/jenkins/root 'jenkins.example.com'
cat www-server.crt | etcdctl set /services/nginx/jenkins/ssl-crt
cat www-server.key | etcdctl set /services/nginx/jenkins/ssl-key
```

Run jenkins

```bash
feetctl list-machines -l
```

update in all `*.service` files `MachineID` to one of your machines than:

```bash
cd jenkins 
fleetctl start squid.service jenkins-volume.service jenkins.service
```

Test your jenkins installation

```bash
fleetctl start jenkins-test
fleetctl list-units
curl http://<your-instance-ip>:10000
fleetctl destroy jenkins-test
```

Test via nginx

```bash
fleetctl start nginx@{1..3}.service
curl https://jenkins.example.com
```

## Backup and restore
[backup and restore doc is here](doc-backup.md)