

Detect where your jenkins is:

```bash
fleetctl list-units
> jenkins.service				85945e91.../62.210.244.113	active	running
export SERVER_SSH=core@62.210.244.113
```

Copy p12 key for your google storage login:

```bash
scp ~/Downloads/your-key.p12 $SERVER_SSH:~/your-key.p12
```

Ssh to your machine:

```bash
ssh $SERVER_SSH
```

On the core os machine:

```bash
# setup your auth storage volume
docker run --volume /.config --name gcloud-config busybox true

# install key
docker run --volumes-from gcloud-config \
  --volume $HOME/your-key.p12:/tmp/your-key-on-docker.p12 \
  --tty \
  --interactive \
  --rm google/cloud-sdk \
  gcloud auth activate-service-account <your-service-account-email> --key-file /tmp/your-key-on-docker.p12 --project <your-project-id>

# test if everything works
docker run --volumes-from gcloud-config --interactive --tty --rm google/cloud-sdk gsutil ls gs://<your-bucket>/
```

## Restore data from jenkins backup:

On your machine:

```bash
fleetctl stop jenkins.service
```

On coreos:

```bash
docker run \
  --tty \
  --interactive \
  -- rm \
  --volumes-from jenkins-data \
  --volumes-from gcloud-config \
  google/cloud-sdk \
  /bin/bash -c "gsutil cat gs://<your-backup-file> | tar xvf"
```

On your machine:

```bash
fleetctl start jenkins.service
```

Open browser:
```bash
open jenkins.appunite.net
```


