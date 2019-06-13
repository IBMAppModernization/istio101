# Beyond the Basics: Istio and IBM Cloud Kubernetes Service

[Istio](https://www.ibm.com/cloud/info/istio) is an open platform to connect, secure, control and observe microservices, also known as a service mesh, on cloud platforms such as Kubernetes in IBM Cloud Kubernetes Service and VMs. With Istio, You can manage network traffic, load balance across microservices, enforce access policies, verify service identity, secure service communication and observe what exactly is going on with your services.

In this course, you can see how to install Istio alongside microservices for a simple mock app called [Guestbook](https://github.com/IBM/guestbook). When you deploy Guestbook's microservices into an IBM Cloud Kubernetes Service cluster where Istio is installed, you can choose to inject the Istio Envoy sidecar proxies in the pods of certain microservices.

## Objectives

After you complete this course, you'll be able to:

- Deploy the Guestbook sample app
- Set up the Istio Ingress Gateway
- Perform simple traffic management, such as A/B tests and canary deployments
- Use metrics, logging and tracing to observe services
- Enforce policies for your microservices

## Setup

1) The instructions for this workshop assume you have a paid cluster from the IBM Kubernetes Service. If following this lab as part of an IBM instructor led workshop, a paid cluster will be provided to you. 

1) This lab uses the guestbook application. If you don't already have a copy of the guestbook application, you can get a copy by cloning the repo:

    ```sh
    git clone https://github.com/IBM/guestbook.git
    ```

1) In the parent directory where you have a copy of the guestbook application, clone this repository.

    ```sh
    git clone https://github.com/IBMAppModernization/istio101.git
    ```

1) If you have guestbook installed on your cluster from a previous lab, you may need to uninstall it.

    ```sh
    kubectl delete deploy guestbook-v1
    kubectl delete service guestbook
    ```

The Kubernetes cluster we have provided for the workshop has been installed with Istio in advanced.

## First, install the Guestbook App to your Kubernetes Cluster

- [Deploying Guestbook with Istio Proxy](deploy-guestbook/README.md)

## Next, expose your app using the Istio Ingress Gateway

- [Expose the service mesh with the Istio Ingress Gateway](create-ingress-gateway/README.md)

## Learn about the features of Istio

- [Perform traffic management](traffic-management/README.md)
- [Observe service telemetry: metrics and tracing](observe-telemetry/README.md)
- [Enforce policies for microservices](enforce-policies/README.md)
