== Health Probe

ifndef::skipInstall[]
We are using a Minikube installation to run the examples. For details, please refer to the link:../../INSTALL.adoc#minikube[installation instructions].
endif::skipInstall[]

Here we are reusing our https://github.com/k8spatterns/random-generator[random-generator], which also includes support for health checks.

To apply a Deployment with liveness and readiness check enabled, use

[source, bash]
----
kubectl apply -f https://k8spatterns.io/HealthProbe/deployment.yml
----

This deployment introduces an artificial pause of 20 seconds before the application becomes ready.

[source, bash]
----
kubectl get pods -w
NAME                                READY   STATUS        RESTARTS   AGE
random-generator-5856b5f774-54h6b   0/1     Running       0          5s
random-generator-5856b5f774-54h6b   1/1     Running       0          38s
----

=== Liveness Probes

The example application exposes two endpoints with which you can manually switch the state of the readiness and liveness checks.
For simplicity reasons, we haven't installed a Service or Ingress (but of course, you are free to do so!)

Instead, we are using a simple port-forwarding directly to the Pod to trigger the toggles:

[source, bash]
----
kubectl port-forward deployment/random-generator 8080:8080 2>&1 &
----

Now you can switch on/off the readiness/liveness checks and see how the cluster manages your pods:

Check the liveness probe by querying the actuator:

[source, bash]
----
curl -s http://localhost:8080/actuator/health | jq .
----

Toggle liveness check to off:

[source, bash]
----
curl -s http://localhost:8080/toggle-live
----

Recheck the liveness probe:

[source, bash]
----
curl -s http://localhost:8080/actuator/health | jq .
----

Watch the pods and wait a bit. What happens after 2-3 mins?

[source, bash]
----
kubectl get pods -w
----