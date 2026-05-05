---
title: "Deploying Microsoft Exchange Server with AWS Managed Microsoft AD Hybrid Edition"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/deploying-microsoft-exchange-server-with-aws-managed-microsoft-ad-hybrid-edition/"
date: "Thu, 26 Mar 2026 14:22:26 +0000"
author: "Tamer Sherif"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p><span style="color: #0f141a;">In today’s hybrid IT landscape, many organizations are strategically moving their infrastructure to </span><span style="color: #0f141a;">Amazon Web Services (AWS) </span><span style="color: #0f141a;">while maintaining certain on-premises services. Microsoft Exchange Server remains a critical communication platform for </span><span style="color: #0f141a;">businesses and</span><span style="color: #0f141a;"> deploying it in AWS provides excellent opportunities to leverage cloud scalability and reliability. </span><span style="color: #0f141a;">Whether you’re </span><span style="color: #0f141a;">migrating</span><span style="color: #0f141a;"> an existing Exchange infrastructure or implementing a new deployment, this blog</span><span style="color: #0f141a;"> post</span><span style="color: #0f141a;"> will walk you through the process of deploying Microsoft Exchange Server in AWS. </span></p> 
<p><span style="color: #0f141a;">Included is</span><span style="color: #0f141a;"> the architectural approach for deploying Exchange Server 2019 and Subscription Edition with </span><a href="https://docs.aws.amazon.com/directoryservice/latest/admin-guide/aws-hybrid-directory.html" rel="noopener noreferrer">AWS Managed Microsoft AD (Hybrid Edition)</a><span style="color: #0f141a;">, focusing on design considerations and deployment patterns. It specifically addresses key deployment </span><span style="color: #0f141a;">considerations for</span><span style="color: #0f141a;"> Hybrid Edition and details the critical forest preparation steps required for successful Exchange implementation. </span><span style="color: #0f141a;">It </span><span style="color: #0f141a;">does not cover detailed step-by-step infrastructure configuration or post-deployment Exchange configuration procedures.</span></p> 
<p><strong>Note:</strong> <span style="color: #0f141a;"> Exchange</span><span style="color: #0f141a;"> Server 2019 reached end of support on October 14, 2025. By migrating to Exchange Server Subscription Edition (SE), you’ll benefit from ongoing security updates, patches, and Microsoft support to keep your environment protected</span>.</p> 
<h1>Why Exchange Server with AWS Managed Microsoft AD (Hybrid Edition)</h1> 
<p>Deploying Microsoft Exchange Server with AWS Managed Microsoft AD Hybrid Edition offers organizations a streamlined approach to modernizing their communication infrastructure while preserving existing Active Directory identity systems and native Active Directory schema extension capabilities. This integration extends your on-premises or cloud self-managed Active Directory domain into AWS without requiring domain restructuring or identity synchronization, enabling native integration with AWS applications and services and leveraging AWS elasticity, reliability, and global reach.</p> 
<p><span style="color: #0f141a;"><strong>Note:</strong></span><span style="color: #0f141a;"> This guide uses Microsoft Exchange Server 2019. All commands, procedures, and screenshots </span><span style="color: #0f141a;">captured </span><span style="color: #0f141a;">are from </span><span style="color: #0f141a;">Exchange Server 2019.</span></p> 
<h1>Solution Overview</h1> 
<h2>Microsoft Exchange Deployment Topologies</h2> 
<h3>Option 1: Single-Region Topology with High Availability</h3> 
<p>This topology deploys all Exchange components within a single <a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html" rel="noopener noreferrer">AWS region</a>, utilizing multiple <a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html" rel="noopener noreferrer">Availability Zones</a> (AZs) for high availability.</p> 
<ul> 
 <li><strong>Configuration</strong>: Two or more Exchange servers deployed across different Availability Zones within the same AWS region.</li> 
 <li><strong>Directory Services</strong>: Hybrid Edition with two AWS-managed and two self-managed domain controllers.</li> 
 <li><strong>High Availability</strong>: Database Availability Group (DAG) <a href="https://learn.microsoft.com/en-us/exchange/high-availability/manage-ha/manage-dags" rel="noopener noreferrer">configured</a> across AZs with automatic failover.</li> 
 <li><strong>Load Balancing</strong>: A <a href="https://aws.amazon.com/elasticloadbalancing/network-load-balancer/" rel="noopener noreferrer">Network Load Balancer</a> distributes client connections. See <a href="https://learn.microsoft.com/en-us/exchange/architecture/client-access/load-balancing" rel="noopener noreferrer">Load balancing in Exchange Server</a> and <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-network-load-balancer.html" rel="noopener noreferrer">Elastic Load Balancing</a>.</li> 
 <li><strong>Ideal for</strong>: Organizations with centralized operations or users primarily in one geographic region</li> 
