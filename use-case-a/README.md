# Use case a - Web application access through the UI

Those manifests are needed to enable:
1. Log in flow through the UI.
2. Access a web application through the UI (jupyter-web-app) in this case.

## Requirements
1. A [clean setup](../clean-setup/).

## Results
I could login and access the Notebooks web app and send a create notebook request using
```shell
kubectl -n istio-system port-forward svc/ambient-ingressgateway-istio 8080:80
```
For more information on the final authorization setup, refer to https://github.com/canonical/bundle-kubeflow/issues/1285#issuecomment-3056440427.

### mTLS: strict
The file `strict-pa-kubeflow.yaml` is not needed. It was applied to test the setup's compatibility with strict mTLS mode. That's why this is not included in the `kustomization.yaml` file.

### Waypoint
The waypoint and its corresponding AuthorizationPolicy were deployed to demonstrate the proposed setup. However, no services needed to be enrolled to support this use case.
