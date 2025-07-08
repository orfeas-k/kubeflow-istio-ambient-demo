# Use case a - Web application access through the UI

Those manifests are needed to enable:
1. Log in flow through the UI.
2. Access a web application through the UI (jupyter-web-app) in this case.

## Requirements
Note that this assumes starting from a [clean setup](../clean-setup/).

## Result
For more information on the final authorization setup, refer to https://github.com/canonical/bundle-kubeflow/issues/1285#issuecomment-3036555144.

### mTLS: strict
The file `strict-pa-kubeflow.yaml` is not needed. It was applied to test the setup's compatibility with strict mTLS mode.
