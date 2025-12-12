# Democratic CSI for FreeNAS/TrueNAS

This directory contains the Flux configuration for deploying democratic-csi to enable dynamic provisioning of persistent volumes using FreeNAS/TrueNAS API drivers.

## Overview

Democratic CSI provides Container Storage Interface (CSI) drivers for various storage systems, including FreeNAS/TrueNAS. This deployment supports both NFS and iSCSI protocols through the FreeNAS API without requiring SSH access.

## Configuration

### Prerequisites

1. **FreeNAS/TrueNAS Server**: Ensure your FreeNAS/TrueNAS server is running and accessible at `172.16.0.102`
2. **API Access**: Enable API access on your FreeNAS/TrueNAS server
3. **ZFS Pool**: Have a ZFS pool configured for storage (default: `tank`)
4. **Network Access**: Ensure Kubernetes nodes can reach the FreeNAS/TrueNAS server

### Available Drivers

This deployment includes two separate configurations:

1. **freenas-api-nfs** (`helm-release-nfs.yaml`): For NFS-based storage
2. **freenas-api-iscsi** (`helm-release-iscsi.yaml`): For iSCSI-based storage

### Required Configuration Updates

Before deploying, you **MUST** update the following values in both `helm-release-nfs.yaml` and `helm-release-iscsi.yaml`:

#### 1. FreeNAS API Authentication
```yaml
# Update these values in both node.driver.config and controller.driver.config sections
httpConnection:
  apiKey: "YOUR-API-KEY-HERE"   # Replace with your FreeNAS API key (recommended)
  username: "YOUR-USERNAME"     # Replace with your FreeNAS username
  password: "YOUR-PASSWORD"     # Or use password if no API key (not recommended)
```

#### 2. ZFS Configuration (if different from defaults)
```yaml
# Update these values to match your ZFS setup
zfs:
  datasetParentName: "tank/k8s/nfs/vols"  # For NFS
  # OR
  datasetParentName: "tank/k8s/iscsi/vols"  # For iSCSI
```

#### 3. Network Configuration
```yaml
# Update network settings for your environment (already set to 172.16.0.0/16)
nfs:
  shareAllowedNetworks: ["172.16.0.0/16"]
```

### Driver Selection

Both drivers will be deployed simultaneously. You can choose which one to use by specifying the storage class:

- **NFS**: Use storage class `freenas-nfs`
- **iSCSI**: Use storage class `freenas-iscsi`

## Deployment

The democratic-csi infrastructure is automatically deployed by Flux when you commit and push these changes. The deployment includes:

1. **Namespace**: Creates the `democratic-csi` namespace
2. **Helm Repository**: Adds the democratic-csi Helm chart repository
3. **Helm Releases**: Deploys both NFS and iSCSI democratic-csi configurations
4. **Storage Classes**: Creates `freenas-nfs` and `freenas-iscsi` storage classes

## Verification

After deployment, verify the installation:

```bash
# Check pod status
kubectl -n democratic-csi get pods

# Check storage classes
kubectl get storageclass

# Check CSI drivers
kubectl get csidrivers
```

## Usage

Once deployed, you can create persistent volumes using the storage classes:

```yaml
# For NFS
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: freenas-nfs
  resources:
    requests:
      storage: 10Gi

---

# For iSCSI
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-iscsi-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: freenas-iscsi
  resources:
    requests:
      storage: 10Gi
```

## Security Considerations

1. **API Keys**: Use API keys instead of passwords when possible
2. **Network Security**: The configuration is set for the 172.16.0.0/16 network
3. **SSL/TLS**: Set `allowInsecure: false` and use valid SSL certificates in production
4. **Secrets Management**: Consider using Kubernetes secrets for sensitive data

## API-Only Configuration

This deployment uses only the FreeNAS API for all operations, eliminating the need for:
- SSH access to FreeNAS
- SSH keys
- Command-line tools on FreeNAS

All storage operations are performed through the FreeNAS REST API.

## Troubleshooting

### Common Issues

1. **Connection Issues**: Verify network connectivity and API credentials
2. **Permission Issues**: Ensure the FreeNAS user has sufficient API permissions
3. **Dataset Issues**: Verify ZFS pool and dataset existence
4. **Network Issues**: Check firewall rules and network policies

### Logs

Check logs for debugging:

```bash
# Controller logs (NFS)
kubectl -n democratic-csi logs -l app.kubernetes.io/component=controller,app.kubernetes.io/instance=democratic-csi-nfs

# Controller logs (iSCSI)
kubectl -n democratic-csi logs -l app.kubernetes.io/component=controller,app.kubernetes.io/instance=democratic-csi-iscsi

# Node logs (NFS)
kubectl -n democratic-csi logs -l app.kubernetes.io/component=node,app.kubernetes.io/instance=democratic-csi-nfs

# Node logs (iSCSI)
kubectl -n democratic-csi logs -l app.kubernetes.io/component=node,app.kubernetes.io/instance=democratic-csi-iscsi
```

## Additional Resources

- [Democratic CSI Documentation](https://github.com/democratic-csi/democratic-csi)
- [FreeNAS API Documentation](https://www.truenas.com/docs/scale/scaleapi/)
- [Kubernetes CSI Documentation](https://kubernetes-csi.github.io/docs/)