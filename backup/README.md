# Backup Google Cloud Service

Start your volume:

```bash
fleetctl start gcloud-config.service
```

## Install keys
Detect where your service is

```bash
fleetctl list-units
> jenkins.service				85945e91.../62.210.244.113	active	running
export SERVER_SSH=core@62.210.244.113
```

Copy p12 key for google storage from you google cloud console:

```bash
scp ~/Downloads/your-key.p12 $SERVER_SSH:~/your-key.p12
```

Ssh to your machine:

```bash
ssh $SERVER_SSH
```

On the core os machine install key:

```bash
docker run --volumes-from gcloud-config \
  --volume $HOME/your-key.p12:/tmp/your-key-on-docker.p12 \
  --tty \
  --interactive \
  --rm google/cloud-sdk \
  gcloud auth activate-service-account <your-service-account-email> --key-file /tmp/your-key-on-docker.p12 --project <your-project-id>

# test if everything works
docker run --volumes-from gcloud-config --interactive --tty --rm google/cloud-sdk gsutil ls gs://<your-bucket>/
```