</ul> 
<p><img alt="This figure shows how to deploy Exchange server in AWS single region topology with high availiability" class="aligncenter wp-image-10999 size-full" height="791" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture1-2.png" width="1111" /></p> 
<p style="text-align: center;">Figure 1 Deploying Exchange 2019 on AWS in a single region</p> 
<h3>Option 2: Multi-Region Topology</h3> 
<p>This topology deploys Exchange servers across multiple AWS regions to provide global coverage and disaster recovery:</p> 
<ul> 
 <li><strong>Configuration</strong>: Exchange server deployments in two or more AWS regions.</li> 
 <li><strong>Directory Services</strong>: Hybrid Edition with two AWS-managed and two self-managed domain controllers in the primary region and additional self-managed domain controllers in secondary regions to be fully resilient.</li> 
 <li><strong>High Availability</strong>: Single or Multiple DAGs <a href="https://learn.microsoft.com/en-us/exchange/high-availability/manage-ha/manage-dags" rel="noopener noreferrer">configured</a> with cross-region database copies.</li> 
 <li><strong>Load Balancing</strong>: Regionally deployed Network Load Balancer distributing client connections. See <a href="https://learn.microsoft.com/en-us/exchange/architecture/client-access/load-balancing" rel="noopener noreferrer">Load balancing in Exchange Server</a> and <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-network-load-balancer.html" rel="noopener noreferrer">Elastic Load Balancing</a>.</li> 
 <li><strong>Ideal for</strong>: Global organizations with users distributed across multiple regions or disaster recovery capabilities.</li> 
</ul> 
<p><img alt="This figure shows how to deploy Exchange server in AWS multi region topology" class="wp-image-10986 aligncenter" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture2-2.png" /></p> 
<p style="text-align: center;">Figure 2: Deploying Exchange 2019 on AWS in multi-region</p> 
<h3>Option 3: Hybrid Topology</h3> 
<p>This topology maintains Exchange servers both on-premises and in AWS for gradual migration:</p> 
<ul> 
 <li><strong>Configuration</strong>: Exchange servers deployed in AWS with connectivity to existing on-premises Exchange.</li> 
 <li><strong>Directory Services</strong>: Hybrid Edition with two AWS-managed and two self-managed domain controllers extending your on-premises Active Directory.</li> 
 <li><strong>Connectivity</strong>: <a href="https://aws.amazon.com/directconnect/" rel="noopener noreferrer">AWS Direct Connect</a> or Site-to-Site VPN for secure, reliable connectivity.</li> 
 <li><strong>Mail Flow</strong>: Configured for seamless routing between on-premises and Exchange environments in AWS.</li> 
 <li><strong>High Availability</strong>: Multiple DAGs <a href="https://learn.microsoft.com/en-us/exchange/high-availability/manage-ha/manage-dags" rel="noopener noreferrer">configured</a> for on-premise and AWS across AZs with automatic failover.</li> 
 <li><strong>Load Balancing</strong>: Regional and on-premises Network Load Balancer distributing client connections, see <a href="https://learn.microsoft.com/en-us/exchange/architecture/client-access/load-balancing" rel="noopener noreferrer">Load balancing in Exchange Server</a> and <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-network-load-balancer.html" rel="noopener noreferrer">Elastic Load Balancing</a>.</li> 
 <li><strong>Ideal for</strong>: Organizations transitioning from on-premises to cloud infrastructure over time.</li> 
