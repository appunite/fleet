# Install postgres

Setup root password:

```bash
etcdctl mkdir /services/postgres
etcdctl set /services/postgres/password your_root_password
```

Start postgres:

```bash
fleectl start postgres.services
```

Test database:

```bash
fleetctl list-units
fleetctl ssh 1210e283
cat /etc/environment 
> COREOS_PRIVATE_IPV4=10.91.1.142
docker run --interactive --tty --rm postgres /bin/bash
psql --host 10.91.1.142 --port 5433 --username postgres
```
