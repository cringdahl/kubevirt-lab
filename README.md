# kubevirt lab FINAL

## What even is this?

This was an attempt to get Github Actions Runners VM images running in kubevirt in a sustainable fashion. Unfortunately, I got stuck at [custom code requirements](https://github.com/zhaofengli/kubevirt-actions-runner) for the runner-scale-set, which was beyond the maintenance scope of the project.

## Usefulness

I did manage to get multiple VMs spinning up from one QCOW2 DataVolume, which I didn't see well documented anywhere. Next steps would have been spinning up VirtulMachineInstance objects instead to see if that decreased create time.

## Rough Notes

Kubevirt: https://kubevirt.io/quickstart_kind/
kubevirt-operator.yaml - v1.2.0
kubevirt-cr.yaml

CDI: (img->pvc support) https://kubevirt.io//labs/kubernetes/lab2
- registry
  - no registry required (or warranted)
- cdi-operator.yaml - v1.59.0
  - from cdi site
- cdi-cr.yaml
  - from cdi site
- cdi-ingress.yaml
  - handmade
  - creates virtctl image-upload URL
  - assumes nginx ingress installed cluster-wide
- cdi-namespace.yaml (eg 'vmi')
  - handmade
  - target for VMs and images

Longhorn (via helm via rancher) (v1.6.1) distributed storage w/ snapshot support
- should be installed in System Project (rancher)
- snapshot support
 - upgraded external-snapshotter client/config/crd to v6.3.2 per longhorn docs
 - longhorn-volumesnapshotclass.yaml
  - handmade
 - longhorn-storageprofile.yaml
  - handmade
  - should use filesystem instead of block in storageprofile
    - longhorn's block devices don't play nice w/ kubevirt (per #kubevirt-dev Slack chat, filesystem was recommended)

QCOW2 DataVolume Availability:
```
virtctl image-upload dv ubuntu2204 --namespace=vmi --size=5Gi --image-path=/Users/cringdahl/Downloads/ubuntu-22.04-server-cloudimg-amd64.img --uploadproxy-url=https://cdi.10-22-37-94.sslip.io --insecure 
```

VMs:
- take 45 seconds to spin up, then 25-30 seconds to get to multi-user
 - in lab environment
- successfully run off snapshot of base ubuntu-2204 image
