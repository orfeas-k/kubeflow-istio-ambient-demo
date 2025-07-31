# kubeflow-istio-ambient-demo
This is a Proof Of Concept for integrating Istio ambient in Kubeflow.

## Use cases
The following use cases are covered from the migration:
1. User login flow (use-case-a directory)
2. Web application access through the UI. User can see their notebooks in jupyter web app. (use-case-a directory)
3. KFP components access from a user’s pods. User can run pipelines successfully (use-case-b directory).
4. Programmatic access to KFP api from outside the cluster. User is able to list KFP objects. (use-case-c directory)
5. Programmatic access to user workloads from outside the cluster. User is able to curl their inference service. (use-case-c directory)
6. KFP components that allow all requests are reachable from user pods. User can access Minio artifacts. (use-case-c directory)
7. Services in the user namespace accept requests from: (use-case-c directory)
    1. Everyone in the same namespace
    1. Ingress gateway and KFP-UI with specific headers.
    1. Notebook controller to specific paths
    1. Anyone to specific paths (e.g. Prometheus)

## Instructions
A PoC can be deployed using instructions in the repo. Due to the missing changes in code, manifests need to be applied step by step following the instructions in each directory. Instructions should be followed from the directories in alphabetical order. 

1. clean-setup
2. use-case-a
3. use-case-b
4. use-case-c
