A VPN, or Virtual Private Network, is like a secret tunnel on the internet. When you use a VPN, it creates a private path for your online activities, keeping them hidden from others. It's similar to sending your internet traffic through a private tunnel instead of a busy public highway. This way, it helps keep your information safe from people who might want to peek at what you're doing online, like hackers or advertisers. 

VPNs prove essential for securing remote access and defending data in transit, especially within cloud-first environments where DevOps and IT professionals concentrate heavily on remote operations and cloud services. Implementing an effective VPN allows your team to improve security, enforce compliance, and maintain high performance across dispersed architectures, making it a crucial component of a modern, scalable, and secure infrastructure. In this blog, I have covered the challenges associated with Traditional VPNs and demonstrated how [Twingate](https://www.twingate.com/) is revolutionizing remote access for organizations, offering enhanced security and efficiency.


## Setbacks in VPN Enabled Environment

VPNs are helpful in creating secure connections to private networks, but often end up with:

1. **Performance issues**: VPNs can slow down internet speeds because all traffic must be encrypted and sent through a VPN server before reaching its destination. This additional routing can lead to latency and reduced bandwidth, which creates problems for activities like streaming videos or large file transfers.

2. **Complex Setup and Management**: Setting up a VPN for enterprices is a hideous task and requires deep technical knowledge. This includes setting up secure connections, managing server configurations, and ensuring compatibility with various devices and operating systems. 

3. **Scalability Issues**: When organizations grow, scaling up a VPN to accommodate more users and higher traffic can become challenging. More resources and better infrastructure are needed, that can be costly and also hard to manage for businesses.

4. **Security Issues**: While VPNs are meant to secure data, they can sometimes become targets for cyberattacks themselves. Misconfigurations, outdated software, or flaws in the VPN protocol can expose the network to security risks. Once a user is authenticated, VPNs often provide broad access to a network, which could lead to a security breach.

5. **Limited User Access Controls**: Traditional VPNs typically do not have granular access controls. Once users are connected, they often have access to much more information than necessary, which can pose a risk if those credentials are compromised.

## Introducing Twingate: 

[Twingate](https://www.twingate.com/) is a modern zero-trust network access solution that offers smooth and safe resource access over various networks. In contrast to traditional VPNs,  Twingate focuses on **fine-grained access control**, ensuring that, without exposing the entire network interface. It only allows authenticated and  users to connect to particular resources. Twingate is an efficient and user-friendly solution for secure remote access since it enables users to access resources by their **Fully Qualified Domain Name (FQDN)** or IP address without knowing the underlying network setups. Twingate enables access to infrastructure without requiring it to be publicly exposed on the internet via a jump server, Bastion host, or other endpoint. It is also easily access multiple clouds or multiple environments (e.g. staging) at the same time.

### Architecture of Twingate

![Screenshot 2024-08-25 at 3.54.07 PM](https://hackmd.io/_uploads/HyHULY_o0.png)

Twingate's architecture emphasizes security, as the multiple components work together before allowing any data flow. Hence, it minimizes the risk of unauthorized access. There are four main operating components to provide secure and encrypted communication: **Controller, Client, Connector, and Relay**. These components also ensure that only authenticated and authorize users are able to access that particular resource that they have been authorized to access.

## Secure Resources using Twingate

In this demo I will cover three important use cases of Twingate as a secure layer to access your private resources. 

### Use Case 1: Secure Kubernetes with Twingate

**Step 1**: **Create a Kuberenetes Cluster**

In order to secure access to our kubernetes applications we need to spin a cluster in which our applications will run. Follow the [Civo Documentation](https://www.civo.com/docs/kubernetes/create-a-cluster) for creating a kubernetes cluster.

**NOTE: Make sure to select [helm](https://helm.sh/) from the the [Civo Marketplace](https://www.civo.com/marketplace) while configuring your cluster.**

**REASON: We will be following [helm chart method](https://www.twingate.com/docs/k8s-private-services) to access resources from kubernetes cluster.**



**Step 2**: **Create NGNIX Deployment and Service**

To run a private resource in our newly created kubernetes cluster on civo we would need a deployment and service configuration files.

**Deployment File**: **nginx.yaml**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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
        image: nginx:latest
        ports:
        - containerPort: 80

```

**Service File**: **nginx-svc.yaml**
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP

```

Now since we want to keep the access of nginx only to our cluster and not to be accessible from outside of the cluster we will keep the service type as **ClusterIP**.

Once both the files are saved, go ahead and apply these configurations in your civo kubernetes cluster using the below commands

```
kubectl apply -f nginx.yaml
kubectl apply -f nginx-svc.yaml
```

You will see the below as output after running the above commands
```
deployment.apps/nginx-deployment created
service/nginx-service created
```

You can also verify the deployment creation using the below commands:

```
kubectl get deployments
```

Output
```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           2m24s
```
Verify the service creation using the below command:

```
kubectl get services
```

Output

```
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.43.0.1      <none>        443/TCP   8m59s
nginx-service   ClusterIP   10.43.216.34   <none>        80/TCP    3m46s
```

Since this service is not accessible outside our kubernetes cluster we will have to install Twingate connector in our cluster, you can also double verify by trying to access the service by copying the **CLUSTER-IP** of **nginx-service** and pasting on your browser. 

**Step 3**: **Deploy Twingate Connector using Helm**

For this step make sure you have [Twingate Account Setup](https://www.twingate.com/docs/quick-start) completed. Once you have your Twingate account configured head over to networks and create a new remote network as below
![image](https://hackmd.io/_uploads/ryxCMtujA.png)

You can either configure a already created connector or deploy/add a new connector. Make sure you choose helm from deployment configuration list and click on generate tokens button.
![image](https://hackmd.io/_uploads/rJ8v4tds0.png)

Once the token are generated copy the helm commands and paste in your kubernetes cluster environment
![image](https://hackmd.io/_uploads/SJhzSYdiA.png)


The above step will add a Twingate helm chart and will create a helm repo in your kubernetes cluster. Once the repo is create go ahead and run the second command to deploy the Twingate connector.

Commands
```
helm repo add twingate https://twingate.github.io/helm-charts
helm upgrade --install twingate-prompt-hedgehog twingate/connector -n default --set connector.network="<network-name>" --set connector.accessToken="<access-token>" --set connector.refreshToken="<refresh-token>"
```

Output
```
Release "twingate-prompt-hedgehog" does not exist. Installing it now.
NAME: twingate-prompt-hedgehog
LAST DEPLOYED: Tue Aug 20 16:05:15 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Once the connector is deployed you will see it online on your Twingate network
![image](https://hackmd.io/_uploads/ry_UHKdiA.png)

**Step 4**: **Create a resource on Twingate**

Go ahead and create a resource on Twingate and add it under your remote network. Copy and paste **CLUSTER-IP** of your **nginx-service** while creating this resource.
![image](https://hackmd.io/_uploads/BJOYUK_s0.png)


You can create teams, add users and roles to provide access to resources, in this scenario I will provide access to everyone
![image](https://hackmd.io/_uploads/BJ03IFuoR.png)


**Step 5**: **Accessing the Resource**

After performing all the above steps download and install twingate app in the device you want to access the running resource. Authenticate the app with your account. You will be able to see the resource running through the app
![image](https://hackmd.io/_uploads/BytKDF_iA.png)

Copy your nginx service **CLUSTER-IP** and paste it in your browser to access the running the resource
![image](https://hackmd.io/_uploads/HkV3wFuiC.png)


### Use Case 2: Securing Access to GitHub

For this section we will use GitHub Actions to provide secure access to Twingate resources.

**Step 1**: **Creating a Workflow for GitHub Actions**

Make sure you have a [GitHub Repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) with **.github/workflows** directory in it. Create a new yaml file with below configurations inside the **.github/workflows** directory to trigger a new [GitHub Action](https://github.com/features/actions).

```
name: Twingate on GitHub Actions Demo
on: [push]
jobs:
  Twingate-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
      - name: Install Twingate
        run: |
          echo "deb [trusted=yes] https://packages.twingate.com/apt/ /" | sudo tee /etc/apt/sources.list.d/twingate.list
          sudo apt update -yq
          sudo apt install -yq twingate

      - name: Setup and start Twingate
        env:
          TWINGATE_SERVICE_KEY: ${{ secrets.SERVICE_KEY }}
        run: |
          echo $TWINGATE_SERVICE_KEY | sudo twingate setup --headless=-
          sudo twingate start

      - name: (optional) Twingate status
        run: twingate status

      - name: (optional) Twingate logs
        run: journalctl -u twingate

      - name: Access a secure resource
        env:
          TEST_URL: <URL-of-secure-resource>
        run: |
          echo Calling $TEST_URL 🚀
          curl $TEST_URL

      - name: Access a public resource
        env:
          TEST_URL: https://www.twingate.com/
        run: |
          echo Calling $TEST_URL 🚀
          curl -v $TEST_URL

      - run: echo "SUCCESS!!! 🤩 This job's status is ${{ job.status }}."

      - name: Stop Twingate
        run: sudo twingate stop
```

In this scenario we will try to access the above created **nginx-service**. Make sure you replace the value of **TEST_URL** under **Access a secure resource** section with the address of your Twingate resource such as **http://10.43.12.110:80/**

**Step 2**: **Setting up a Service Account**

To access the secure resource using GitHub Actions you need to create a service account on Twingate and add the resource in the created service account.

Head back to twingate and under **Team** section you will see the **Services** option. Go ahead and create a service account.
![image](https://hackmd.io/_uploads/H1ixOYujA.png)

Once the service account is created, go ahead and generate a service key
![image](https://hackmd.io/_uploads/rJuQ_FOs0.png)

Download and copy the service key
![image](https://hackmd.io/_uploads/rkVV_YdjR.png)

Add the resources you want to access using this service account
![image](https://hackmd.io/_uploads/SyLrOYusC.png)

**Step 3**: **Accessing the Resource** 

To access the resource using GitHub Actions setup the service key created as a secret in your desired GitHub repository using this [guide](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions). Make sure the name of the key is **SERVICE_KEY** while creating a secret on GitHub repository.

Once all the above steps are correctly configured, you will be able to access the resource everytime your **GitHub Action** pipeline triggers an update in your repository. You can check your workflow logs by visiting the **Actions** tab in the GitHub repository navbar.

In this scenario we were running nginx as a resource and hence got the below output, but outputs can differ based on the application running in the twingate resource.
![image](https://hackmd.io/_uploads/r1Wp_Kdo0.png)


### Use Case 3: Secure Cloud Environments

In this section we will see how we can secure connection to our cloud environments using Twingate, for this section we will create two [EC2 Instance](https://aws.amazon.com/ec2/) under one [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html). One instance will be publicly accessible while the other will be a private instance. 

**Step 1**: **Create a VPC**

Make sure you have the below configurations in your VPC
![image](https://hackmd.io/_uploads/r1iaKK_j0.png)

- **Public and Private Subnets**
- **Route Tables for both public and private subnets**
- **Internert Gateway and NAT Configured** 

**Step 2**: **Create EC2 Instances**

For first instance let's name it as **twingate-connector**(public instance), choose a base Linux Image. For this scenario I have used **ubuntu** as base Linux Image. Make sure you edit the network setting before launching the instance and your network setting should look similar to the below image
![image](https://hackmd.io/_uploads/rkFh9tusR.png)

- **Choose the VPC created in the above step**
- **Choose public subnet for this instance**
- **Enable the auto assign IP feature** 
- **Create a new security group, or you can use an existing one**

Configuration of **twingate-connector**
![image](https://hackmd.io/_uploads/rJO_iFOiC.png)


For second instance let's name it as **private-instance**, choose a base Linux Image. For this scenario I have used **ubuntu** as base Linux Image. Make sure you edit the network setting before launching the instance and your network setting should look similar to the below image
![image](https://hackmd.io/_uploads/BkE7oYOjR.png)

- **Choose the VPC created in the above step**
- **Choose private subnet for this instance**
- **Disable the auto assign IP feature** 
- **Choose the security group created above**

Configuration of **private-instance**
![image](https://hackmd.io/_uploads/rJncjKusC.png)

**Step 3**: **Deploy the Twingate Connector**

Now if you SSH into twingate-connector instance using the key pair used to create the instances you won't get any errors but when you will try to SSH into private instance you will get error in connection. We can manage this access to private instance using twingate connector and also secure this connection.

Head over to twingate network created and create a new remote network
![image](https://hackmd.io/_uploads/SkhastdjA.png)

Choose Linux from the deployment types in connector under the newly created remote network and click on generate tokens button
![image](https://hackmd.io/_uploads/Skyx2t_sR.png)

SSH into the twingate-connector instance using the below command
```
ssh -i "<key-pair-path>" ubuntu@ec2-<public-ip>.compute-1.amazonaws.com
```

Example

```
ssh -i twingate-demo.pem ubuntu@ec2-54-210-165-102.compute-1.amazonaws.com
```

Once we are in the twingate-connector instance copy the command from the Linux connector and run it inside the instance to deploy the connector and  establish connection
![image](https://hackmd.io/_uploads/r1R8nFdsA.png)

Command
```
curl "https://binaries.twingate.com/connector/setup.sh" | sudo TWINGATE_ACCESS_TOKEN="<access-token>" TWINGATE_REFRESH_TOKEN="<refresh-token>" TWINGATE_NETWORK="<network-name>" TWINGATE_LABEL_DEPLOYED_BY="linux" bash
```

Head back to twingate and you will see that the connector is online
![image](https://hackmd.io/_uploads/ry4cnK_iC.png)

**Step 4**: **Add the Private Instance to Access via Twingate**

Create a resource on Twingate and paste the **Private-IP** of the private instance in this resource.
![image](https://hackmd.io/_uploads/H1SypK_iC.png)

You will see that the resource is online, open the Twingate app and you will be able to SSH into the private instance now
![image](https://hackmd.io/_uploads/S1aHpKOjC.png)

You can now securly run any desired application in this private instance.

To establish connection over mutiple VPCs using the same network repeat the above steps.

## Key Features of Twingate:

Twingate offers various features which are designed to enhance the security, ease of use, and performance of the network:
1. **Zero Trust Network Access:** Twingate focuses on fine-grained access control, which ensures that users can access only those resources for which they are authorized to use instead of access to a broad network.
2. **Integration with Identity Providers:** Twingate also helps in user authentication to external Identity Providers like Okta, Azure AD, or Google Workspace to enhance security by separating the authentication directly from the network. ⁤
3. **Ease of Deployment and Scalability:**
The controller is hosted and managed by Twingate, which helps provide redundancy and high availability. ⁤Twingate works fine without requiring changes to existing network configurations, making it easy to deploy and scale across different environments. 
4. **Decentralized Architecture**
Twingate uses a decentralized architecture, and the four components of the Twingate ensure security.
The controller acts as the central coordination point, helping to manage access policies and also authenticating users to third-party identity providers.
Similarly, the client component helps in handling authentication, authorization, and network routing, which ensures secure connections to protected resources. 
- The connector helps in forwarding traffic to local resources.
Relay helps set up the connection between Clients and Connectors when a direct peer-to-peer connection cannot be established without storing any network-identifiable information.
5. **End-to-End Encryption:**
- All traffic between the Client and Connector is encrypted using certificate-pinned TLS tunnels, which ensures data integrity and confidentiality. 


## Why Businesses Are Switching to Twingate?

Cybersecurity threats are increasing day-by-day, and remote work is common; organizations are looking for more secure ways to manage network access. Twingate is a preferred choice for many organizations due to its innovative approach to remote access. Here are some key reasons why businesses are making the switch:

1. **Enhanced Security with Zero Trust Model**: Twingate works on the zero trust model principle. Twingate does not allow access to every access it receives. This approach also ensures that only authenticated and authorized users can access specific applications or services, which helps reduce cyber attacks.

2. **Improved User Experience**: Twingate is designed to run in the background, easing the user's process. Other VPNs require users to connect and disconnect manually, but Twingate automates the process and provides a more stable connection.

3. **Simplified Management and Scalability**: Managing traditional VPNs can be difficult as it involves setting up complex infrastructure and frequent maintenance. Twingate allows IT teams to configure and monitor access easily without technical teams. Not only this, but Twingate is also cloud-native, so it scales as the organization grows and can adjust an increasing number of users and workloads without the need for infrastructure.

4. **Reduced Costs**: Twingate can reduce operational costs for businesses. It also eliminates the need for costly hardware deployments and reduces IT teams' workload, helping them focus on other business areas.


6. **Global Accessibility**: Any organization with employees from around the world needs fast and secure access to the network. Twingate's network optimization techniques ensure that users worldwide have consistent and reliable access.

### Use Cases
Twingate offers several use cases but here are three different use cases for Twingate:

1. **Remote Work Access**:
   - Due to Twingate employees are able to securely access company resources from anywhere without the fail.

2. **Secure Third-Party Access**:
   - Businesses often need to grant access to external parties, such as contractors or partners, to specific parts of their network. Twingate allows for **granular access control**, helping organizations to provide exactly the necessary resource access without exposing their entire network, enhancing security and control.

3. **Development and IT Operations**:
   - Twingate supports DevOps practices by providing developers and IT teams with secure access to development environments, servers, and databases without exposing them to the public internet. Its compatibility with automation tools like Terraform and Pulumi, and advanced configuration capabilities through CLI tools, make it an ideal choice for agile and secure software development.



## Difference between Twingate and VPN
1. **Direct Peer-to-Peer (P2P) Connections:** P2P direct connections offer the fastest performance and most reliability than any other Traditional VPN

2. **Private Proxy Architecture:** Unlike Traditional VPNs, which require time and cost to set up, Twingate's private proxy architecture acts as a bridge between clients and network resources, simplifying deployment. Private proxy architecture makes deployment fast, easy, and flexible.

3. **API-First Design:**
Traditional network security tools often need more comprehensive API support, limiting automation and integration capabilities. In contrast, Twingate's API-first design is built to enable powerful automation and prioritizes using application programming interfaces to manage network access and security settings. 

4. **Streamlined Zero-Trust:** In Traditional security models, we need an IT team to set up everything. At the same time, Twingate is designed to be accessible for all the key constituencies of a zero-trust project: IT, security, developers/devops, and end users. Twingate facilitates secure application access and development processes without interfering with productivity. End users benefit from a seamless connection experience that requires minimal interaction



## Ideal Market for Twingate

Twingate is designed to cater to various audiences, offering secure and efficient remote access solutions:

- **Remote and Hybrid Workforces**: Enables secure access to corporate resources from any location.
- **IT and Cybersecurity Teams**: Enhances network security with a zero-trust architecture.
- **Healthcare Providers**: Ensures compliance with privacy regulations like HIPAA.
- **Financial Services**: Manages sensitive financial data securely, aiding in regulatory compliance.
- **Educational Institutions**: Supports remote access to educational materials and administrative systems.
- **Legal and Consulting Firms**: Provides secure access to sensitive client data and communications.
- **Technology Startups and SMEs**: Offers scalable and easy deployment for growing businesses.
- **Government Agencies**: Improves cybersecurity while enabling secure remote operations.


## Getting Started with Twingate

Starting with Twingate is easy and it's free for small teams or testing it out. You can use it for up to 5 users without paying anything. Here’s how to get going:

1. **Sign Up**: Go to the Twingate website, pick the free plan, and sign up.
2. **Install**: Follow simple steps to get Twingate set up on your devices.
3. **Set Up**: Adjust the settings to what your team needs.
4. **Connect**: Link Twingate with other tools you use. You can find the tools it works with [here](https://www.twingate.com/integrations).
5. **Try It Out**: Start using Twingate with your team and see how it works for you.

## Conclusion

Projects, organizations, and businesses are moving towards remote work, and there are various challenges with traditional VPNs like performance, security, and scalability, which hinder the productivity of the Tech team. In order to enhance productivity and allow remote network access,  Twingate's architecture and features—such as direct P2P connections, private proxy architecture, API-first design, and a user-friendly approach- help the project scale.
