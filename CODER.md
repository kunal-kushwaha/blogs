Developers generally face issues where their code runs perfectly fine on their local machines but fails in production due to inconsistent development environments. These environments are also restricted by the hardware limitations of their computers, which makes it challenging to scale resources for larger projects. Setting up and managing these environments is time-consuming and also generates errors, which generally decreases productivity. Also, storing sensitive data on local devices may be risky due to security issues. In this blog, I have covered common challenges of these envrionment and how we can address them using [Coder](https://coder.com/).


## Common Challenges Faced by Developers

Developers faced several common challenges in their workflows like:

1. Setting up different development environments for developers requires a complex setup and increases the cost, which reduces the time of delivering the software.
2. Managing software dependencies and versions across different machines led to conflicts which again reduced the time of delivering the software
3. Large codebases in their local system delay the build time, reducing overall productivity. 
4. There may be security issues in their development environment. Managing security and compliance in a remote setup is a bit challenging, which affects the overall security of an application.
5. There may be a limitation of hardware in the local system, which gives performance issues.
6. Last but not least, collaboration between developers due to global work is challenging due to inconsistencies in the environment.


## About Coder


Coder is an open-source platform for creating and managing **developer workspaces** on your preferred clouds and servers. It offers **Cloud Development Environments** (CDEs), which means you can use your cloud to manage your development environment that enables developers to work from anywhere without any complex local setups. It simplifies the collaboration between remote teams.

### How does Coder solve these Challenges?

1. **Simplified Environment Setup:** Coder provides a cloud-based platform that automates setting up development environments. This reduces the complexity and cost associated with local configurations and also speeds up software delivery.
2. **Streamlined Dependency Management:** Coder centralizes dependency management, ensuring consistent versions across all environments.
3. **Reduced Build Times:** It provides cloud-based IDE so developers can build in the cloud, reducing build time.
4. **Enhanced Security:** Coder ensures that development environments have strict security and compliance standards. 
5. **Optimized Resource Utilization:** Coder has a cloud infrastructure layer that provides scalable and efficient resource allocation, helps overcome the limitations of local hardware, and improves overall performance.

### Key Features of Coder

Coder offers different features: 
- **Remote Development Environments:** This ensures the security of the source code and the safety of sensitive data.
- **Developer Productivity:** It allows developers to contribute code from day one due to an automated development environment, which minimizes the onboarding time. 
- **Enhanced Security:** Coder separates development environments from desktops and provides a more secure development process.
- **Various integrations support:** Coder also supports a wide array of Integrated Development Environments (IDEs), from web-based setups like code-server and Jupyter to JetBrains Gateway and VS Code Remote. This flexibility ensures that developers can access their workspaces from any device. Developers not only have the flexibility to use their laptops, but it also supports lightweight notebooks and iPads. It also includes integration with Google Cloud VMs, Amazon EC2 VMs, Raspberry Pi, and Kubernetes. 


## Run Ollama in Kubernetes Cluster using Coder

**Step 1**: **Create a Kubernetes Cluster on Civo**
In order to secure access to our kubernetes applications we need to spin a cluster in which our applications will run. Follow the [Civo Documentation](https://www.civo.com/docs/kubernetes/create-a-cluster) for creating a kubernetes cluster.

**NOTE: Make sure to select [helm](https://helm.sh/) from the the [Civo Marketplace](https://www.civo.com/marketplace) while configuring your cluster.**

**Step 2**: **Install and Run Coder in your cluster**

After exporting your kubeconfig file of Kubernetes cluster, follow the [Coder Installation Guide](https://coder.com/docs/install/kubernetes) for Kubernetes to setup a Coder environment.

**Step 3**: **Configure Coder for Kubernetes Deployments**

Once the coder is up and running, go ahead and create secret from your kubeconfig file using the below command

```
kubectl create secret generic kubeconfig-secret -n coder --from-file=<path-to-kubeconfig-file>
```

Install and configure the changes in the yaml file using the below command

```
helm install coder coder-v2/coder \
    --namespace coder \
    --values values.yaml \
    --version 2.14.2

```

Output of the above command:

```
Release "coder" has been upgraded. Happy Helming!
NAME: coder
LAST DEPLOYED: Wed Aug 28 02:44:51 2024
NAMESPACE: coder
STATUS: deployed
REVISION: 5
TEST SUITE: None
NOTES:
Enjoy Coder! Please create an issue at https://github.com/coder/coder if you run
into any problems! :)
```

Example **values.yaml** file

```
coder:
  # ...
  volumes:
    - name: "kubeconfig-mount"
      secret:
        secretName: "kubeconfig-secret"
  volumeMounts:
    - name: "kubeconfig-mount"
      mountPath: "/mnt/secrets/kube"
      readOnly: true

  # You can specify any environment variables you'd like to pass to Coder
  # here. Coder consumes environment variables listed in
  # `coder server --help`, and these environment variables are also passed
  # to the workspace provisioner (so you can consume them in your Terraform
  # templates for auth keys etc.).
  #
  # Please keep in mind that you should not set `CODER_HTTP_ADDRESS`,
  # `CODER_TLS_ENABLE`, `CODER_TLS_CERT_FILE` or `CODER_TLS_KEY_FILE` as
  # they are already set by the Helm chart and will cause conflicts.
  env:
    - name: CODER_PG_CONNECTION_URL
      valueFrom:
        secretKeyRef:
          # You'll need to create a secret called coder-db-url with your
          # Postgres connection URL like:
          # postgres://coder:password@postgres:5432/coder?sslmode=disable
          name: coder-db-url
          key: url

    # (Optional) For production deployments the access URL should be set.
    # If you're just trying Coder, access the dashboard via the service IP.
    - name: CODER_ACCESS_URL
      value: "<External-IP-of-coder-service>"

  tls:
    secretNames:
      - civo-ingress-tls
``` 

Make sure to configure TLS in civo cluster using this [guide](https://www.civo.com/learn/kubernetes-https-ingress-controller-with-your-own-tls-certificate).

**Step 4**: **Create Sample Deployment and Workflows**

After deploying coder in your kubernetes cluster head over to your browser and paste the **External-IP** of your coder service created in step 2.

Head over to templates tab from the navbar to create a desired template, in this scenario we will be making a kubernetes deployment

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/8f08e473-33c6-416c-932a-558417b6d474">

After choosing the template, fill in the required details and click on create template button. You can then go ahead and create a workspace for this template

<img width="1439" alt="image" src="https://github.com/user-attachments/assets/ad8be6af-cfc2-4579-8a40-d5ec28cc1caa">

Fill in the CPU and Memory requirements and click on create workspace button

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/a47e0d08-b1af-4c5f-83f9-0e3471c2f696">

Once the workflow is created using the template you can choose between the different modes to perform different operations in the workspace created

![image](https://github.com/user-attachments/assets/4199f11d-37c5-4bdd-bfe8-1fb47bb3982f)

This is how your terraform configuration should look like in the template created.

```terraform {
  required_providers {
    coder = {
      source = "coder/coder"
    }
    kubernetes = {
      source = "hashicorp/kubernetes"
    }
  }
}

provider "coder" {
}

variable "use_kubeconfig" {
  type        = bool
  description = <<-EOF
  Use host kubeconfig? (true/false)

  Set this to false if the Coder host is itself running as a Pod on the same
  Kubernetes cluster as you are deploying workspaces to.

  Set this to true if the Coder host is running outside the Kubernetes cluster
  for workspaces.  A valid "~/.kube/config" must be present on the Coder host.
  EOF
  default     = false
}

variable "namespace" {
  type        = string
  description = "The Kubernetes namespace to create workspaces in (must exist prior to creating workspaces). If the Coder host is itself running as a Pod on the same Kubernetes cluster as you are deploying workspaces to, set this to the same namespace."
}

data "coder_parameter" "cpu" {
  name         = "cpu"
  display_name = "CPU"
  description  = "The number of CPU cores"
  default      = "2"
  icon         = "/icon/memory.svg"
  mutable      = true
  option {
    name  = "2 Cores"
    value = "2"
  }
  option {
    name  = "4 Cores"
    value = "4"
  }
  option {
    name  = "6 Cores"
    value = "6"
  }
  option {
    name  = "8 Cores"
    value = "8"
  }
}

data "coder_parameter" "memory" {
  name         = "memory"
  display_name = "Memory"
  description  = "The amount of memory in GB"
  default      = "2"
  icon         = "/icon/memory.svg"
  mutable      = true
  option {
    name  = "2 GB"
    value = "2"
  }
  option {
    name  = "4 GB"
    value = "4"
  }
  option {
    name  = "6 GB"
    value = "6"
  }
  option {
    name  = "8 GB"
    value = "8"
  }
}

data "coder_parameter" "home_disk_size" {
  name         = "home_disk_size"
  display_name = "Home disk size"
  description  = "The size of the home disk in GB"
  default      = "10"
  type         = "number"
  icon         = "/emojis/1f4be.png"
  mutable      = false
  validation {
    min = 1
    max = 99999
  }
}

provider "kubernetes" {
  # Authenticate via ~/.kube/config or a Coder-specific ServiceAccount, depending on admin preferences
  config_path = var.use_kubeconfig == true ? "~/.kube/config" : null
}

data "coder_workspace" "me" {}
data "coder_workspace_owner" "me" {}

resource "coder_agent" "main" {
  os                     = "linux"
  arch                   = "amd64"
  startup_script         = <<-EOT
    set -e
    curl -fsSL https://ollama.com/install.sh | sh
    /usr/local/bin/ollama start >/tmp/ollama.log 2>&1 &
  EOT


  # The following metadata blocks are optional. They are used to display
  # information about your workspace in the dashboard. You can remove them
  # if you don't want to display any information.
  # For basic resources, you can use the `coder stat` command.
  # If you need more control, you can write your own script.
  metadata {
    display_name = "CPU Usage"
    key          = "0_cpu_usage"
    script       = "coder stat cpu"
    interval     = 10
    timeout      = 1
  }

  metadata {
    display_name = "RAM Usage"
    key          = "1_ram_usage"
    script       = "coder stat mem"
    interval     = 10
    timeout      = 1
  }

  metadata {
    display_name = "Home Disk"
    key          = "3_home_disk"
    script       = "coder stat disk --path $${HOME}"
    interval     = 60
    timeout      = 1
  }

  metadata {
    display_name = "CPU Usage (Host)"
    key          = "4_cpu_usage_host"
    script       = "coder stat cpu --host"
    interval     = 10
    timeout      = 1
  }

  metadata {
    display_name = "Memory Usage (Host)"
    key          = "5_mem_usage_host"
    script       = "coder stat mem --host"
    interval     = 10
    timeout      = 1
  }

  metadata {
    display_name = "Load Average (Host)"
    key          = "6_load_host"
    # get load avg scaled by number of cores
    script   = <<EOT
      echo "`cat /proc/loadavg | awk '{ print $1 }'` `nproc`" | awk '{ printf "%0.2f", $1/$2 }'
    EOT
    interval = 60
    timeout  = 1
  }
}

# code-server
resource "coder_app" "code-server" {
  agent_id     = coder_agent.main.id
  slug         = "code-server"
  display_name = "code-server"
  icon         = "/icon/code.svg"
  url          = "http://localhost:13337?folder=/home/coder"
  subdomain    = false
  share        = "owner"

  healthcheck {
    url       = "http://localhost:13337/healthz"
    interval  = 3
    threshold = 10
  }
}

resource "kubernetes_persistent_volume_claim" "home" {
  metadata {
    name      = "coder-${lower(data.coder_workspace_owner.me.name)}-${lower(data.coder_workspace.me.name)}-home"
    namespace = var.namespace
    labels = {
      "app.kubernetes.io/name"     = "coder-pvc"
      "app.kubernetes.io/instance" = "coder-pvc-${lower(data.coder_workspace_owner.me.name)}-${lower(data.coder_workspace.me.name)}"
      "app.kubernetes.io/part-of"  = "coder"
      //Coder-specific labels.
      "com.coder.resource"       = "true"
      "com.coder.workspace.id"   = data.coder_workspace.me.id
      "com.coder.workspace.name" = data.coder_workspace.me.name
      "com.coder.user.id"        = data.coder_workspace_owner.me.id
      "com.coder.user.username"  = data.coder_workspace_owner.me.name
    }
    annotations = {
      "com.coder.user.email" = data.coder_workspace_owner.me.email
    }
  }
  wait_until_bound = false
  spec {
    access_modes = ["ReadWriteOnce"]
    resources {
      requests = {
        storage = "${data.coder_parameter.home_disk_size.value}Gi"
      }
    }
  }
}

resource "kubernetes_deployment" "main" {
  count = data.coder_workspace.me.start_count
  depends_on = [
    kubernetes_persistent_volume_claim.home
  ]
  wait_for_rollout = false
  metadata {
    name      = "coder-${lower(data.coder_workspace_owner.me.name)}-${lower(data.coder_workspace.me.name)}"
    namespace = var.namespace
    labels = {
      "app.kubernetes.io/name"     = "coder-workspace"
      "app.kubernetes.io/instance" = "coder-workspace-${lower(data.coder_workspace_owner.me.name)}-${lower(data.coder_workspace.me.name)}"
      "app.kubernetes.io/part-of"  = "coder"
      "com.coder.resource"         = "true"
      "com.coder.workspace.id"     = data.coder_workspace.me.id
      "com.coder.workspace.name"   = data.coder_workspace.me.name
      "com.coder.user.id"          = data.coder_workspace_owner.me.id
      "com.coder.user.username"    = data.coder_workspace_owner.me.name
    }
    annotations = {
      "com.coder.user.email" = data.coder_workspace_owner.me.email
    }
  }

  spec {
    replicas = 1
    selector {
      match_labels = {
        "app.kubernetes.io/name" = "coder-workspace"
      }
    }
    strategy {
      type = "Recreate"
    }

    template {
      metadata {
        labels = {
          "app.kubernetes.io/name" = "coder-workspace"
        }
      }
      spec {
        security_context {
          run_as_user = 1000
          fs_group    = 1000
        }

        container {
          name              = "dev"
          image             = "codercom/enterprise-base:ubuntu"
          image_pull_policy = "Always"
          command           = ["sh", "-c", coder_agent.main.init_script]
          security_context {
            run_as_user = "1000"
          }
          env {
            name  = "CODER_AGENT_TOKEN"
            value = coder_agent.main.token
          }
          resources {
            requests = {
              "cpu"    = "250m"
              "memory" = "512Mi"
            }
            limits = {
              "cpu"    = "${data.coder_parameter.cpu.value}"
              "memory" = "${data.coder_parameter.memory.value}Gi"
            }
          }
          volume_mount {
            mount_path = "/home/coder"
            name       = "home"
            read_only  = false
          }
        }

        volume {
          name = "home"
          persistent_volume_claim {
            claim_name = kubernetes_persistent_volume_claim.home.metadata.0.name
            read_only  = false
          }
        }

        affinity {
          // This affinity attempts to spread out all workspace pods evenly across
          // nodes.
          pod_anti_affinity {
            preferred_during_scheduling_ignored_during_execution {
              weight = 1
              pod_affinity_term {
                topology_key = "kubernetes.io/hostname"
                label_selector {
                  match_expressions {
                    key      = "app.kubernetes.io/name"
                    operator = "In"
                    values   = ["coder-workspace"]
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```
**Step - 5**: **Run llama3 in Coder Terminal**

You can click on the terminal created by coder to access a coder environment. Verify and start Ollama services inside your Kubernetes cluster using coder.

Run the below command to run llama3 model

```
ollama run llama3
```

Output

![image](https://github.com/user-attachments/assets/ec096c99-7cf5-4c90-89e8-312b17ecc4b8)

## Key Components of Coder

Coder provides a flexible and scalable solution for setting up remote development environments. It helps developers by simplifying the setup, management, and security of these environments.

1. **coderd**: This is the core service that manages the remote development environments. It provides:
   - A user interface (UI) for managing workspaces.
   - An API for interacting with workspaces.
   - A reverse proxy to route traffic to different workspaces.
   - Agent registration and management.

2. **provisionerd**: This handles infrastructure provisioning tasks using Terraform. It helps set up and manage the underlying resources needed for remote workspaces.

3. **Agents**: These run within each remote workspace and provide services like:
   - SSH access
   - Port forwarding
   - Health checks
   - Automation for startup scripts

#### Deployment Models
1. **Single Region Architecture**:
   - **Components**: One load balancer, multiple `coderd` instances, and workspaces in the same region.
   - **Features**: High availability with multiple instances, simple setup for smaller deployments, and use of a lightweight container runtime for efficiency.

2. **Multi-Region Architecture**:
   - **Components**: A global load balancer, regional workspace proxies, multiple `coderd` instances, and workspaces in different regions.
   - **Features**: Optimizes speed and performance by using regional proxies to minimize latency, and manages workspace connections globally.

3. **Multi-Cloud Architecture**:
   - **Components**: `coderd` instances in one region per cloud provider, workspace provisioners and proxies across multiple clouds.
   - **Features**: Reduces risk from cloud-specific outages and leverages different cloud provider features.

4. **Air-Gapped Architecture**:
   - **Components**: Internal resources with no internet access, local package and plugin repositories, and secure data transfer.
   - **Features**: Used for highly secure environments where internet access is restricted. It includes offline installation and local management of resources.

5. **Dev Containers** (Experimental):
   - **Components**: Workspace templates with custom Docker images built using `envbuilder`.
   - **Features**: Allows customization of development environments with specific features tailored to project needs.

Now let's discuss how it works:
1. **Workspace Creation**: Administrators use Coder to set up remote workspaces. These workspaces are configured with required resources like virtual machines or Kubernetes clusters.
2. **Remote Access**: Developers access these workspaces through the web browser or SSH. The `coderd` service manages connections and routing.
3. **Resource Management**: `provisionerd` uses Terraform to provision and manage the underlying infrastructure for the workspaces.
4. **Security and Compliance**: Coder isolates development environments from local desktops, enhancing security and ensuring compliance with policies.
5. **Scalability**: Coder’s architecture supports scaling by adding more `coderd` instances, load balancers, and workspaces as needed, depending on the deployment model used.


## What Coder is Not

It's important to clarify what Coder does not aim to be:

- **Not an IaC Platform**: The coder supports Terraform integration but is not limited to infrastructure as code functionality.
- **Not a DevOps/CI Platform**: Coder includes best practices for development but does not manage deployment cycles.
- **Not an Online IDE**: Coder supports major IDEs over secure connections but is not replacing them.
- **Not a Collaboration Platform**: Collaboration is supported through external tools like Git and IDE extensions.
- **Not SaaS/Fully-Managed**: The coder requires hosting on a cloud service or private data center, which gives users full control over their deployment.


## Why Choose Coder?

While there are several remote development platforms available, Coder is different because it also provides infrastructure layers which is helpful for creating development environments:

- It supports various workspaces, including ARM, Windows, Linux, and macOS.
- It enables persistent workspaces across all the environments and also offers cloud hosting. 
- Coder also includes production-ready templates for major cloud platforms like AWS EC2, Azure, Google Cloud, and Kubernetes, further simplifying deployment.

## Getting Started

![image](https://github.com/user-attachments/assets/b5585824-30f5-403a-b141-42473ff1badf)

- **Contribute to the Project**: Visit the Coder [GitHub repository](https://github.com/coder/coder) to contribute to its development.
- **Learn more**: Read the [documentation](https://coder.com/docs) in order to learn more.
- **Install Coder**: Install the tool [here](https://coder.com/docs/install) and start collaborating with you team mates.
- **Join the Community**: Got questions or feedback? Join the Coder [Discord ](https://discord.com/invite/coder)community to connect with fellow developers, share insights, and access various to enhance your cloud journey with Choreo. 

## Conclusion

Due to limited hardware resources and time-consuming setup processes, developers often need more support between their local and production environments. These issues can reduce productivity and the time needed to deliver the software. However, Coder addresses these challenges by offering a platform for managing cloud-based development environments. By automating the setup and enhancing security, the Coder helps streamline the development process. Its features, like remote workspaces and scalable infrastructure, make it easier for developers to collaborate and build applications efficiently. Coder’s flexible deployment models and integration options provide a secure and optimized solution for modern development needs.