</ul> 
<p><img alt="This figure shows how to deploy Exchange server in hybrid topology" class="aligncenter wp-image-10987 size-full" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture3-2.png" /></p> 
<p style="text-align: center;">Figure 3 Hybrid deployment of Exchange 2019</p> 
<p><strong>Note:</strong> While the diagrams above show Exchange servers and domain controllers in the same <a href="https://aws.amazon.com/vpc/" rel="noopener noreferrer">Amazon Virtual Private Cloud (VPC)</a>, you can deploy them in separate VPCs with <a href="https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html" rel="noopener noreferrer">VPC peering</a> or <a href="https://aws.amazon.com/transit-gateway/" rel="noopener noreferrer">AWS Transit Gateway</a> for additional network isolation and security.</p> 
<h1>Prerequisites</h1> 
<p>The following are the prerequisites to <span style="color: #0f141a;">proceed with the Exchange Server 2019 deployment</span><span style="color: #0f141a;">: </span></p> 
<h2>AWS Requirements</h2> 
<ul> 
 <li>An <a href="https://aws.amazon.com/console/" rel="noopener noreferrer">AWS account</a>.</li> 
 <li>An understanding of <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html" rel="noopener noreferrer">Amazon Machine Images (AMIs)</a>, <a href="https://aws.amazon.com/ec2/" rel="noopener noreferrer">Amazon Elastic Compute Cloud (EC2)</a> instances, and how to <a href="https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-windows-launch-instance.html" rel="noopener noreferrer">launch a Windows Server Amazon EC2 instance</a>.</li> 
 <li>Familiarity with VPC networking concepts.</li> 
</ul> 
<h2>Infrastructure Planning</h2> 
<ul> 
 <li><strong>Region and Availability Zone Selection</strong>: <span style="color: #0f141a;">Choose </span><span style="color: #0f141a;">a </span><span style="color: #0f141a;">primary AWS region based on user proximity and compliance requirements</span><span style="color: #0f141a;">. Within that region, deploy across multiple Availability Zones for high availability. For disaster recovery, consider adding a secondary region</span>.</li> 
 <li><strong>EC2 Instance Sizing</strong>: Size Exchange servers based on user count and performance requirements; select appropriate instance types for domain controllers and witness servers.</li> 
 <li><strong>Storage Configuration</strong>: Configure <a href="https://aws.amazon.com/ebs/" rel="noopener noreferrer">Amazon Elastic Block Store (EBS)</a> volumes with adequate IOPS for Exchange database performance.</li> 
 <li><strong>SSL/TLS Certificates</strong>: Obtain valid public certificates for Exchange client access services.</li> 
</ul> 
<h2>Active Directory Requirements</h2> 
<ul> 
 <li><strong>Domain Environment</strong>: Extend an existing or install a new single-domain forest with at least two writable, self-managed domain controllers running Windows Server 2012 R2 or later, with Windows Server 2012 R2 or 2016 functional level. <a href="https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-" rel="noopener noreferrer">See <strong>Install a New Windows Server 2012 Active Directory Forest</strong></a> and <a href="https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/active-directory-functional-levels" rel="noopener noreferrer">Active Directory Domain Services functional levels</a>.</li> 
 <li><strong>AWS Extension</strong>: Hybrid Edition Domain Controllers properly configured to extend your AD environment. Follow <a href="https://aws.amazon.com/blogs/modernizing-with-aws/extend-your-active-directory-domain-to-aws-with-aws-managed-microsoft-ad-hybrid-edition/" rel="noopener noreferrer">AWS Managed Microsoft AD (Hybrid Edition)</a>.</li> 
 <li><strong>FSMO Roles</strong><span style="color: #0f141a;">: </span><span style="color: #0f141a;">All FSMO roles must be hosted on </span><span style="color: #0f141a;">self-</span><span style="color: #0f141a;">managed writable domain controllers located in the same Active Directory site where Exchange </span><span style="color: #0f141a;">s</span><span style="color: #0f141a;">chema</span> <span style="color: #0f141a;">and domain preparation operations will </span><span style="color: #0f141a;">run</span><span style="color: #0f141a;">.</span></li> 
 <li><strong>AD sites configuration:</strong> 
  <ul> 
   <li>Co-locate Exchange servers and writable domain controllers in the same Active Directory site.</li> 
   <li>Hybrid Edition DCs are automatically placed in site AWS-DirectoryService-&lt;region-name&gt; (e.g., AWS-DirectoryService-us-west-1).</li> 
   <li>VPC CIDR ranges are automatically added to the appropriate “AWS-DirectoryService-&lt;region-name&gt;” site for proper site-awareness.</li> 
  </ul> </li> 
 <li><strong>DNS Configuration</strong>: Configure <a href="https://learn.microsoft.com/en-us/windows-server/networking/dns/quickstart-install-configure-dns-server?tabs=powershell" rel="noopener noreferrer">DNS forwarding</a> and conditional forwarders between on-premises DNS servers, self-managed domain controllers, and AWS Managed Microsoft AD DNS for seamless name resolution across hybrid environments and AWS infrastructure endpoints.</li> 
