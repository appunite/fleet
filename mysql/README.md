# Install mysql

Setup root password:

```bash
etcdctl mkdir /services/mysql
etcdctl set /services/mysql/password your_root_password
```

Start mysql:

```bash
fleectl start mysql.services
```

Test database:

```bash
fleetctl list-units
fleetctl ssh 1210e283
cat /etc/environment 
> COREOS_PRIVATE_IPV4=10.91.1.142
docker run --interactive --tty --rm mysql:5.7.5 /bin/bash
mysql --host 10.91.1.142 --port 3306 --user root --password=your_root_password
```
