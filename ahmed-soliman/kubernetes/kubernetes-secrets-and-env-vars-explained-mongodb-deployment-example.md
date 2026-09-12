# Kubernetes Secrets & Env Vars Explained: MongoDB Deployment Example

#### Env Variables

* **What are env vars?**
  * Like normal variables, they have a **name** and **value**.
  * Defined at the **OS level** or **container level** (not process level).
  * Processes read them when needed.
  * They **don’t live inside the process’s source code**.
* **How Kubernetes handles them:**
  * You define env vars in your Pod spec.
  * Kubernetes injects them into the container.
  * The application reads them at runtime.

#### Security Practices

* **Do not** inject secrets directly into:
  * Your code
  * Dockerfiles
* **Solution:** Use **Secrets** or **ConfigMaps**.

#### Example Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
```

* **Explanation:**
  * Two environment variables are defined at the container level.
  * `Key = value`
    * `MONGO_INITDB_ROOT_USERNAME` → value comes from Secret `mongodb-secret`, key `mongo-root-username`.
    * `MONGO_INITDB_ROOT_PASSWORD` → value comes from Secret `mongodb-secret`, key `mongo-root-password`.

***

#### How MongoDB Uses These Variables

* MongoDB **entrypoint script** runs first when the container starts.
* The script checks if **MONGO\_INITDB\_ROOT\_USERNAME** and **MONGO\_INITDB\_ROOT\_PASSWORD** exist.
* If both exist and the data directory is empty:
  * The script launches `mongod`.
  * Creates a root user in the admin database using these values.
  * Then restarts `mongod`.
* Kubernetes **does not create these variables** inside MongoDB — it only sets them in the container environment.

***

#### Secrets YAML Example

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
type: Opaque
data:
  mongo-root-username: dXNlcm5hbWU=   # base64 of username
  mongo-root-password: cGFzc3dvcmQ=   # base64 of password
```

* **Advantages:**
  * Avoid hardcoding credentials in scripts.
  * Secure storage and controlled access.

***

#### How to Secure Secrets

1. **Restricted access:**
   * Use RBAC to allow only necessary pods, users, or service accounts to read them.
2. **Encryption at rest:**
   * Enable etcd encryption to store Secrets and ConfigMaps encrypted on disk.
3. **External secret managers (optional):**
   * AWS Secrets Manager, HashiCorp Vault, etc.
   * Provides:
     * Central storage
     * Least-privilege access
     * Dynamic injection at runtime
     * Automatic rotation and auditing

#### What Kubernetes Secrets/ConfigMaps Can Do

* Persistent files stored in **etcd**
* Injected as **environment variables** or **mounted volumes**
* Referenced in Deployment YAML
* Restricted with RBAC
* Optionally encrypted at rest

**Limitations:**

* Static by default — must manually update if a value changes
* Pods don’t automatically reload — running pods keep old values
* Cluster-level only — no built-in central management across clusters
* No automatic rotation — requires scripts or operators
