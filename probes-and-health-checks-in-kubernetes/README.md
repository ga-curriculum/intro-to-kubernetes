<h1>
  <span class="headline">Intro to Kubernetes</span>
  <span class="subhead">Probes and Health Checks in Kubernetes</span>
</h1>

**Learning objective:** By the end of this lesson, students will understand how Kubernetes uses container probes (health checks) to monitor and maintain container health, and the types of probes available.

## Healthy or Unhealthy: How can Kubernetes tell?

Kubernetes claims it can figure out if a container is healthy or unhealthy—but what does that actually mean? How does it know if your API can't connect to its database or is returning HTTP 500 errors to customers?

Kubernetes uses **container probes**, also called **health checks**, to monitor container health.

When configured, Kubernetes periodically sends requests to specific HTTP endpoints in your application.

Then it can act based on the information it receives:

- If the response is satisfactory (like a 200 OK status), Kubernetes keeps the container running.
- If the response is a failure (non-2XX status), takes too long, or causes an error, Kubernetes may terminate the pod and start a new one.

These checks are also configurable. For example, you can tell Kubernetes to terminate a pod only after it fails three consecutive health checks.

## Health checks don’t fix bad code

It’s important to understand that health checks don’t fix broken applications. Kubernetes doesn’t debug or repair your code—it simply helps keep things running as smoothly as possible by restarting containers that aren’t working.

This behavior helps maintain high availability and minimizes problems caused by caused by temporary issues, like network interruptions, memory leaks, or excessive CPU use. Basically, Kubernetes gives your application a “turn it off and on again” treatment, helping the cluster heal itself.

## Types of container probes

Kubernetes performs three types of probes to check container health: **Startup**, **Readiness**, and **Liveness**.

Each has a specific role:

### 1. Startup probes

- **Purpose:** Used for applications that take a long time to start (ex: legacy systems).
- **Actions performed:**
  - Kubernetes keeps retrying the startup probe until it succeeds.
  - Once successful, readiness and liveness probes take over.
  - If the startup probe fails within a set time, Kubernetes restarts the pod.

### 2. Readiness probes

- **Purpose:** Indicates when a container is ready to handle traffic.
- **Actions performed:**
  - Readiness probes run throughout the pod’s lifecycle to make sure it stays ready.
  - If a container fails a readiness probe, Kubernetes removes the pod from the load balancer.
- **Common Use Case:** Checking external dependencies, like database connections or APIs, to make sure the application is fully functional.

### 3. Liveness probes

- **Purpose:** Detects when a container is no longer healthy and needs to be restarted.
- **Actions performed:**
  - If a liveness probe fails, Kubernetes terminates the pod and starts a new one.
- **Common Use Case:** Handling issues like hanging processes or deadlocks.

## Probe implementation types

Kubernetes provides three ways to perform health checks with probes, depending on the application:

### 1. ExecAction

- Runs a command inside the container.
- If the command returns an exit code of `0`, the container is considered healthy.

**Example:** Useful for services running shell scripts or cron jobs.

### 2. HTTPGetAction

- Makes an HTTP request to a container endpoint.
- If the response is a 2XX or 3XX status code, the container is healthy.

**Example:** Used for APIs or web applications to make sure they’re responsive.

### 3. TCPSocketAction

- Attempts to establish a TCP connection to a specific port on the container.
- If the connection succeeds, the container is healthy.

**Example:** Ideal for applications like mail servers or databases.

## What exactly _is_ a probe?

A probe is simply a set of instructions that tells Kubernetes how to check if a container is healthy. These instructions define:

- **What to check:** A command to run, an HTTP endpoint to ping, or a port to connect to.
- **When to check:** How frequently Kubernetes should run the check.
- **What to do if the check fails:** Whether to restart the pod or take other actions.

Probes help Kubernetes decide whether a container is ready to handle traffic or needs to be replaced to maintain a healthy cluster.

## Example: Liveness Probe with ExecAction

Here’s an example of a **liveness probe** defined in a YAML file. This configuration runs a command inside the container to check its health. If the command fails, Kubernetes restarts the pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
    - name: liveness
      image: k8s.gcr.io/busybox
      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 5
```

[source](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

## Additional Resources

- [How Complex Systems Fail](https://queue.acm.org/detail.cfm?id=2898444)
- [Google Cloud Kubernetes Comic](https://cloud.google.com/kubernetes-engine/kubernetes-comic)
