
# Jenkins backup and restore

## Restore data from jenkins backup:

Prepare your backup service according to [backup doc](../backup/README.md)

On your machine:

```bash
fleetctl stop jenkins.service
```

On coreos:

```bash
docker run \
  --tty \
  --interactive \
  --rm \
  --volumes-from jenkins-volume.service \
  --volumes-from gcloud-config \
  google/cloud-sdk \
  /bin/bash -c "gsutil cat gs://<your-backup-file>.tar.gz | tar xvf"
```

On your machine:

```bash
fleetctl start jenkins.service
```

Open browser:
```bash
open jenkins.appunite.net
```


## Backup your data manuall
On coreos machine:

```bash
DIR=gs://au-router/docker-backups/backup_$(date +"%Y-%d-%m_%H%M%S")

docker run \
  --tty \
  --interactive \
  --volumes-from gcloud-config \
  --volumes-from jenkins-volume.service \
  --volume=$(pwd):/backup \
  google/cloud-sdk \
  /bin/bash -c "tar --exclude='/jenkins/jobs/*/workspace*/*' --exclude='/jenkins/jobs/*/builds/*/archive' -zcvf /tmp/file.tar.gz /jenkins && gsutil cp /tmp/file.tar.gz ${DIR}/jenkins.tar.gz"
```


## Backup via etcdctl

### Backup one shot

```bash
fleetctl load backup-jenkins.service
```

### backup timer

```bash
fleetctl load backup-jenkins-timer.timer 
```
