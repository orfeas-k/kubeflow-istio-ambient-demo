## Send all traffic via a waypoint
The instructions below achieve the same functionality (access to a web app) with a different setup.

## Setup
1. Enroll all services in the `kubeflow` namespace to the waypoint, instead of specific ones.
2. Proxy all traffic (including ingress traffic) via the waypoint.

This ensures all AuthorizationPolicy are enforced at the waypoint for services in the `kubeflow` namespace.

## Instructions
1. Follow instructions from parent directory.
2. Label the `kubeflow` namespace with extra waypoint labels
```sh
kubectl label ns kubeflow istio.io/use-waypoint=kubeflow-waypoint
kubectl label ns kubeflow istio.io/ingress-use-waypoint=true
```
3. Build and apply manifests from this directory with
```sh
kustomize build . | kubectl apply -f -
```
3. Delete previous obsolete AP resources
```sh
kubectl delete -n kubeflow authorizationpolicy centraldashboard-l4-ingressgateway
kubectl delete -n kubeflow authorizationpolicy jwa-l4-ingressgateway
kubectl delete -n kubeflow authorizationpolicy profiles-l4-centraldashboard
```

## Results
Final resources should look like this:
```sh
$ kubectl get authorizationpolicy -A
NAMESPACE                   NAME                                    ACTION   AGE
istio-system                ambient-ingressgateway                  ALLOW    3d3h
istio-system                ambient-ingressgateway-oauth2-proxy     CUSTOM   47h
istio-system                ambient-ingressgateway-require-jwt      DENY     47h
istio-system                global-allow-noting-deny-all                     3d4h
istio-system                waypoint-allow-nothing                           2d2h
kubeflow-user-example-com   ml-pipeline-visualizationserver                  4d2h
kubeflow-user-example-com   ns-owner-access-istio                            4d2h
kubeflow                    centraldashboard-ap-l7-ingressgateway   ALLOW    2d1h
kubeflow                    centraldashboard-l4-waypoint            ALLOW    2d1h
kubeflow                    jwa-ap-l7-ingressgateway                ALLOW    47h
kubeflow                    jwa-l4-waypoint                         ALLOW    47h
kubeflow                    profiles-kfam-l7-cd                     ALLOW    47h
kubeflow                    profiles-l4-waypoint                    ALLOW    47h

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
