# Gpu-K8s-Cluster-Amd-Nvidia

Notes and example manifests for enabling GPU workloads on Kubernetes with NVIDIA (and, eventually, AMD) devices.

## Overview

| Path | Status |
|------|--------|
| [`nvidia/readme.md`](./nvidia/readme.md) | Guide: NVIDIA GPU access in a Kind cluster (container toolkit, Kind config, GPU Operator, CUDA test pod) |
| [`nvidia/time-sliceing/`](./nvidia/time-sliceing) | GPU time-slicing: ConfigMap (`replicas: 4`), cluster-policy patch command, example pods |
| [`nvidia/kubeai-models.yaml`](./nvidia/kubeai-models.yaml) | KubeAI model catalog snippet (CPU models via Ollama) |
| [`amd/readme.md`](./amd/readme.md) | Placeholder, contains only the word "amd" (TODO) |

## Prerequisites

- A machine with an NVIDIA GPU and drivers
- Docker, [Kind](https://kind.sigs.k8s.io/), `kubectl`, Helm
- NVIDIA Container Toolkit

## Quick start (NVIDIA on Kind)

Follow [`nvidia/readme.md`](./nvidia/readme.md): configure the Docker runtime, create the Kind cluster, install the GPU Operator with `helm install ... nvidia/gpu-operator --set driver.enabled=false`, then verify with the `cuda-vectoradd` pod.

## Usage: GPU time-slicing

Names below come from the files in `nvidia/time-sliceing/`.

```bash
kubectl apply -f nvidia/time-sliceing/time-slicing.yaml     # ConfigMap time-slicing-config in gpu-operator
kubectl patch clusterpolicies.nvidia.com/cluster-policy \
  -n gpu-operator --type merge \
  -p '{"spec": {"devicePlugin": {"config": {"name": "time-slicing-config", "default": "any"}}}}'
kubectl apply -f nvidia/time-sliceing/example-pod.yaml      # four pods, each requesting nvidia.com/gpu: 1
```

The ConfigMap advertises one physical GPU as 4 `nvidia.com/gpu` slots. These steps have not been verified end to end (TODO).

## Structure

```
.
├── amd/readme.md
└── nvidia/
    ├── readme.md
    ├── kubeai-models.yaml
    └── time-sliceing/{readme.md,time-slicing.yaml,example-pod.yaml}
```

## Known issues

- `nvidia/readme.md` contains a duplicated, partly unformatted copy of the guide above its cleaned-up version; the directory name `time-sliceing` is misspelled. Both left as is.

## License

Released under the [MIT License](./LICENSE).

## Author

Umesh Khatiwada, https://github.com/umesh-khatiwada
