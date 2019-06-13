# Expose the service mesh with the Istio Ingress Gateway

The components deployed on the service mesh by default are not exposed outside the cluster. External access to individual services so far has been provided by creating an external load balancer or node port on each service.

An Ingress Gateway resource can be created to allow external requests through the Istio Ingress Gateway to the backing services.

## Expose the Guestbook app with Ingress Gateway

1. Configure the guestbook default route with the Istio Ingress Gateway. The `guestbook-gateway.yaml` file is in this repository (istio101) in the `plans` directory. If you are in the directory from following the instructions in the previous lab, run:

    ```shell
    cd ../../istio101/plans
    ```

    Then create the ingress resource:

    ```shell
    kubectl create -f guestbook-gateway.yaml
    ```

2. Get the **EXTERNAL-IP** of the Istio Ingress Gateway.

    ```shell
    kubectl get service istio-ingressgateway -n istio-system
    ```

    Output:

    ```shell
    NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
    istio-ingressgateway   LoadBalancer   172.21.254.53    169.6.1.1       80:31380/TCP,443:31390/TCP,31400:31400/TCP    1m  
    ```

3. Make note of the external IP address that you retrieved in the previous step, as it will be used to access the Guestbook app in later parts of the course. You can create an environment variable called $INGRESS_IP with your IP address.

    Example:

    ```shell
    export INGRESS_IP=169.6.1.1
    ```

Open up that IP in a browser, you should see your guestbook.

Congratulations! You exposed guestbook via the ingress resource!

### [Continue to expore the traffic management features of Istio](../traffic-management/README.md)

## References:

[Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

[Istio Ingress](https://istio.io/docs/tasks/traffic-management/ingress.html)

