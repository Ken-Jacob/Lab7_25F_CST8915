# Lab 7 - CST8915 Full-stack Cloud-native Development: Introduction to Kubernetes Basics

Welcome to Lab 7 of the **CST8915 Full-stack Cloud-native Development** course. In this lab, you will explore the foundational concepts of Kubernetes, focusing on using `kubectl` for basic operations, understanding the structure of Kubernetes YAML configuration files, and creating essential Kubernetes resources such as Pods, Deployments, and Services.

> **Note:** This lab will incur an approximate cost of $3 to your Azure credit. Be sure to clean up your resources after completing the lab to avoid additional charges.


---

## Lab Objectives

1. Understand the basic structure of a Kubernetes YAML file.
2. Learn how to create Kubernetes objects using `kubectl` and YAML files.
---

## Introduction
You can create Kubernetes (k8s) resources using **`kubectl`** or using a **`YAML`** file. These are two different approaches to defining and deploying resources in a Kubernetes cluster.

### **`kubectl`**
**`kubectl`** is the command-line interface (CLI) tool for Kubernetes, allowing you to interact directly with the Kubernetes API. You can use it to create, modify, and manage resources in a cluster without needing a pre-written configuration file.

**Use **`kubectl`** commands when you:**
- Are **testing** or **experimenting** with Kubernetes resources.
- Need to **quickly** create or modify a small number of resources.
- **Don’t** require version control or reproducibility.

### **`YAML`**
**`YAML`** files offer a declarative approach to defining Kubernetes resources, allowing you to specify configuration files that can be reused, versioned, and easily shared. With a YAML file, you define the desired state of the resource, and then apply it to the cluster.

**Use YAML files when you:**
- Want **reproducibility**, **version control**, and **maintainability** of your resources.
- Are working on **complex deployments** or **configurations**.
- Need to **collaborate** with others or integrate into **CI/CD** processes.


## Step 1: Objects In Kubernetes
Read: [Objects In Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/)

## Step 2: Understanding Kubernetes YAML Files
In Kubernetes, resources are often defined and managed using YAML configuration files. YAML files enable you to declare the desired state of Kubernetes resources, such as Pods, Deployments, and Services, in a human-readable format. 

Let’s explore the basic components of a Kubernetes YAML file.

### Key Components of a Kubernetes YAML File

1. **`apiVersion`**:
   - Specifies the version of the Kubernetes API to use. The version depends on the type of resource being created.
   - Example: `apiVersion: v1`

2. **`kind`**:
   - Defines the type of Kubernetes resource you want to create, such as `Pod`, `Deployment`, or `Service`.
   - Example: `kind: Pod`

3. **`metadata`**:
   - Contains metadata information about the resource, like `name`, `namespace`, and `labels`, which help **uniquely** identify the resource within the cluster.
   - Example:
     ```yaml
     metadata:
       name: my-pod
       labels:
         app: my-app
     ```

4. **`spec`**:
   - Provides the detailed specification of the resource. The content within `spec` varies depending on the resource type.
   - For example, for a `Pod`, `spec` includes details about the containers, images, ports, and other configurations needed for the Pod.

### Example: Basic YAML Structure for a Pod

Here is a simple YAML configuration for creating a Pod with an NGINX container:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    ports:
    - containerPort: 80
