---
title: Network Authorization for CockroachDB Cloud Clusters
summary: Learn about the network authorization features for {{ site.data.products.db }} clusters.
toc: true
docs_area: manage
---

{{ site.data.products.dedicated }} allows explicit configurations of the networks that can access your cluster.

To prevent denial-of-service attacks, brute force password attacks, and other forms of malicious activity, Cockroach Labs recommends restricting your network to allow access only from specific IP address ranges controlled by your organization. These might include specific networks for your application deployments, hardened administrator access points, or disaster recovery pipelines.

## Options for controlling network access

You can authorize network access to your cluster by:

- [Adding an authorized range of public IP addresses](#ip-allowlisting).
- Setting up [Google Cloud Platform (GCP) Virtual Private Cloud (VPC) peering](#vpc-peering) or [Amazon Web Service (AWS) PrivateLink](#aws-privatelink) for your cluster (Dedicated clusters only). Access via [GCP VPC peering](#vpc-peering) or [AWS PrivateLink](#aws-privatelink) avoids traversing the public network, and therefore offers several advantages:

    - Enhanced network security (no access through public IPs i.e. no transit over public networks).
    - Direct connection from application deployment that do not have static public IPs
    - Reduced network latency.
    - This option can also be helpful if you need more than the current maximum limit of authorized networks per cluster.

**Prerequisite**: {% include cockroachcloud/cluster-operator-prereq.md %}

{{site.data.alerts.callout_success}}
Use PrivateLink, GCP VPC Peering, or AWS PrivateLink) if:

- You need to allowlist more defined IP address ranges than allowed by the maximum (20 for Dedicated clusters and 50 for Serverless).
- Your servers’ IP addresses are not static.
- You want avoid exposing your cluster to the public internet.

Learn more about [Private Clusters (Preview)](private-clusters.html), which offer enhanced cluster security. A private cluster's nodes have no public IP addresses.
{{site.data.alerts.end}}

{{site.data.alerts.callout_info}}

During [limited access](/docs/{{site.versions["stable"]}}/cockroachdb-feature-availability.html), neither Azure Private Link nor private clusters are available for {{ site.data.products.dedicated }} clusters on Azure. Refer to [{{ site.data.products.dedicated }} on Azure](cockroachdb-dedicated-on-azure.html).
{{site.data.alerts.end}}

## Cluster default network configuration

{{ site.data.products.dedicated }} and Serverless clusters differ in their default network configuration:

- On creation, a Serverless cluster is open to all traffic as it is created with a `0.0.0.0/0` IP allowlist entry.
- On creation, a Dedicated cluster is "locked down" and has no access until an authorized network is created.

{{ site.data.products.db }} clusters can only accept SQL connections from [allowed IP addresses](#ip-allowlisting).

## IP allowlisting

Authorized network access can be managed from the {{ site.data.products.db }} console Network Authorization page at:

`https://cockroachlabs.cloud/cluster/{ your cluster UUID}/networking`

Serverless and Dedicated clusters support different maximum numbers of IP allowlist rules:

Cluster Type | IP allowlist rule max
--------|------------
Dedicated|20
Serverless|50

{{site.data.alerts.callout_info}}
While developing and testing your application, you may add `0.0.0.0/0` to the allowlist, which allows all networks. However, before moving into production, make sure you delete the `0.0.0.0/0` network.
{{site.data.alerts.end}}

If your application servers’ IP addresses are not static, or you want to limit your cluster's exposure to the public network, you can connect to your {{ site.data.products.dedicated }} clusters using VPC Peering or AWS PrivateLink instead.

Refer to:

- [Connect to a {{ site.data.products.serverless }} Cluster: Authorize your network](connect-to-a-serverless-cluster.html#authorize-your-network).
- [Connect to a {{ site.data.products.dedicated }} Cluster: Authorize your network](connect-to-your-cluster.html#authorize-your-network).

## VPC peering

If you select GCP as your cloud provider while [creating your {{ site.data.products.dedicated }} cluster](create-your-cluster.html), you can use [Google Cloud's VPC Network Peering](https://cloud.google.com/vpc/docs/vpc-peering) feature to connect your GCP application directly to your {{ site.data.products.dedicated }} cluster using internal IP addresses, thus limiting exposure to the public network and reducing network latency.

GKE users should note that we recommend deploying your application to a VPC-native cluster that uses [alias IP addresses](https://cloud.google.com/kubernetes-engine/docs/how-to/alias-ips). If you are connecting from a [routes-based GKE cluster](https://cloud.google.com/kubernetes-engine/docs/how-to/routes-based-cluster) instead, you will have to [export custom routes](https://cloud.google.com/vpc/docs/vpc-peering#importing-exporting-routes). {{ site.data.products.db }} will import your custom routes by default.

Setting up a VPC peering connection between your {{ site.data.products.dedicated }} cluster and GCP application is a two-part process:

1. [Configure the IP range and size while creating the {{ site.data.products.dedicated }} cluster](create-your-cluster.html#step-7-enable-vpc-peering-optional)
1. [Establish a VPC Peering connection after creating the cluster](connect-to-your-cluster.html#establish-gcp-vpc-peering-or-aws-privatelink)

{{site.data.alerts.callout_info}}
Self-service VPC peering setup is not supported for {{ site.data.products.dedicated }} clusters deployed before March 5, 2020. If your cluster was deployed before March 5, 2020, you will have to [create a new cluster](create-your-cluster.html) with VPC peering enabled, then [export your data](use-managed-service-backups.html) from the old cluster to the new cluster. If your cluster was deployed on or after March 5, 2020, it will be locked into {{ site.data.products.dedicated }}'s default IP range (`172.28.0.0/14`) unless you explicitly configured a different IP range during cluster creation.
{{site.data.alerts.end}}

## AWS PrivateLink

If your cloud provider is AWS, you can use [AWS PrivateLink](https://aws.amazon.com/privatelink/) to securely connect your AWS application with your {{ site.data.products.dedicated }} cluster using a private endpoint. Like VPC Peering, a PrivateLink connection will prevent your traffic from being exposed to the public internet and reduce network latency. 

Refer to: [Managing AWS PrivateLink for a Dedicated Cluster](aws-privatelink.html).
