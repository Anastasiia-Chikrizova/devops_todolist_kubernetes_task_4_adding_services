# INSTRUCTION.md

This document provides instructions on how to test the Django ToDo application in a Kubernetes environment. Below are the steps to test the application in various scenarios, including using a ClusterIP service, accessing the application with a NodePort service, and testing it via port-forwarding.

---

## Prerequisites
1. Ensure you have a running Kubernetes cluster and `kubectl` CLI configured to interact with the cluster.
2. Deploy the ToDo application and associated Kubernetes manifests (Pods and Services) as per the task requirements.

---

## 1. Testing the Application Using a ClusterIP Service DNS
The **ClusterIP Service** allows internal communication within the cluster. Use the following steps to test the application through the ClusterIP Service from a BusyBox container:

### Steps:
1. Ensure the ClusterIP Service is deployed along with the ToDo application Pods. Identify the service name:
   ```bash
   kubectl get svc
   ```
2. Start a BusyBox container inside the same namespace:
   ```bash
   kubectl run busybox --image=busybox --restart=Never --rm -it -- /bin/sh
   ```
3. Resolve the ClusterIP Service DNS and test connectivity. Replace `<SERVICE_NAME>` with the name of the ClusterIP Service:
   ```bash
   nslookup <SERVICE_NAME>
   wget -qO- http://<SERVICE_NAME>:<SERVICE_PORT>
   ```
   Example:
   ```bash
   wget -qO- http://todolist-service:8000
   ```

4. You should receive an HTTP response from the application, verifying that the ClusterIP Service is working correctly.

---

## 2. Testing the Application Using Service Port-Forward
Port-forwarding enables local access to the application from your machine without exposing it externally.

### Steps:
1. Identify the name of the Service associated with the ToDo application:
   ```bash
   kubectl get svc
   ```
2. Forward the service port to your local machine. Replace `<SERVICE_NAME>` with the name of your service and set the appropriate ports:
   ```bash
   kubectl port-forward svc/<SERVICE_NAME> 8080:<SERVICE_PORT>
   ```
   Example:
   ```bash
   kubectl port-forward svc/todolist-service 8080:8000
   ```
3. Access the ToDo application on your local machine via the forwarded port:
   [http://localhost:8080](http://localhost:8080)

   You should see the application's interface confirming that the port-forward is successful.

---

## 3. Accessing the Application Using a NodePort Service
The **NodePort Service** exposes the application at the node's IP address on a specific port.

### Steps:
1. Ensure the NodePort Service is deployed. Identify the node's external IP address and the `NodePort`:
   ```bash
   kubectl get nodes -o wide
   kubectl get svc
   ```
   Example output:
   ```
   NAME                      TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
   todolist-nodeport-service NodePort   10.100.200.2   <none>        8000:30000/TCP   5m
   ```
   The `NodePort` in this case is `30000`.

2. Access the service by visiting the `NodeIP:NodePort` from your browser or using `curl`. Replace `<NODE_IP>` and `<NODE_PORT>` with the appropriate values:
   ```bash
   curl http://<NODE_IP>:<NODE_PORT>
   ```
   Example:
   ```bash
   curl http://192.168.1.100:30000
   ```

3. The application should be accessible via the node's IP address and the specified NodePort. You can open it in your browser as well:
   [http://<NODE_IP>:<NODE_PORT>](http://<NODE_IP>:<NODE_PORT>)

---

## Conclusion
Following the instructions above, you should be able to:
1. Test the ToDo application using the internal **ClusterIP Service** DNS.
2. Test the application locally using the **port-forward** command.
3. Access the application externally via the **NodePort Service**.

Ensure all required manifests are deployed correctly for successful testing.