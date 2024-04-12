# OpenShift E2E testing not working

https://github.com/fabric8io/kubernetes-client/issues/5545
https://github.com/eclipse/jkube/issues/2900

Evaluate the different options to be able to keep testing our projects in an OpenShift cluster.

## OSCI.io

https://openshift-console.osci.io/ 

These are not ephemeral clusters with a single namespace per project and limited capabilities.
- Jobs can't be run concurrently.
- Some features are missing CRDs, etc.

We **won't** be able to run the E2E tests in this cluster for each pull request.
Not being able to run groups of tests concurrently, means that running the entire suite takes a very long time.

### Generating tokens

Generating a long-lived token for a service account:
```bash
oc project $project
# https://docs.openshift.com/container-platform/4.14/authentication/understanding-and-creating-service-accounts.html
oc create sa robot
oc policy add-role-to-user admin system:serviceaccount:prod-jkube-ci:robot
# Or for the Fabric8 Kubernetes Client
# oc policy add-role-to-user admin system:serviceaccount:prod-fabric8-kubernetes-client-ci:robot
# https://access.redhat.com/solutions/7025261 (How to create a long lived service account token in RHOCP 4)
oc create token robot --duration=$((365*24))h
```

Logging in  with the token:
```bash
oc login --server=https://api.ospo-osci.z3b1.p1.openshiftapps.com:6443 --token=$token
```

### GitHub setup

### Eclipse JKube setup

> [!WARNING]
> The work here has not concluded yet (https://github.com/eclipse/jkube/issues/2900)

> [!NOTE]
> Tests for Eclipse JKube are slow to run if they can't be parallelized and run on multiple clusters at the same time.
> This limits the number of pipeline executions that can be run in a day.
> For now, we'll only run the tests once a day.

The Eclipse JKube E2E tests repository (https://github.com/jkubeio/jkube-integration-tests) includes a new tag (`OpenShiftOSCI`) to specifically run the tests in the OSCI.io cluster.
https://github.com/jkubeio/jkube-integration-tests/pull/361

#### Secrets

A long-lived token is stored in a secret (https://github.com/jkubeio/jkube-integration-tests/settings/secrets/actions):

| Secret      | Description                                                          |
|-------------|----------------------------------------------------------------------|
| OSCI_SERVER | The OpenShift server URL                                             |
| OSCI_TOKEN  | The OpenShift token as generated following the provided instructions |

### Fabric8 Kubernetes Client setup

The E2E tests have been added as part of a nightly scheduled job (can also be triggered manually).

The applicable tests have been tagged with `@Tag("OSCI")`

https://github.com/fabric8io/kubernetes-client/pull/5883


#### Secrets

| Secret      | Description                                                          |
|-------------|----------------------------------------------------------------------|
| OSCI_SERVER | The OpenShift server URL                                             |
| OSCI_TOKEN  | The OpenShift token as generated following the provided instructions |

