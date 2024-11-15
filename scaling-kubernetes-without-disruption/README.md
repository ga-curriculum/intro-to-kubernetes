<h1>
  <span class="headline">Intro to Kubernetes</span>
  <span class="subhead">Scaling Kubernetes without Disruption</span>
</h1>

**Learning Objective:** By the end of this lesson, students will be able to explain how Kubernetes uses services and load balancing to manage traffic, maintain high availability, and minimize disruptions during scaling and pod failures.

## Scaling without Disruption

When Kubernetes adds or removes pods to scale your application, you might wonder: **How does it do this without disrupting users?**

For example, what happens if a customer is in the middle of a request when Kubernetes decides to shut down the container handling it? The answer lies in Kubernetes' **services** and **load balancing** capabilities.

## Services: Kubernetes' traffic manager

A **_service_** in Kubernetes is responsible for routing traffic within your cluster. It acts as a stable endpoint for your application and ensures traffic is balanced across healthy pods.

### Let’s break it down with an example:

Suppose your application is an API accessible at `api.mycompany.com`. This API is linked to a Kubernetes service called **API**.

Imagine you have a deployment that runs three pods for the API. Kubernetes assigns each pod a unique internal IP address. For instance:

- `10.0.0.1`
- `10.0.0.2`
- `10.0.0.3`

Each pod hosts the API on port `5000`. The Kubernetes service tracks these IPs in its routing table like this:

| IP       | Port |
| -------- | ---- |
| 10.0.0.1 | 5000 |
| 10.0.0.2 | 5000 |
| 10.0.0.3 | 5000 |

When a request is sent to `api.mycompany.com`, the service routes it to one of these pods. This distribution ensures traffic is evenly spread across the pods.

## Handling failures gracefully

Let’s say something goes wrong with one of the pods. For example, the container at `10.0.0.3` develops a memory leak, slowing it down and causing requests to fail.

Here’s what Kubernetes does to handle this:

1. **Detects the Issue**  
   Kubernetes notices the failing pod through its **liveness health check**.

2. **Stops Routing Traffic**  
   Kubernetes begins draining existing HTTP connections to `10.0.0.3`. No new requests are sent there.

3. **Updates the Routing Table**  
   The service now routes traffic only to the healthy pods (`10.0.0.1` and `10.0.0.2`):

   | IP                     | Port               |
   | ---------------------- | ------------------ |
   | 10.0.0.1               | 5000               |
   | 10.0.0.2               | 5000               |
   | ~~10.0.0.3~~ (Deleted) | ~~5000~~ (Deleted) |

4. **Starts a Replacement Pod**  
   Kubernetes spins up a new pod to replace the failing one. Once the new pod is ready and passes its **readiness health check**, it is added to the routing table:

   | IP       | Port |
   | -------- | ---- |
   | 10.0.0.1 | 5000 |
   | 10.0.0.2 | 5000 |
   | 10.0.0.4 | 5000 |

5. **Shuts Down the Unhealthy Pod**  
   Kubernetes sends a polite shutdown signal (`SIGTERM`) to the failing container, giving it a chance to exit gracefully. If it doesn’t shut down in time, Kubernetes forces termination (`SIGKILL`) to free up resources.

## Why It Matters

By using services and load balancing, Kubernetes ensures your application remains available, even when scaling or handling failures. It minimizes disruptions for your users by seamlessly managing traffic and replacing unhealthy pods with new ones. This allows Kubernetes to maintain high availability and reliability for your applications.
