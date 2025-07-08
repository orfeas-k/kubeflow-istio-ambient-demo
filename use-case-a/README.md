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
This assumes the use of the `ingress-use-waypoint` label. For a setup without that label, see the [overlay-without-ingress-use-gateway](./overlay-without-ingress-use-gateway) directory. Note that it is not a proper overlay that can be used with `kustomize`.
