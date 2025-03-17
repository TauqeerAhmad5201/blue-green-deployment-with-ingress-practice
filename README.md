# Kubernetes NGINX Ingress Controller Setup Guide

## Overview
This documentation covers the setup of NGINX Ingress Controller on Kubernetes using raw manifests, implementing path-based routing for blue-green deployments.

## Prerequisites
- Running Kubernetes cluster
- `kubectl` configured with cluster access
- Basic understanding of Kubernetes concepts

## Installation of NGINX Ingress Controller

We use the official NGINX Ingress Controller manifest for Kubernetes version 1.8.1. This method provides more control and visibility over the deployment components.

### Steps to Install NGINX Ingress Controller

1. **Download the Manifest File**  
   Download the official NGINX Ingress Controller manifest:
   ```bash
   wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml
   ```

2. **Apply the Manifest**  
   Deploy the NGINX Ingress Controller to your cluster:
   ```bash
   kubectl apply -f deploy.yaml
   ```

3. **Verify the Installation**  
   Check the status of the NGINX Ingress Controller pods:
   ```bash
   kubectl get pods -n ingress-nginx
   ```

4. **Check the External IP**  
   Retrieve the external IP of the NGINX Ingress Controller service:
   ```bash
   kubectl get svc ingress-nginx-controller -n ingress-nginx
   ```
   Wait until the `EXTERNAL-IP` field shows a valid IP address instead of `pending`.

## Project Structure
```plaintext
/aks-learn-new/
├── deploy.yaml           # NGINX Ingress manifest
├── ingress.yml          # Routing rules
├── blue-deployment.yml  # V1 application
├── green-deployment.yml # V2 application
├── blue-service.yml     # V1 service
└── green-service.yml    # V2 service
```

## Components

### 1. NGINX Ingress Controller
- **Version**: 1.8.1
- **Namespace**: ingress-nginx
- **Service Type**: LoadBalancer
- **Ports**: 
  - HTTP: 80
  - HTTPS: 443

### 2. Ingress Configuration
```yaml
Path Configuration:
- /v1    → Blue Service (Version 1)
- /v2    → Green Service (Version 2)
- /      → Default Service (Blue)
```

### 3. Application Services
- **Blue Service**: Original version
  - Port: 3000
  - Type: ClusterIP
  
- **Green Service**: New version
  - Port: 3000
  - Type: ClusterIP

## Architecture
```plaintext
                        ┌──────────────┐
                        │   Ingress    │
                        │  Controller  │
                        └──────┬───────┘
                               │
                   ┌──────────┴──────────┐
                   │                     │
            ┌──────┴─────┐        ┌─────┴──────┐
            │    /v1     │        │    /v2     │
      ┌─────┴─────┐    ┌─┴────────┴─┐
      │   Blue    │    │   Green    │
      │ Service   │    │  Service   │
      └─────┬─────┘    └────────────┘
            │
    ┌───────┴───────┐
    │ Blue          │
    │ Deployment    │
    └───────────────┘
```

## Security Considerations
- RBAC rules are configured for the ingress controller
- ServiceAccounts are properly configured
- Network policies should be implemented based on requirements

## Monitoring and Logs
The ingress controller provides metrics and logs for monitoring:
- **Metrics**: Available at `/metrics` endpoint
- **Health**: Status check at `/healthz`
- **Logs**: Container logs with detailed request information

## Best Practices
1. Always use specific versions for deployments
2. Implement health checks
3. Configure resource limits
4. Use proper annotations for customization
5. Regular backup of configurations

## Scaling
The ingress controller can be scaled:
- Horizontal scaling through deployment replicas
- Resource allocation adjustment
- Load balancer configuration

## Troubleshooting Guide
Common issues and solutions:
1. **Pending External IP**
   - Check cloud provider configuration
   - Verify service type LoadBalancer support

2. **Routing Issues**
   - Verify ingress class
   - Check service endpoints
   - Validate path configurations

3. **Certificate Issues**
   - Verify TLS secret
   - Check certificate validity
   - Confirm proper annotations

## References
- [Official NGINX Ingress Controller Documentation](https://kubernetes.github.io/ingress-nginx/)
- [Kubernetes Ingress Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)

## Version History
- v1.0.0 - Initial setup with NGINX Ingress Controller v1.8.1
- v1.1.0 - Added blue-green deployment configuration

## Support
For issues and support:
1. Check controller logs
2. Verify configuration
3. Review Kubernetes events
4. Consult official documentation
