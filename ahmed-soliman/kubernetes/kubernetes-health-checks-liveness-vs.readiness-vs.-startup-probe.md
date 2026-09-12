# Kubernetes Health Checks: Liveness vs.Readiness vs. Startup Probe

&#x20;

* A Pod can keep running forever even if the application inside is broken.
* Kubernetes can restart a Pod for several reasons even if the application inside has errors.
* Application errors, like an API endpoint not responding or a port not accepting connections, **will not stop the container from running**.
* **Liveness probe = auto-restart mechanism (self-healing).**
* Without monitoring, the same problem will repeat endlessly and you won’t know why.
* If the liveness probe fails (the HTTP request or whatever check it performs fails), Kubernetes will **restart the container inside the Pod**.
* For liveness probes: **only the container restarts**, the Pod itself stays the same.

***

#### How Liveness Probe Works

* **Purpose:** To check the health of the application running inside a container in a Pod.
* **How it checks:**
  * Periodically sends an HTTP request, TCP check, or runs a command inside the container.
  * Determines whether the app is responding correctly.
* **If the app is unhealthy:**
  * Probe fails for `failureThreshold` consecutive checks.
  * Kubernetes restarts the container inside the same Pod.
* **Important:**
  * The Pod object itself does not restart — only the container is restarted.
  * If the app continues failing, Kubernetes keeps restarting the container repeatedly.
  * The liveness probe only detects that the application is unhealthy.
  * When it fails, Kubernetes restarts the container, giving the app a chance to recover.
  * It does not fix the underlying problem in the application itself (e.g., bug, memory leak, infinite loop).
  * Liveness probes combined with logging and monitoring alert you that the application needs fixing, rather than just hiding the problem with automatic restarts.

***

#### Readiness Probe

* **Purpose:** Tells Kubernetes whether a Pod is ready to accept traffic.
* If the probe fails:
  * The Pod stays running, but Kubernetes **stops sending traffic to it**.
* This is different from a liveness probe, which restarts the pod if it fails.
* Kubernetes does not just check if the container responds, it checks the **HTTP status code** returned by the endpoint:
  * Success: HTTP 200–399 → Pod is marked ready
  * Failure: HTTP ≥400 or no response → Pod is marked not ready
* **So:**
  * If your endpoint responds `200 OK` → Pod is ready, traffic goes to it.
  * If your endpoint responds `500 Internal Server Error` → Pod is not ready, traffic is not sent.
  * If it doesn’t respond at all → Pod is not ready
