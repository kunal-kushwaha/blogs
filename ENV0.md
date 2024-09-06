Infrastructure as Code (IaC) has changed the way how organizations install and manage their IT infrastructure. It helps IT department to move away from manual settings and opt for the automated method for setting up the infrastructure.  In this blog, I'll be covering about IaC , address the common challenges of IaC and better approach to solve the challenges.

## Infrastructure as Code (Iac)

IaC is the way to manage your infrastructure like networks, servers, operating systems and databases using code. It gives you the privilege to set up those resources using code instead of manual setting up servers one-by-one, installing databases and so on. Now, think how tedious this was in earlier times when developers manually set up all this stuff. 

### Challenges Before IaC:

There are various challenges before IaC.
1. **Manual Configuration**: Earlier, setting up infrastructure was a headache as it required significant time and effort. Each server, network, or database had to be configured manually, which created delays and errors. 
2. **Inconsistency**:
   When infrastructure is set up manually, there's a high risk of inconsistencies between different environments (e.g., development, testing, and production). This could lead to issues where something does not work in one environment.
3. **Scaling Issues**:
  As the number of servers and resources grew according to user and application required. Scaling up and down these computing resources is difficult, and sometimes, it also increases the cost of the organization. In case you want to scale down the server, it takes way more time than just doing it with declarative coding. 
  
### Importance of IaC

Infrastructure as Code (IaC) is crucial in modern IT environments for several key reasons:

1. **Consistent:** It ensures consistent environments across development, testing, and production which avoids the **works on my machine** problem.
2. **Scalable:** You can scale infrastructure up or down quickly and efficiently.
3. **Automation:** IaC automates the provisioning and management of infrastructure, reducing manual setup and configuration errors.
4. **Collaborative:** Teams can collaborate more effectively with shared visibility into the infrastructure.
5. **Cost Efficiency:** By automating resource management, IaC helps optimize costs by allowing precise control over resource allocation and quick adjustments based on needs.

### Role of IaC in DevOps

Infrastructure as Code (IaC) is a core practice in DevOps that enables the automation and management of infrastructure through code which allows teams to manage resources in a reliable, repeatable, and scalable manner. Here's how IaC plays a crucial role in DevOps:

1. Automation and Consistency
2. Version Control and Collaboration
3. Scalability and Efficiency
4. Faster Deployments and Continuous Integration/Continuous Deployment (CI/CD)
5. Disaster Recovery and Compliance
6. Environment Replication
7. Monitoring and Auditing
  
## About Env0

<img width="1439" alt="image" src="https://github.com/user-attachments/assets/73f78f57-4454-4b06-b1b1-ad0cd55d6301">

Env0 is a cloud-based platform which is used to automate  infrastructure management and it also streamlines the development process. It is built around the concept of **infrastructure-as-code templates** that allows for easy deployment and management of cloud environments. 

### How Env0 solves challenges in IaC?

