# Use case a - Web application access through the UI

Those manifests are needed to enable:
1. Log in flow through the UI.
![alt text](./img/image.png)
2. Access a web application through the UI (jupyter-web-app) in this case.
![alt text](./img/image-1.png)

> [!NOTE]
> The diagrams are used to indicate the request flow and do not correspond exactly to the manifests from this repo. Those include the resources used in sidecar mode.

## Requirements
1. A [clean setup](../clean-setup/).

## Instructions
Apply manifests from this directory using:
```shell
kustomize build . | kubectl apply -f -
```
Note that:
1. The `strict-pa-kubeflow` PeerAuthentication (that set mTLS mode to `strict`) is not a requirement for Kubeflow and thus excluded from the `kustomization.yaml` file. It was used to test the setup's compatibility with strict mTLS mode.
1. The waypoint and its corresponding AuthorizationPolicy are deployed to demonstrate the proposed setup. However, no services need to be enrolled to support this use case.

## Results
You should be able to login, access the Notebooks web app and send a create notebook request. This is possible from `localhost:8080` with
```shell
kubectl -n istio-system port-forward svc/ambient-ingressgateway-istio 8080:80
```
For more information on the final authorization setup, refer to https://github.com/canonical/bundle-kubeflow/issues/1285#issuecomment-3056440427.

The expected istio resources are:
```
$ kubectl get ap -A
NAMESPACE                   NAME                                    ACTION   AGE
istio-system                ambient-ingressgateway                  ALLOW    3d3h
istio-system                ambient-ingressgateway-oauth2-proxy     CUSTOM   47h
istio-system                ambient-ingressgateway-require-jwt      DENY     47h
istio-system                global-allow-noting-deny-all                     3d4h
istio-system                waypoint-allow-nothing                           2d2h
kubeflow-user-example-com   ml-pipeline-visualizationserver                  4d2h
kubeflow-user-example-com   ns-owner-access-istio                            4d2h
kubeflow                    centraldashboard-l4-ingressgateway   ALLOW    2d1h
kubeflow                    jwa-l4-ingressgateway                         ALLOW    47h
kubeflow                    profiles-l4-centraldashboard                    ALLOW    47h

$ kubectl get requestauthentications.security.istio.io -A
NAMESPACE      NAME      AGE
istio-system   dex-jwt   47h

$ kubectl get httproutes -A                              
NAMESPACE      NAME               HOSTNAMES   AGE
auth           dex                            3d5h
kubeflow       centraldashboard               3d23h
kubeflow       jupyter-web-app                3d23h
oauth2-proxy   oauth2-proxy                   3d5h
```
