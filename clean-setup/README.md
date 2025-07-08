# Clean setup

This documents how to start from a clean setup in order to work on integrating Kubeflow with Istio ambient. The term "clean setup" means a Kubeflow deployment, where there is no authentication or authorization Istio custom resources affecting networking.

## Instructions

1. Ensure that Gateway API is installed as instructed in the [istio docs](https://istio.io/v1.25/docs/ambient/getting-started/#install-the-kubernetes-gateway-api-crds).
    ```shell
    kubectl get crd \
        gateways.gateway.networking.k8s.io &> /dev/null \
        || kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.3.0/standard-install.yaml
    ```
1. Install the Kubeflow example from this branch https://github.com/orfeas-k/kf-manifests/tree/integrate-istio-ambient-mesh-1.10.1-istio-1.26.2.
    ```
    while ! kustomize build example | kubectl apply --server-side --force-conflicts -f -; do echo "Retrying to apply resources"; sleep 20; done
    ```
    For more information on how those manifests were generated/modified, refer to the [Manifests Configuration](#manifests-configuration) section below.
1. Remove previous authorization-related istio resources:
    ```shell
    kubectl -n kubeflow delete authorizationpolicies.security.istio.io --all
    kubectl -n istio-system delete authorizationpolicies.security.istio.io --all
    kubectl -n kubeflow delete networkpolicies.networking.k8s.io --all
    kubectl -n knative-serving delete authorizationpolicies.security.istio.io --all

    # you should not need to remove those, but if there are any
    kubectl -n kubeflow delete gateways.networking.istio.io --all 
    kubectl -n istio-system delete gateways.networking.istio.io --all 
    kubectl -n knative-serving delete gateways.networking.istio.io --all 
    ```


Note that this assumes a working k8s cluster. 

### Manifests configuration
In order to manually generate the manifests provided at the [orfeas-k/kf-manifests/integrate-istio-ambient-mesh-1.10.1-istio-1.26.2](https://github.com/orfeas-k/kf-manifests/tree/integrate-istio-ambient-mesh-1.10.1-istio-1.26.2?tab=readme-ov-file#install-with-a-single-command) branch:
1. Install istioctl
	```shell
	wget https://github.com/istio/istio/releases/download/1.25.1/istioctl-1.25.1-linux-amd64.tar.gz \
		-O istioctl.tar.gz
	tar -xzvf istioctl.tar.gz
	mv istioctl ~/.local/bin
	rm istioctl.tar.gz
	```
2. Go to kf/manifests repo
	```shell
	git clone https://github.com/kubeflow/manifests
	cd manifests
	cd common/istio
	```
3. Prepare for generating istio manifests
	```shell
	source <venv-directory>/bin/activate
	pip install ruamel.yaml
	```
4. Generate Istio ambient manifests
	```shell
	istioctl manifest generate \
		--set profile=ambient \
		--set "values.global.platform=microk8s" \ # Update this based on your cluster if needed, otherwise remove
	> 	istio-ambient-manifests.yaml
	
	./split-istio-packages -f istio-ambient-manifests.yaml
	mv crd.yaml istio-crds/base
	mv install.yaml istio-install/base
	mv cluster-local-gateway.yaml cluster-local-gateway/base
	rm istio-ambient-manifests.yaml
	```
5. Use ambient for kubeflow namespace
	```shell
	cd ../../
	find common/kubeflow-namespace/base/namespace.yaml \
	-type f -exec sed -i \
	"s#istio-injection: enabled#istio.io/dataplane-mode: ambient#g" \
	{} +
	```
6. Ensure no sidecars are enforced
	```shell
	find . \
		-type f -exec sed -i \
		"s#sidecar.istio.io/inject: \"true\"#sidecar.istio.io/inject: \"false\"#g" {} +
    ```
