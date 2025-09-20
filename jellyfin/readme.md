## Jellyfin image based OS

Containers are embedded as quadlets

See more here:
https://docs.fedoraproject.org/en-US/bootc/embedding-containers/#_quadlet_containerized_workloads_in_systemd

### Build podman image:
```
sudo podman build -t localhost/jellyfin -f Containerfile .
```

### Create qcow2 bootc image
```
sudo podman run \
    --rm \
    -it \
    --privileged \
    --pull=never \
    --security-opt label=type:unconfined_t \
    -v ./config.toml:/config.toml:ro \
    -v ./output:/output \
    -v /var/lib/containers/storage:/var/lib/containers/storage \
    quay.io/centos-bootc/bootc-image-builder:latest \
    --type qcow2 \
    --use-librepo=True \
    localhost/jellyfin:latest

```

### Create bootc iso image
```
sudo podman run \
    --rm \
    -it \
    --privileged \
    --pull=never \
    --security-opt label=type:unconfined_t \
    -v ./config.toml:/config.toml:ro \
    -v ./output:/output \
    -v /var/lib/containers/storage:/var/lib/containers/storage \
    quay.io/centos-bootc/bootc-image-builder:latest \
    --type iso \
    --use-librepo=True \
    localhost/jellyfin:latest

```

### Create VM from qcow2 with libvirt
```
sudo virt-install \                                       
  --name jellyfin-bootc \
  --cpu host \
  --vcpus 4 \
  --memory 4096 \
  --import --disk ./output/qcow2/disk.qcow2,format=qcow2 \
  --os-variant fedora-eln \
  --network network=default
```