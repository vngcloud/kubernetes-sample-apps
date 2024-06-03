# Using Nvidia GPU with VKS clusters
- ***Author***: Cuong. Duong Manh
- ***Email***: cuongdm3@vng.com.vn
- ***Date***: 2024-06-03

<hr>

**Table of Contents**

- [1. Introduction](#1-introduction)



## 1. Introduction
- In this document, I want to focus on how to use Nvidia GPU with VKS clusters, including of:
  - Install `gpu-operator` of Nvidia on VKS clusters.



## 2. Install `gpu-operator` of Nvidia on VKS clusters
- In this guideline, I deployed a VKS cluster with the following configuration:
  - Kubernetes version `v1.29.1`.
  - 1 node with **1 Nvidia GPU RTX 2080Ti**.
  - And with **4 CPU cores and 16GB of RAM**.

- Let's have a quick glance about my cluster using:
  ```bash
  kubectl get nodes -owide
  ```

<center>

  ![](./images/01.png)

</center>

- Install Nvidia `gpu-operator` using the below commands (Helm 3+ is required):
  ```bash
  helm install nvidia-gpu-operator --wait --version v24.3.0 \
      -n gpu-operator --create-namespace \
      oci://vcr.vngcloud.vn/81-vks-public/vks-helm-charts/gpu-operator
  ```
<center>

  ![](./images/02.1.png)

</center>

- Until this moment, if your all action is correct, your cluster is lovely with Nvidia GPU. You can check the status of Nvidia GPU by the following command:
  ```bash
  kubectl -n gpu-operator get all
  ```

<center>

  ![](./images/03.png)

</center>

- The `gpu-operator` will relabels some pair of key-value to the node, you can check it by the following command:
  ```bash
  kubectl get nodes <gpu-node> -oyaml
  ```

<center>

  ![](./images/04.png)

</center>

- Using `helm list repo -A` to get the status of Nvidia GPU:
  ```bash
  helm list repo -A
  ```

<center>

  ![](./images/05.png)

</center>

- Use can using `nvidia-smi` on the node which is integrated with Nvidia GPU. The `gpu-operator` use `DaemonSet` to deploy the `nvidia-driver` to the node. You can check the status of `DaemonSet` by the following command:
  ```bash
  kubectl -n gpu-operator exec nvidia-driver-daemonset-<random-string> -- nvidia-smi 
  ```

<center>

  ![](./images/06.png)

</center>

  > **Note**: 
  > - **Multi-Instance GPU** (aka ***MIG***) is a greate feature of Nvidia GPU using Ampere architecture. But in this guideline, my node is using RTX 2080Ti.
  > - This page lists entire the Nvidia products that support MIG feature [https://docs.nvidia.com/datacenter/tesla/mig-user-guide/index.html](https://docs.nvidia.com/datacenter/tesla/mig-user-guide/index.html) _(ref 2024-06-03)_.
  > <center>
  > 
  >    ![](./images/07.png)
  > 
  > </center>