# Fleet configuration

## Setup fleet ctl

```bash
# clone repostiory
git clone https://github.com/coreos/fleet.git
cd fleet
# build
./build
# Copy to your bash path
cp bin/fleetctl ~/bin/fleetctl

# Add your ssh key to agent
ssh-add ~/.ssh/id_rsa
# setup tunnel address
export FLEETCTL_TUNNEL=coreos1.appunite.net
# test connection
fleetctl list-machines
> MACHINE		IP		METADATA
> 1210e283...	8.8.8.127	-
> 85945e91...	8.8.8.113	-
> f7e21643...	8.8.8.112	-
```

## Run test service

```bash
fleetctl submit test.service 
fleetctl start test
```

Test:

```bash
fleetctl list-units
> test.service				85945e91.../62.210.244.113	inactive	dead
```

Show logs:

```bash
fleetctl journal test
```

Test if working:

```bash
# find ip via list-units
ssh core@62.210.244.113
# Run some docker
docker run --tty --interactive --link=testservice:test --rm busybox
# Test connection
telnet test 8888
> OK
```

Remove test:
```bash
fleetctl destroy test
```


## Run monitoring
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


## Run jenkins

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
cd jenkins

fleetctl submit squid.service 
fleetctl submit jenkins-volume.service 
fleetctl submit jenkins.service 

fleetctl start squid.service
fleetctl start jenkins-volume.service 
fleetctl start jenkins-service
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


## Setup cadvisor

Run cAdvisors

```bash
fleetctl start cadvisor.service
```

Setup roots:

```bash
etcdctl ls /services/nginx/
> /services/nginx/cadvisor_1.2.3.4
> /services/nginx/cadvisor_1.2.3.5

etcdctl set /services/nginx/cadvisor_1.2.3.4/root 'cadvisor1.example.com'
etcdctl set /services/nginx/cadvisor_1.2.3.5/root 'cadvisor2.example.com'
```

Setup your ssh

```bash
cat www-server.crt | etcdctl set /services/nginx/cadvisor_1.2.3.4/ssl-crt
cat www-server.key | etcdctl set /services/nginx/cadvisor_1.2.3.4/ssl-key
cat www-server.crt | etcdctl set /services/nginx/cadvisor_1.2.3.5/ssl-crt
cat www-server.key | etcdctl set /services/nginx/cadvisor_1.2.3.5/ssl-key
```

Test your connection:

```bash
open cadvisor1.example.com
open cadvisor2.example.com
```

Restrict connection via base64
``bash
etcdctl set /services/nginx/cadvisor_1.2.3.4/restrict true
etcdctl mkdir /services/nginx/cadvisor_1.2.3.4/restrict-users
etcdctl set /services/nginx/cadvisor_1.2.3.4/restrict-users/root password

etcdctl set /services/nginx/cadvisor_1.2.3.5/restrict true
etcdctl mkdir /services/nginx/cadvisor_1.2.3.5/restrict-users
etcdctl set /services/nginx/cadvisor_1.2.3.5/restrict/-usersroot password
```


## Setup diest router

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