</ul> 
<h2>Required Permissions for Exchange Deployment</h2> 
<h3>Active Directory Permissions</h3> 
<p>Administrative account with membership in:</p> 
<ul> 
 <li>Schema Admins group.</li> 
 <li>Enterprise Admins group.</li> 
 <li>Domain Admins group.</li> 
</ul> 
<h3>AWS IAM Roles and Permissions</h3> 
<p>Create dedicated IAM roles with minimal required permissions:</p> 
<ul> 
 <li>EC2 instance permissions for <a href="https://aws.amazon.com/systems-manager/" rel="noopener noreferrer">AWS Systems Manager</a> <a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-permissions.html" rel="noopener noreferrer">access</a> for patch management.</li> 
 <li><a href="https://aws.amazon.com/cloudwatch/" rel="noopener noreferrer">Amazon CloudWatch</a> logging permissions.</li> 
</ul> 
<h3>Infrastructure Components</h3> 
<ul> 
 <li><strong>Witness Server</strong>: Additional Windows Server instance (non-Exchange) configured as File Share Witness (FSW).</li> 
 <li><strong>Networking</strong>: Properly configured security groups, NACLs, and routing to enable inter-server communication as per this <a href="https://learn.microsoft.com/en-us/exchange/plan-and-deploy/deployment-ref/network-ports" rel="noopener noreferrer">reference</a>.</li> 
 <li><strong>DNS Configuration</strong>: Fully functional DNS resolution between all servers in the environment.</li> 
</ul> 
<p><span style="color: #0f141a;"><strong>Reference:</strong></span> <a href="https://docs.microsoft.com/en-us/exchange/plan-and-deploy/system-requirements" rel="noopener noreferrer"><span style="color: #0f141a;">Exchange Server 2019 and SE system requirements</span></a></p> 
<h2>Deploy AWS Infrastructure</h2> 
<p>Before deploying Exchange Server and configuring Active Directory integration, establish the foundational AWS infrastructure components based on your desired design:</p> 
<h3>VPC Structure and Networking</h3> 
<ul> 
 <li>Create a VPC with the necessary CIDR blocks for your organization.</li> 
 <li>Design a subnet architecture across multiple AZs or Regions (public, private, and management subnets).</li> 
 <li>Configure route tables and internet/NAT gateways as needed.</li> 
 <li>Establish network connectivity (Direct Connect or Site-to-Site VPN) to on-premises environment.</li> 
</ul> 
<h3>AWS Directory Service Setup</h3> 
<ul> 
 <li>Deploy AWS Managed Microsoft AD Hybrid Edition.</li> 
 <li>Ensure proper AD site configuration.</li> 
</ul> 
<h3>Security Configuration</h3> 
<ul> 
 <li>Configure network access control lists (ACLs) for additional network-level security.</li> 
 <li>Configure instance <a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-instance-permissions.html" rel="noopener noreferrer">permissions required for Systems Manager</a>.</li> 
</ul> 
<h2>Deployment Steps</h2> 
<h3>Prepare Active Directory for Exchange</h3> 
<p><strong>Important Note</strong>: If you deploy Exchange in a multi-site Active Directory environment and is not in the same site as the self-managed domain controller that holds the Schema Master role, you cannot prepare Active Directory using the GUI wizard. Instead, follow the guide <a href="https://learn.microsoft.com/en-us/exchange/plan-and-deploy/prepare-ad-and-domains" rel="noopener noreferrer">Exchange Setup command-line</a> to specify the self-managed domain controller with Schema Master role.</p> 
<p><strong>Step 1: Prepare Schema</strong></p> 
<pre><code class="lang-powershell">E:\Setup.exe /IAcceptExchangeServerLicenseTerms_DiagnosticDataON /PrepareSchema /DomainController:DC01.corp.local</code></pre> 
<p><img alt="This command line screen shows the prepare schema command output" class="wp-image-11002 size-full aligncenter" height="783" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture4-1.png" width="1374" /></p> 
<p style="text-align: center;">Figure 4 Prepare schema command output</p> 
<p><strong>Step 2: Prepare AD with organization name</strong></p> 
<pre><code class="lang-powershell">E:\Setup.exe /IAcceptExchangeServerLicenseTerms_DiagnosticDataON /PrepareAD /OrganizationName:"Corp" /DomainController:DC01.corp.local</code></pre> 
<p><img alt="This command line screen shows the prepare AD command output" class="aligncenter wp-image-11003 size-full" height="878" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture5-1.png" width="1376" /></p> 
<p style="text-align: center;">Figure 5 Prepare AD command output</p> 
<p><strong>Step 3: Prepare all domains</strong></p> 
<pre><code class="lang-powershell">E:\Setup.exe /IAcceptExchangeServerLicenseTerms_DiagnosticDataON /PrepareAllDomains</code></pre> 
<p><img alt="This command line screen shows the prepare all domains command output" class="wp-image-11004 size-full aligncenter" height="801" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture6.png" width="1376" /></p> 
<p style="text-align: center;">Figure 6 Prepare all domains command output</p> 
<p><strong>Important Considerations:</strong></p> 
<ul> 
 <li>Replace DC01.corp.local with your fully managed DC that holds FSMO roles.</li> 
 <li>Replace /OrganizationName:”Corp”, with your organization name.</li> 
 <li>Run commands with Schema Admin and Enterprise Admin privileges.</li> 
 <li>Allow sufficient time for AD replication between each step.</li> 
 <li>Verify the successful completion of each step before proceeding to the next.</li> 