```
#### Explanation of the YAML Example

- **`apiVersion: v1`:** Specifies that this Pod uses version `v1` of the Kubernetes API.
- **`kind: Pod`** Indicates that this file defines a Pod resource.
- **`metadata:`**
   - **`name: my-pod`**: Sets the name of the Pod to my-pod.
- **`spec`**:
   - **`containers`**: A list of containers to run inside the Pod.
      - **`name: my-container`**: The name of the container within the Pod.
      - **`image: nginx`**: Specifies the container image to use, in this case, nginx.
      - **`ports`**: Defines the network port that the container will expose, with **`containerPort: 80`** indicating that the container listens on port `80`.
---


### Applying the YAML File
To create the resource defined in a YAML file, use the following command:
```bash
kubectl apply -f <file-name>.yaml
```
This command instructs Kubernetes to create or update the resource based on the YAML file. YAML files provide an easy way to manage configurations, making it simple to reproduce, modify, and apply resource definitions across environments.

Run the following command to delete all resources defined in the YAML file.
```bash
kubectl delete -f <file-name>.yaml
``` 

## Step 3: Create Resources Using kubectl and YAML
This section will show you how to create kubernetes resources using kubectl command and YAML file.

### Create an Azure Kubernetes Cluster:
Create an AKS cluster with one worker node for this exercise.

### Create a Pod:
- Using **`kubectl`**:

   Run the following command to create a Pod directly
   ``` bash
   kubectl run my-pod --image=nginx --port=80
   ```

   Run the following command to delete this Pod:
   ```bash
   kubectl delete pods my-pod 
   ```
- Using **`YAML`**

   - Save the following Pod YAML as **`my-pod.yaml`**
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    ports:
    - containerPort: 80
```

- Run the following command to apply the YAML file:

   ```bash
   kubectl apply -f my-pod.yaml
   ```
- Verify Pod Creation:
   ```bash
   kubectl get pods
   ```
- Run the following command to delete the pod:
   ```bash
   kubectl delete -f my-pod.yaml
   ```

### Create a Deployment:
- Using **`kubectl`**:
   ``` bash
   kubectl create deployment my-deployment --image=nginx
   ```
   Run the following command to delete the deployment:
   
   ```bash
   kubectl delete deployments.apps my-deployment
   ```
- Using **`YAML`**
   - Create a file **`my-deployment.yaml`** with the following content:
``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

- This YAML file defines a Kubernetes Deployment resource named my-deployment using the apps/v1 API version. 
- The Deployment specifies that **three replicas** of the application should be running, ensuring that three identical Pods are deployed and maintained
- Each Pod created by this Deployment will match the label **`app: nginx`**, which helps in grouping and identifying these Pods. 
- The **template** section defines the configuration for each Pod, setting metadata labels and a specification that describes a single container. 
- The container, named `nginx`, uses the `nginx` image and exposes port `80` within the container, allowing traffic directed to this port. 
- This configuration enables Kubernetes to manage and maintain the desired state of the application, automatically handling scaling, rolling updates, and rescheduling Pods if needed.


- Run the following command to apply the YAML file:

   ```bash
   kubectl apply -f my-deployment.yaml
   ```
- Verify Deployment and Pods:
   ```bash
   kubectl get deployments
   ```

   ```bash
   kubectl get pods
   ```

### Create a Service:
- Using **`kubectl`**:
   - Expose `my-deployment` as a Service with kubectl:

   ``` bash
   kubectl expose deployment my-deployment --type=LoadBalancer --port=80
   ```
   
   - To delete this service:
   ```bash
   kubectl delete service my-deployment
   ```
- Using **`YAML`**
   - Create a file `my-service.yaml` with the following content:
