Reference links:

- [Istio getting started official guide](https://istio.io/latest/docs/setup/getting-started/)
- [Istio tutorial](https://medium.com/google-cloud/istio-service-mesh-101-part-1-3-f07a8fedeea8)


Download Istio
'''
# Download the latest version
curl -L https://istio.io/downloadIstio | sh -

# Download 1.20.0 version 
curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.20.0 TARGET_ARCH=x86_64 sh -

# The installation directory contains Sample applications and  istioctl client binary in the bin/ directory.
# Add the istioctl client to path
cd istio-1.20.0
export PATH=$PWD/bin:$PATH
'''

Install Istio
'''
# Configure to use demo profile 
istioctl install --set profile=demo -y
 # Istio core installed
 # Istiod installed
 # Egress gateways installed
 # Ingress gateways installed
 # Installation complete

# istioctl insall check 
istioctl version

# Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies
kubectl label namespace default istio-injection=enabled

# check the label
kubectl label namespace default istio-injection=enabled
'''

Uninstall
'''
istioctl uninstall -y --purge

# The istio-system namespace is not removed by default
kubectl delete namespace istio-system

# remove the label
kubectl label namespace default istio-injection-
'''

