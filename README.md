# OpenShift E2E testing not working

https://github.com/fabric8io/kubernetes-client/issues/5545

Evaluate the different options to be able to keep testing our projects in an OpenShift cluster.

## OSCI.io

https://openshift-console.osci.io/ 

These are not ephemeral clusters with a single namespace per project and limited capabilities.
- Jobs can't be run concurrently.
- Some features are missing CRDs, etc.

### GitHub setup