``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```
- This YAML file defines a Kubernetes Service resource named `my-service` using API version v1. 
- The Service is of type LoadBalancer, which means it will expose the application to external traffic through a load balancer, making it accessible outside the Kubernetes cluster. 
- The selector specifies `app: nginx`, so this Service will **route traffic to all Pods that have this label**, effectively **balancing** traffic across those Pods. 
- The ports section defines that the Service will listen on port `80` (accessible to external clients) and forward this traffic to port `80` on the target Pods. 
- This setup enables seamless load balancing and external access to the application running within the cluster.

- Apply the YAML:
   ```bash
   kubectl apply -f my-service.yaml
   ```
- Verify the service:
   ```bash
   kubectl get services
   ```   
   Note the `EXTERNAL-IP` assigned to the LoadBalancer Service for accessing the application externally.

- Delete the service:
   ```bash
   kubectl delete -f my-service.yaml
   ```
- Delete the deployment:
   ```bash
   kubectl delete -f my-deployment.yaml
   ```
- Verify that you deleted all resources created.

## Step 4: Defining Multiple Kubernetes Resources in a Single YAML File
In Kubernetes, you can define multiple resources in a single YAML file by separating each resource with `---`. This approach allows you to group related resources, such as a Deployment and its corresponding Service, into one file. By combining these resources, you can apply and manage them together with a single command, making deployment easier and keeping configurations organized.

### Example: The `algonquin-pet-store-all-in-one.yaml` File
In this file, we define Deployments and Services for the various components of the `Algonquin Pet Store` application, including RabbitMQ, Order Service, Product Service, and Store Front. Each section specifies a different resource, separated by `---` to clearly distinguish them.

Here’s a breakdown of the resources defined in the file:

1. **RabbitMQ Deployment and Service**:
   - The first section creates a Deployment for RabbitMQ, defining a single replica with environment variables for user credentials.
   - The Service for RabbitMQ exposes both the AMQP port (`5672`) for messaging and the management interface (`15672`) within the cluster.

2. **Order Service Deployment and Service**:
   - The second section defines a Deployment for the Order Service with one replica, connecting it to RabbitMQ via an environment variable that specifies the connection string.
   - The Service for Order Service exposes it on port `3000`, making it accessible within the cluster.

3. **Product Service Deployment and Service**:
   - The third section defines a Deployment for the Product Service, again with one replica, exposing its API on port `3030`.
   - The corresponding Service exposes this port internally within the cluster.

4. **Store Front Deployment and Service**:
   - The final section defines a Deployment for the Store Front, setting up a single replica that serves as the frontend.
   - The Service for the Store Front is of type `LoadBalancer`, exposing port `80` to external traffic so that users outside the cluster can access the frontend.

### Applying and Managing Combined Resources

By combining all resources into a single file, you can easily deploy the entire application stack with one command:
```bash
kubectl apply -f algonquin-pet-store-all-in-one.yaml
```

Similarly, to delete all resources defined in the file:
```bash
kubectl delete -f algonquin-pet-store-all-in-one.yaml
```

This setup allows you to deploy or manage all components of the application in a single step, simplifying the process and ensuring all parts of the application are consistently defined and deployed together.

## Important: Clean Up Azure Resources

## Lab Tasks

1. **Examine `algonquin-pet-store-all-in-one.yaml`**:
   - Open the `algonquin-pet-store-all-in-one.yaml` file provided in this lab. Review each section carefully, paying particular attention to the **RabbitMQ** component, which includes both a Deployment and a Service configuration.

2. **Identify Potential Issues with RabbitMQ Configuration**:
   - Think about how RabbitMQ handles data and the challenges it may face.
   - Consider the implications of running RabbitMQ without persistent storage, especially if the pod is deleted or restarted.
   - Is RabbitMQ Stateless or Stateful application?

## Submission

### What to Submit
1. **Demo Video (Max 5 minutes)**  
   - Record a short demo video for the lab that includes:
      - Deploying the Algonquin Pet Store application using the `algonquin-pet-store-all-in-one.yaml` file
      - Demonstrate the RabbitMQ configuration problem.

2. **GitHub Repository (Submission Repo)**  
   - You must create **one GitHub repository** for your lab submission.  
   - This submission repository must include:  
     - A `README.md` file with:  
         - The YouTube demo video link
         - Your written analysis of the RabbitMQ configuration issues, including:
            - Whether RabbitMQ is a stateless or stateful application
            - The implications of running RabbitMQ without persistent storage
            - What happens when the RabbitMQ pod is deleted or restarted
            - Potential solutions to this problem (research-based)
            - Does using Azure Service Bus solve the issues identified with RabbitMQ Configuration in this Lab?
         - (Optional) Notes about setup challenges or lessons learned.  

### How to Submit
- Push your work to a **public GitHub repository** (the submission repository).  
- Submit the link to your submission repository as your final lab deliverable in **Brightspace**.  