</ul> 
<h3>Install Exchange Server</h3> 
<p>After successful Active Directory preparation, you can <a href="https://learn.microsoft.com/en-us/exchange/plan-and-deploy/deploy-new-installations/install-mailbox-role" rel="noopener noreferrer">install Exchange Server</a> using either the command line or the setup GUI.</p> 
<h3>Verify Installation</h3> 
<p>Check Exchange Services using the following PowerShell command:</p> 
<pre><code class="lang-powershell">Get-Service -Name "MSExchange*" | Where-Object {$_.StartType -eq "Automatic"} | Format-Table Name, Status, StartType -AutoSize</code></pre> 
<p><img alt="This command line screen shows installation verification output as part of the installation verification steps" class="wp-image-10991 aligncenter" height="705" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture7-1.png" width="1462" /></p> 
<p style="text-align: center;">Figure 7 Installation verification output</p> 
<p><strong>Verify Exchange Admin Center:</strong></p> 
<ol> 
 <li>Open a browser on the Exchange server.</li> 
 <li>Navigate to https://localhost/ecp.</li> 
 <li>Log in with your domain administrator credentials.</li> 
 <li>Confirm the Exchange Admin Center loads successfully as shown in the screenshot below:</li> 
</ol> 
<p><img alt="This figure shows the Exchange 2019 control admin center as part of the installation verification steps" class="wp-image-10992 aligncenter" height="908" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/10/Picture8-1.png" width="1625" /></p> 
<p style="text-align: center;">Figure 8 Exchange 2019 control panel</p> 
<h3>Post-Installation Tasks</h3> 
<p>Review the recommended post-installation tasks at <a href="https://learn.microsoft.com/en-us/exchange/plan-and-deploy/post-installation-tasks/post-installation-tasks" rel="noopener noreferrer">Exchange Server post-installation tasks</a>.</p> 
<h2>Cleaning Up</h2> 
<p>To avoid incurring charges for unused resources, it’s essential to decommission your Exchange Server deployment in AWS when no longer needed. This cleanup process involves deleting all resources created during implementation.</p> 
<h2>Conclusion</h2> 
<p>Deploying Microsoft Exchange Server on AWS with AWS Managed Microsoft AD Hybrid Edition streamlines communication infrastructure modernization while preserving existing Active Directory identity infrastructure. This approach extends your Active Directory domain directly into AWS, maintaining familiar authentication mechanisms while gaining cloud scalability and reliability. Following the deployment patterns in this guide enables organizations to successfully implement Exchange on AWS infrastructure with enhanced disaster recovery capabilities, allowing IT teams to focus on user experience rather than infrastructure management.</p> 
<p><strong>About the Author</strong></p> 
<p><strong>Tamer Sherif</strong> is a Principal Solutions Architect with 18 years of experience specializing in Microsoft technologies and cloud infrastructure. Tamer focuses on helping organizations successfully migrate and optimize their Microsoft workloads on AWS, with expertise in Active Directory, Exchange Server deployments, and hybrid cloud architectures. He is passionate about enabling customers to leverage the scalability and reliability of AWS while maintaining the familiar Microsoft ecosystem they depend on for their business-critical operations<strong>.</strong></p>
