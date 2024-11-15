<h1>
  <span class="headline">Intro to Kubernetes</span>
  <span class="subhead">Kubernetes Core Features and Limitations</span>
</h1>

**Learning Objective:** By the end of this lesson, students will be able to identify the core features of Kubernetes and recognize its limitations.

## Key features of Kubernetes

Kubernetes provides several powerful features that help manage and scale containerized applications. Here’s a look at the essential capabilities Kubernetes offers, as well as what it doesn’t do.

### Service discovery and DNS

Kubernetes allows internal and external DNS configuration to route traffic to services. Inside the cluster, containers and services can communicate directly using internal domain names, bypassing external DNS. This setup is essential for microservices, where different parts of an application need to communicate seamlessly.

### Load balancing

Kubernetes balances incoming traffic across the cluster to prevent any container or node from being overloaded. As containers are added or removed, Kubernetes automatically adjusts traffic routing, ensuring smooth operation without disruption.

### Fault tolerance

Kubernetes monitors the health of containers and replaces unresponsive or unhealthy ones. This keeps applications available, even if individual containers experience issues.

### High availability

Kubernetes is self-healing. It only directs traffic to healthy containers. If a container fails or becomes unresponsive, it’s temporarily removed from circulation. If it recovers, it’s brought back; if not, it’s replaced. This approach ensures high availability for applications.

### Provisioning

Kubernetes uses your “desired cluster state” to automatically start, stop, and manage containers. Scaling is simple—just specify your desired state, and Kubernetes will adjust container numbers accordingly.

### Networking

Kubernetes manages all networking within the cluster. It creates networks, subnets, and assigns IP addresses, allowing Kubernetes to efficiently route traffic and maintain cluster connectivity.

### Bin packing

Kubernetes efficiently schedules containers based on available resources, similar to solving a ["bin packing problem."](https://en.wikipedia.org/wiki/Bin_packing_problem) By packing containers onto nodes efficiently, Kubernetes maximizes the use of your infrastructure’s processing power.

<br>

<img src="https://cloud.google.com/kubernetes-engine/kubernetes-comic/assets/panel-34.png" alt="Deploy code faster: with CI/CD and Kubernetes" style="width:700px;"/>

<br>

[source](https://cloud.google.com/kubernetes-engine/kubernetes-comic)

> A bin packing problem is an [optimization problem](https://en.wikipedia.org/wiki/Optimization_problem), in which items of different sizes must be packed into a finite number of bins or containers, each of a fixed given capacity, in a way that minimizes the number of bins used

### Scaling

Scaling is easy: add nodes to increase capacity or specify the desired number of container instances, and Kubernetes will handle the rest. Scaling up or down is managed automatically based on your requirements.

### Internal DNS

Kubernetes provides its own internal DNS, allowing containers to communicate with each other within the cluster without relying on external DNS. This is particularly useful for microservices that need frequent, reliable communication.

### Configurability and pluggability

Kubernetes is built to be flexible. You can integrate external components, such as hardware load balancers, and configure them to work natively within your cluster.

### Secrets management

Kubernetes securely manages encrypted secrets (API keys, credentials) that applications may need, keeping them safe and accessible only when needed.

## What Kubernetes doesn’t do

While Kubernetes offers extensive features for container management, there are certain things it’s intentionally not designed to handle:

### Logging

Kubernetes doesn’t manage application logs. By default, most containers log to standard output (stdout). For log storage, searching, and analysis, you’ll need to integrate an external logging solution (ex: [Datadog](https://www.datadoghq.com/), [Papertrail](https://www.papertrail.com/)) to handle this.

### Metrics

Kubernetes doesn’t track metrics on its own. You’ll need external tools like [Graphite](https://graphiteapp.org/) or [Grafana](https://grafana.com/) to collect and visualize metrics. Kubernetes can work with these tools through service discovery and custom configurations, but they aren’t included by default.

### Message bus

Kubernetes doesn’t offer a message bus or queue service like cloud providers do (ex: [Azure](https://azure.microsoft.com/en-us/), [AWS](https://aws.amazon.com/) or [Google](https://cloud.google.com/)). You’ll need to integrate a separate messaging system if required.

### Additional services (Monitoring, Alerting, Databases, Storage)

Kubernetes doesn’t handle alerting, monitoring, database management, or storage on its own. It focuses specifically on container orchestration, leaving these other functions to specialized tools or cloud providers.
