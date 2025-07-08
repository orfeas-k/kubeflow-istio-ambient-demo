# Use case a - Web application access through the UI

Those manifests are needed to enable:
1. Log in flow through the UI.
2. Access a web application through the UI (jupyter-web-app) in this case.

## Requirements
1. A [clean setup](../clean-setup/).
1. The proper labels to `kubeflow` namespace with:
    ```shell
    kubectl label ns kubeflow istio.io/use-waypoint=kubeflow-waypoint
    kubectl label ns kubeflow istio.io/ingress-use-waypoint=true
    ```

## Results
For more information on the final authorization setup, refer to https://github.com/canonical/bundle-kubeflow/issues/1285#issuecomment-3036555144.

### mTLS: strict
The file `strict-pa-kubeflow.yaml` is not needed. It was applied to test the setup's compatibility with strict mTLS mode. That's why this is not included in the `kustomization.yaml` file.

### ingress-use-waypoint
This assumes the use of `ingress-use-waypoint` label. If this is disabled, there needs to be an L4 policy for the centraldashboard and the jupyter-web-app to allow Ztunnel requests from the centraldashboard
```
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: centraldashboard-l4-ingressgateway
  namespace: kubeflow
spec:
  action: ALLOW
  rules:
  - from:
    - source:
        principals:
        - cluster.local/ns/istio-system/sa/ambient-ingressgateway-istio
  selector:
    matchLabels:
      app: centraldashboard
```
