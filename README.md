# buildkite

# CICD

A CI/CD pipeline is a series of automated steps that helps software teams deliver code faster, safer, and more reliably. By automating the build, test, and deployment processes, CI/CD pipelines reduce the risk of manual errors, ensure consistent output quality, and enable rapid iteration and deployment of applications.

# Buildkite

Buildkite is a cloud-based continuous integration (CI) and continuous delivery (CD) platform designed to help development teams automate the building, testing, and deployment of their code. It provides a range of features and capabilities to streamline and enhance the development lifecycle.

# Create a Agent Token to Install on GCP GKE Cluster

Using the Buildkite Interface

To create an agent token for a cluster using the Buildkite interface:
 1.Select Agents in the global navigation to access the Clusters page.
 2.Select the cluster that will be associated with this agent token.
 3.Select Agent Tokens > New Token.
 4.In the Description field, enter an appropriate description for the agent token.
 5.If you need to restrict which network addresses are allowed to use this agent token, enter these addresses CIDR notations
 into the Allowed IP Addresses field.
 6.Select Create Token.

# Running the agent on Google Kubernetes Engine

Google Kubernetes Engine can run the agent as a Docker container using Kubernetes. To run Docker–based builds, ensure the container is started with a privileged security context and mount the Docker socket as a volume.

1. We've to create a secret using the token that we created before.
2. Mention the created token in the following command to create a kubernetes secrets
3. $ kubectl create secret generic buildkite-agent --from-literal=token=<INSERT-YOUR-AGENT-TOKEN-HERE>
4. Create a Kubernetes deployment to start an agent. 
[Note: Agent yaml and Secret yaml is available in agent-k8s directory]

After waiting another minute, verify that your agent pod is running:

$ kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
buildkite-agent-67d54b9b88-jnzxg   2/2     Running   0          22s

And that the Buildkite agent has registered successfully.

$ kubectl logs buildkite-agent-67d54b9b88-jnzxg buildkite

   _           _ _     _ _    _ _                                _
  | |         (_) |   | | |  (_) |                              | |
  | |__  _   _ _| | __| | | ___| |_ ___    __ _  __ _  ___ _ __ | |_
  | '_ \| | | | | |/ _` | |/ / | __/ _ \  / _` |/ _` |/ _ \ '_ \| __|
  | |_) | |_| | | | (_| |   <| | ||  __/ | (_| | (_| |  __/ | | | |_
  |_.__/ \__,_|_|_|\__,_|_|\_\_|\__\___|  \__,_|\__, |\___|_| |_|\__|
                                                 __/ |
 https://buildkite.com/agent                    |___/

2021-05-26 12:55:27 NOTICE Starting buildkite-agent v3.29.0 with PID: 7
2021-05-26 12:55:27 NOTICE The agent source code can be found here: https://github.com/buildkite/agent
2021-05-26 12:55:27 NOTICE For questions and support, email us at: hello@buildkite.com
2021-05-26 12:55:27 INFO   Configuration loaded path=/buildkite/buildkite-agent.cfg
2021-05-26 12:55:27 INFO   Registering agent with Buildkite...
2021-05-26 12:55:27 WARN   Failed to find unique machine-id: machineid: machineid: open /etc/machine-id: no such file or directory
2021-05-26 12:55:27 INFO   Successfully registered agent "buildkite-agent-67d54b9b88-jnzxg-1" with tags []
2021-05-26 12:55:27 INFO   Starting 1 Agent(s)
2021-05-26 12:55:27 INFO   You can press Ctrl-C to stop the agents
2021-05-26 12:55:27 INFO   buildkite-agent-67d54b9b88-jnzxg-1 Connecting to Buildkite...
2021-05-26 12:55:27 INFO   buildkite-agent-67d54b9b88-jnzxg-1 Waiting for work...

You're successfully running a Buildkite agent!

# Deploying to Kubernetes

This tutorial demonstrates deploying to Kubernetes using Buildkite best practices.

The tutorial uses one pipeline for tests and another for deploys. The test pipeline runs tests and push a Docker image to a registry. The deploy pipelines uses the DOCKER_IMAGE environment variable to create a Kubernetes deployment using kubectl. Then, you'll see how to link them together to automate deploys from main branch.

# Create a deploy pipeline 

This section covers creating a new Buildkite pipeline that loads steps from .buildkite/pipeline.yml [ Note: https://buildkite.com/docs/pipelines/deployments/to-kubernetes --> Document Referred ]

This Buildkite pipeline automates the process of building, testing, and deploying an application to Kubernetes. 

The first step will be a pipeline upload using our new deploy pipeline YAML file. Create a new pipeline. Enter buildkite-agent pipeline upload .buildkite/pipeline.yml in the commands to run field.

# Kubernetes Deployment Permission Issue in Buildkite Pipeline

# Issue:

While deploying applications to Kubernetes via a Buildkite pipeline, the following error was encountered:

"deployments.apps is forbidden: User 'system:serviceaccount:default:default' cannot create resource 'deployments' in API group 'apps' in the namespace 'default' "

This error occurs because the default service account used in the Buildkite pipeline does not have the necessary permissions to create deployments in Kubernetes.

# Resolution:

To resolve this issue, we applied a ClusterRoleBinding that grants the default service account (default) cluster-admin privileges, allowing it to manage deployments. The RBAC YAML configuration inside k8s-rbac directory was applied in the GKE Cluster
