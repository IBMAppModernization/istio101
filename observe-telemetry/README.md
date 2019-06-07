# Observe service telemetry: metrics and tracing

### Challenges with microservices

We all know that microservice architecture is the perfect fit for cloud native applications and it increases the delivery velocities greatly. Envision you have many microservices that are delivered by multiple teams, how do you observe the the overall platform and each of the service to find out exactly what is going on with each of the services?  When something goes wrong, how do you know which service or which communication among the few services are causing the problem?

### Istio telemetry

Istio's tracing and metrics features are designed to provide broad and granular insight into the health of all services. Istio's role as a service mesh makes it the ideal data source for observability information, particularly in a microservices environment. As requests pass through multiple services, identifying performance bottlenecks becomes increasingly difficult using traditional debugging techniques. Distributed tracing provides a holistic view of requests transiting through multiple services, allowing for immediate identification of latency issues. With Istio, distributed tracing comes by default. This will expose latency, retry, and failure information for each hop in a request.

You can read more about how [Istio mixer enables telemetry reporting](https://istio.io/docs/concepts/policy-and-control/mixer.html).

### Configure Istio to receive telemetry data

1. Verify that the Grafana, Prometheus, Kaili and istio-tracing add-ons were installed successfully. All add-ons are installed into the `istio-system` namespace.

    ```shell
    kubectl get pods -n istio-system
    kubectl get services -n istio-system
    ```

2. Configure Istio to automatically gather telemetry data for services that run in the service mesh.

    a. Create a rule to collect telemetry data.
    ```shell
    kubectl create -f guestbook-telemetry.yaml
    ```

3. Obtain the guestbook endpoint to access the guestbook.

    a. You can access the guestbook via the external IP for your service as guestbook is deployed as a load balancer service. Get the EXTERNAL-IP of the guestbook service via output below:

    ```shell
    kubectl get service guestbook -n default
    ```

    Go to this external ip address in the browser to try out your guestbook.


4. Generate a small load to the app.

    ```shell
    while sleep 0.5; do curl http://<guestbook_endpoint/; done
    ```

## View guestbook telemetry data

#### Jaeger

1. Patch the existing tracing service to change it from a `ClusterIP` to a `NodePort` type.

    ```shell
    kubectl patch svc tracing --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]' -n istio-system
    ```
2. Find the port to access the service

    ```shell
    $ kubectl get svc tracing -n istio-system
    NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    tracing   NodePort   172.21.36.204   <none>        80:32075/TCP   1d
    ```
    
    In this example, the port is 32075.
    
3. Find the host to access the service

    ```shell
    $ ibmcloud cs workers <cluster_name>
    OK
    ID                                                 Public IP       Private IP    Machine Type         State    Status   Zone    Version   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w1   169.61.73.131   10.191.9.76   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w2   169.61.73.142   10.191.9.71   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547
    ```
    
    Combine one of the public IPs and the port together to access the service. For example: `169.61.73.131:32075`
    
3. From the **Services** menu, select either the **guestbook** or **analyzer** service.
4. Scroll to the bottom and click on **Find Traces** button to see traces.

#### Grafana

1. Patch the existing tracing service to change it from a `ClusterIP` to a `NodePort` type.

    ```shell
    kubectl patch svc grafana --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]' -n istio-system
    ```
    
2. Find the port to access the service

    ```shell
    $ kubectl get svc grafana -n istio-system
    NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    grafana   NodePort   172.21.36.204   <none>        80:32075/TCP   1d
    ```
    
    In this example, the port is 32075.
    
3. Find the host to access the service

    ```shell
    $ ibmcloud cs workers <cluster_name>
    OK
    ID                                                 Public IP       Private IP    Machine Type         State    Status   Zone    Version   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w1   169.61.73.131   10.191.9.76   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w2   169.61.73.142   10.191.9.71   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547
    ```
    
    Combine one of the public IPs and the port together to access the service. For example: `169.61.73.131:32075`
    
4. Navigate to the Istio Mesh Dashboard by clicking on the Home menu on the top left.

#### Prometheus

1. Patch the existing tracing service to change it from a `ClusterIP` to a `NodePort` type.

    ```shell
    kubectl patch svc prometheus --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]' -n istio-system
    ```
2. Find the port to access the service

    ```shell
    $ kubectl get svc prometheus -n istio-system
    NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    prometheus   NodePort   172.21.36.204   <none>        80:32075/TCP   1d
    ```
    
    In this example, the port is 32075.
    
3. Find the host to access the service

    ```shell
    $ ibmcloud cs workers <cluster_name>
    OK
    ID                                                 Public IP       Private IP    Machine Type         State    Status   Zone    Version   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w1   169.61.73.131   10.191.9.76   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w2   169.61.73.142   10.191.9.71   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547
    ```
    
    Combine one of the public IPs and the port together to access the service. For example: `169.61.73.131:32075`
    
4. In the “Expression” input box, enter: `istio_requests_total{destination_service='guestbook.default.svc.cluster.local', destination_version='2.0'}`. Click Execute.

#### Kiali

Kiali is an open-source project that installs on top of Istio to visualize your service mesh. It provides deeper insight into how your microservices interact with one another, and provides features such as circuit breakers and request rates for your services.

1. Patch the existing tracing service to change it from a `ClusterIP` to a `NodePort` type.

    ```shell
    kubectl patch svc kiali --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]' -n istio-system
    ```
2. Find the port to access the service

    ```shell
    $ kubectl get svc kiali -n istio-system
    NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    kiali   NodePort   172.21.36.204   <none>        80:32075/TCP   1d
    ```
    
    In this example, the port is 32075.
    
3. Find the host to access the service

    ```shell
    $ ibmcloud cs workers <cluster_name>
    OK
    ID                                                 Public IP       Private IP    Machine Type         State    Status   Zone    Version   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w1   169.61.73.131   10.191.9.76   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547   
    kube-wdc07-cr1b3398b985d84e9b8e9544a91d61428a-w2   169.61.73.142   10.191.9.71   b2c.4x16.encrypted   normal   Ready    wdc07   1.11.8_1547
    ```
    
    Combine one of the public IPs and the port together and add `/kiali` to access the service. For example: `169.61.73.131:32075/kiali`
    

4. Click on the web preview icon and select port 8084 to access the Kiali dashboard. Login with the following username/password: admin/admin.

5. Click the "Graph" tab on the left side to see the a visual service graph of the various services in your Istio mesh. You may need to select a namespace in the dropdown.

6. You can see request rates as well by clicking the "Edge Labels" tab and choosing "Traffic rate per second".

Kiali has a number of views to help you visualize your services. Click through the various tabs to explore the service graph, and the various views for workloads, applications and services.


## Questions

1. Does a user need to modify their app to get metrics for their apps?   A: 1. Yes 2. No. (2 is correct)

2. Does a user need to modify their app to get distributed tracing for their app to work properly? A: 1. Yes 2. No. (1 is correct)

3. What distributed tracing system does Istio support by default?  A: 1. Zipkin 2. Kibana 3. LogStash 4. Jaeger. (1 and 4 are correct)

#### [Continue to enforce policies for microservices](../enforce-policies/README.md)

