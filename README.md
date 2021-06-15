# wirepact-poc

Proof of Concept for WirePact (Distributed Authentication Mesh).
Please find the project report at https://buehler.github.io/mse-project-thesis-1/.

The case study to WirePact consists of:

- The WirePact POC operator
- An application with three parts ("Frontend", "Modern Backend", "Legacy Backend")

## TL;DR

To install the case study, perform the following steps:

- Get a Kubernetes instance (Docker Desktop with Kubernetes / minikube)
- Install `kustomize` and `kubectl` executable
- Install Ambassador in the Kubernetes cluster with `./Kubernetes/case-study/install-ambassador.sh`
- Execute in bash:
  ```bash
    cd Kubernetes
    kustomize build | kubectl apply -f -
  ```
- Access the frontend via "https://localhost"
  (or "https://kubernetes.docker.internal", "https://kubernetes.local depeding on your configuration)
  when everything is done.

## Install and Run the Case Study

To install the case study, you'll need a Kubernetes cluster.
Under the assumption, that this will be tested on a local machine,
please install the appropriate Kubernetes engine for your machine.
In case of Windows and MacOS, this can be achieved via Docker Desktop
and then enable Kubernetes. On Linux, a local Kubernetes instance
can be created with [minikube](https://minikube.sigs.k8s.io/docs/start/).
Furthermore, [Kustomize](https://kustomize.io/) is needed for the installation.

### Install the POC in one step

If you want to install the whole application (operator and case study)
in one go, you can do so with the `kustomization.yaml` in the
`Kubernetes` folder. Be aware, that you'll need to install Ambassador
as well with the provided `./Kubernetes/case-study/install-ambassador.sh`
script. The further sections describe the effective
installation of the operator and the demo application separatly.

### Install the Operator

The operator is located in the [poc-operator repository](https://github.com/WirePact/poc-operator).
It watches for `Deployments` and `Services`. If such a resource is created, the
operator does check for the specific annotations that WirePac (POC quality) uses.
Afterwards, the needed sidecars are injected.

To install the operator, install it with the corresponding
`kustomization.yaml` file in `./Kubernetes/operator`.

```bash
cd Kubernetes/operator
kustomize build | kubectl apply -f -
```

After the stated command, the following log messages
should be visible:

```bash
namespace/wirepact-poc-operator created
clusterrole.rbac.authorization.k8s.io/wirepact-poc-operator-operator-role created
clusterrolebinding.rbac.authorization.k8s.io/wirepact-poc-operator-operator-role-binding created
deployment.apps/wirepact-poc-operator-operator created
```

You now have the operator installed.

### Install the Application

The demo application is located in the
[poc-showcase-app](https://github.com/WirePact/poc-showcase-app) repository.
It uses three Deployments. The first contains a simple frontend application,
the second is a "modern" service with OpenID Connect (OIDC) authentication
and the last one is a legacy service which is only capable of authenticating
users via Basic Authentication.

The case study installs the following parts:

- Ambassador API Gateway (to access the Frontend)
- Frontend application (with OIDC support)
- Modern API application (with OIDC support)
- Legacy API application (with Basic Auth support)
- The config for the modern API
- A Basic Authentication secret where the credentials are stored

To install the case study, first install [Ambassador](https://getambassador.io)
with the shell script in `./Kubernetes/case-study/install-ambassador.sh`.

Afterward, use kustomize to install the application in the `Kubernetes/case-study`
folder:

```bash
cd Kubernetes/case-study
kustomize build | kubectl apply -f -
```