Infrastructure-as-Code (IaC) has also presented challenges such as complex pipelines, cost, operational duties, and security issues. Env0 addresses those challenges and helps to solve them with the unique features it brings to the table : 
1. Env0 supports multiple tools like Pulumi, Terraform,  , Terragrunt etc that helps the organizations to choose the tool according to their use case and preferences.
2. Env0 uses a tool called [terratag](https://www.terratag.io/) for managing the cost. This is used to tag cloud resources and determine how much each resource costs over time.
3. There is a common problem with IaC that is known as **drift detection**. It addresses this by enabling scheduled drift detection tasks that automatically compare the actual cloud resource state with the current  IaC configurations. 

## IaC Using Env0

In this section I will be using env0 to configure EKS service on AWS and will also access few tools like Grafana on the services configured. I will be using terraform configurations to make deployment using env0.

**Step 1**: **Configure Env0**

Visit the [env0 website](https://www.env0.com/) and create a account to access the env0 dashboard.

<img width="1437" alt="image" src="https://github.com/user-attachments/assets/250cef16-84d3-4a82-8395-0e335277c545">

**Step 2**: **Create a Project**

Once you have created the account, go ahead and create a new project.

<img width="1439" alt="image" src="https://github.com/user-attachments/assets/f189bb39-a2c8-4ac9-b174-d8a12ab05517">

**Step 3**: **Create and Configure Credentials**

Since we are using AWS in this scenario we have to store **Access Key** credentials along with **Kubernetes EKS Config** credentials.

Go to the credentials tab under setting of the organisation and add the AWS Access Key as the credential in order to connect env0 with your AWS console.

![image](https://github.com/user-attachments/assets/85a3547b-0a22-4c2a-8a1d-37c7e1b4d7c9)

After creating the credentials, under credentials tab of the project go ahead and select the credential just created above

![image](https://github.com/user-attachments/assets/7e3c5004-58c4-4a2a-98e4-82e497917d58)

**Step 4**: **Create Required Templates**

To make sure our deployment is successfully done with all the services required to complete the demo, we can create all the templates required by navigating to templates sections under our organisation. Click on the **Create New Template** button.

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/5677d3ca-969a-4c4e-bb18-3127498654e3">

Click on next after filling the necessary details, make sure to select the correct template type.

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/cbbf662e-f767-40d9-bbcb-54ca99d04fde">

Fill in the necessary details for the version control system you want to use in this template.

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/25946474-3e11-45c4-8c20-6707d973bbd6">

Select the project you want to run this template in.

<img width="1439" alt="image" src="https://github.com/user-attachments/assets/fcf475d9-cda0-4fd5-9e76-a23999ddfe76">

You can also configure various other condition and services such as variables, it can be easily found in the env0 dashboard.

Similary as the above process you can go ahead and configure more such template which can be used to perform different operations such as creating a helm template for grafana and prometheus with different configurations.

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/110c87f1-4a02-4109-b60e-cdf658c2fc4e">

In this scenario I will be pulling grafana and prometheus directly from helm repo. You can fetch all the necessary information about helm repo of grafana and prometheus from [ArtifactHUB](https://artifacthub.io/).


<img width="1439" alt="image" src="https://github.com/user-attachments/assets/fb91b6ff-e560-4ecb-b15e-08cf09798ed2">

Repeat the above process for prometheus and make sure to attach all these templated to the same project to not fall into any error.

**Step 5**: **Running the Templates**

Using the sidebar head over to projects section, choose the project created earlier and under template section and you should see the list created templates here which are attached to this particular project.

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/1e0952f5-530f-4959-9ab2-74e4414a0b35">

Let's go ahead and run the EKS template, make sure you have your credentials correctly configured under this project. You can also specific other details such as TTL, environment variables and automatic terraform plan execution

![image](https://github.com/user-attachments/assets/b44d38a8-d4d9-4f45-9a4e-442c1c5c989d)

After clicking on it will start executing and you can even see the realtime logs of the terraform deployment happening as below

![image](https://github.com/user-attachments/assets/0927530b-964a-4167-99d8-9d5a2587d2ad)

You can review the ```terraform plan``` configurations and click on approve button to create the resources using ```terraform apply```.

![image](https://github.com/user-attachments/assets/f49f01c3-6993-41df-b6c8-fab6c5e9cff2)

You can also use the cost estimation features to look at the bill certain resources can create on running continously for a certain period of time.

![image](https://github.com/user-attachments/assets/46b649c0-82af-4d84-9319-7c8f36d2e10e)

Once the cluster is created you can gather various information using env0 dashboard, such as terrform output, cluster details etc.

![image](https://github.com/user-attachments/assets/0e1d18cc-0308-4343-8925-3485de427b8c)

You can also double verify cluster creation on AWS Management Console

![image](https://github.com/user-attachments/assets/d1cc5b66-4da1-47e4-adb0-616ef843e067)

After cluster is created make sure to add **Kubernetes EKS Configuration** as credential under organisation and add it in the project to configure it with all the templates.

![image](https://github.com/user-attachments/assets/90f5579c-cea5-480a-a50f-852be925163d)

Similarly deploy the other two templates of prometheus and grafana.

![image](https://github.com/user-attachments/assets/3012aa14-a610-474f-a751-6dd41e7ea143)

**Step 6**: **Access the Cluster**

After deploying all the templates make use of the below command to access you kubernetes cluster created.

```
aws eks update-kubeconfig --name <cluster-name> --region <region-name>
```

Output

```
Updated context arn:aws:eks:us-east-1:892502952503:cluster/env0_demo in <path-of-kubeconfig-file>
```

You can use the below command to list all the services running inside your cluster.

```
kubectl get all
```

Output

```
NAME                                                    READY   STATUS    RESTARTS   AGE
pod/grafana-7d5d79bf96-8nfk9                            1/1     Running   0          2m11s
pod/prometheus-alertmanager-0                           1/1     Running   0          4m8s
pod/prometheus-kube-state-metrics-7f979f5c55-25s8l      1/1     Running   0          4m8s
pod/prometheus-prometheus-node-exporter-8tzw9           1/1     Running   0          4m8s
pod/prometheus-prometheus-node-exporter-dqcmw           1/1     Running   0          4m8s
pod/prometheus-prometheus-pushgateway-879d6dd6c-59qwl   1/1     Running   0          4m8s
pod/prometheus-server-64589ccb46-n78xt                  2/2     Running   0          4m8s

NAME                                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/grafana                               ClusterIP   172.20.84.74     <none>        80/TCP     2m11s
service/kubernetes                            ClusterIP   172.20.0.1       <none>        443/TCP    25m
service/prometheus-alertmanager               ClusterIP   172.20.223.234   <none>        9093/TCP   4m8s
service/prometheus-alertmanager-headless      ClusterIP   None             <none>        9093/TCP   4m8s
service/prometheus-kube-state-metrics         ClusterIP   172.20.55.101    <none>        8080/TCP   4m8s
service/prometheus-prometheus-node-exporter   ClusterIP   172.20.62.30     <none>        9100/TCP   4m8s
service/prometheus-prometheus-pushgateway     ClusterIP   172.20.110.209   <none>        9091/TCP   4m8s
service/prometheus-server                     ClusterIP   172.20.201.160   <none>        80/TCP     4m8s

NAME                                                 DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
daemonset.apps/prometheus-prometheus-node-exporter   2         2         2       2            2           kubernetes.io/os=linux   4m8s

NAME                                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                             1/1     1            1           2m12s
deployment.apps/prometheus-kube-state-metrics       1/1     1            1           4m9s
deployment.apps/prometheus-prometheus-pushgateway   1/1     1            1           4m9s
deployment.apps/prometheus-server                   1/1     1            1           4m9s

NAME                                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7d5d79bf96                            1         1         1       2m12s
replicaset.apps/prometheus-kube-state-metrics-7f979f5c55      1         1         1       4m9s
replicaset.apps/prometheus-prometheus-pushgateway-879d6dd6c   1         1         1       4m9s
replicaset.apps/prometheus-server-64589ccb46                  1         1         1       4m9s

NAME                                       READY   AGE
statefulset.apps/prometheus-alertmanager   1/1     4m9s
```

Use the below command to fetch the command which will provide password to login into grafana dashboard.

```
helm status grafana
```

Output

```
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /Users/aayush/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /Users/aayush/.kube/config
NAME: grafana
LAST DEPLOYED: Thu Sep  5 20:37:30 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get your 'admin' user password by running:

   kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


2. The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

   grafana.default.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:
     export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")
     kubectl --namespace default port-forward $POD_NAME 3000

3. Login with the password from step 1 and the username: admin
#################################################################################
######   WARNING: Persistence is disabled!!! You will lose your data when   #####
######            the Grafana pod is terminated.                            #####
#################################################################################
```

Fetch the command below command from the above output

```
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Output of the above command will be a string which will be used as a password to login into grafana dashboard

Use the below command to run grafana locally

```
kubectl port-forward svc/grafana <port-number>:80
```

Visit localhost on the port number mentioned in the above command to access the grafana dashboard.

![image](https://github.com/user-attachments/assets/1b34901a-a411-4987-a075-88526043f67d)

Enter **admin** in username and the password generated from the above command to login.

You can then go ahead and add prometheus as data source to play around with metrics.

![image](https://github.com/user-attachments/assets/261055c1-14b7-4ce6-874e-85ace50af1a3)

## Features of Env0:

1. **Workflows**: In env0, workflows are crucial for automating and managing deployment processes which allows users to customize operational steps according to specific requirements.
2. **Open Policy Agent (OPA)**: The integration of Open Policy Agent (OPA) in Env0 allows users to enforce compliance and governance through code which provides robust mechanism for policy management within cloud deployments. By using OPA, Env0 enables automatic policy checks against Terraform configurations before deployment which ensures that all **infrastructure changes** with organizational standards and security protocols.
3. **Drift Detection**: This feature in **env0** helps monitor and notify users of any difference from the defined infrastructure configurations which helps in changing the configuration quickly.
4. **Environment Discovery**: env0's environment discovery capability helps in identifying and managing previously **untracked cloud resources** which enhances visibility and control.
5. **PR Comments**: env0 automatically generate comments on pull requests to write potential impacts of infrastructure changes which helps in better **decision-making.**
6. **Reusable Variables**: env0 supports the definition and reuse of variables **across multiple deployments** and templates which enhances consistency and reducing configuration errors.
7. **Templates**: env0 allows users to utilize predefined templates or create custom ones to standardize environment setups which simplifies and speeds up deployment processes.
8. **Access Control**: It's robust access control mechanisms in env0 ensure that only authorized person can make changes to the environment which enhances security.
9. **Environment Scheduling**: env0 provides scheduling capabilities that allows for the automation of environment deployments which helps in optimizing the resources as well as operational costs.

## Ideal Market

Env0 is a cloud management platform that offers Infrastructure as Code (IaC) automation and collaborative remote-run workflow management. The ideal market for Env0 typically includes:

1. **DevOps Teams:** Env0 is particularly useful for DevOps teams that manage cloud resources across different environments. 
2. **Software Development Companies:** Companies that frequently deploy and manage applications across various cloud platforms can benefit from Env0’s capabilities, such as drift detection, cost estimation, and policy enforcement.
3. **Managed Service Providers (MSPs):** MSPs that handle cloud infrastructure for multiple clients can use Env0 to improve efficiency and provide value-added services like cost optimization and compliance monitoring.
4. **Enterprises with Multi-cloud Environments:** Env0 supports multiple cloud platforms, making it an excellent tool for enterprises that operate in hybrid or multi-cloud environments. It provides a unified interface for managing resources across AWS, Azure, Google Cloud, and more.
5. **Startups and Scale-Ups:** These organizations often need to move quickly and adapt their infrastructure with minimal overhead. Env0 automates many aspects of infrastructure management, which can accelerate development cycles and reduce operational costs.

## Getting Started

- Read the [documentation](https://docs.env0.com/docs/about-env0) in order to learn more.
- **Install**: You can take free trial of the tool from [here](https://app.env0.com/login?_gl=1*15pc9ig*_gcl_au*MTk5Mzc3NDc5NC4xNzI1NDYyMDcy*_ga*MjA4MjgwNjcwMS4xNzI1NDYyMDcx*_ga_VYZFC0GDCG*MTcyNTQ2ODQzNy4zLjEuMTcyNTQ3MTg5OC4zMS4wLjA.) and start using according to your organization requirements.

## Conclusion

In conclusion, Infrastructure as Code (IaC) has fundamentally transformed how organizations set up and manage IT infrastructure moving from manual configurations to automated processes. This shift enhances efficiency, reduces errors, and ensures consistency across various environments. The integration of platforms like Env0 further streamlines these processes by offering features such as drift detection, cost management, and policy enforcement. For organizations aiming to optimize their cloud infrastructure management, adopting IaC tools like Env0 is not just an improvement but a necessity. It empowers teams, speeds up deployments, and ultimately drives business agility and growth.
