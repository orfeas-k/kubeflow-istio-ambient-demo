## Instructions
1. Apply all manifests with
    ```sh
    kustomize build . | kubectl apply -f -
    ```
1. Add proper labels
    ```sh
    kubectl label ns my-profile istio.io/dataplane-mode=ambient
    kubectl label ns my-profile istio-injection-
    kubectl label ns my-profile istio.io/use-waypoint=waypoint
    kubectl label ns my-profile istio.io/ingress-use-waypoint=true
    ```
1. If profile-controller has been configured, then delete previous APs
    ```sh
    kubectl delete ap -n my-profile ns-owner-access-istio
    kubectl delete ap -n my-profile ml-pipeline-visualizationserver
    ```
