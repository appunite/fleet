# Setup cadvisor

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
