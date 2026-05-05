---
title: "Reduce Microsoft SQL Server High Availability costs running on Amazon EC2"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/amazon-ec2-reduces-costs-for-microsoft-sql-server-high-availability-deployments/"
date: "Mon, 17 Nov 2025 22:29:35 +0000"
author: "Vikas Babu Gali"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p>AWS has helped hundreds of thousands of customers transition their SQL Server workloads to the cloud since 2008, longer than any other cloud provider. Running business-critical applications often requires Microsoft SQL Server databases to be highly available. Customers choosing to use SQL Server for their mission-critical workloads can achieve high availability by <a href="https://docs.aws.amazon.com/sql-server-ec2/latest/userguide/create-sql-server-on-ec2-instance.html" rel="noopener noreferrer" target="_blank">deploying SQL Server using Always On technology on EC2.</a> SQL Server Always On can be deployed in two main configurations:&nbsp;<a href="https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-ver15" rel="noopener noreferrer" target="_blank">Always On Failover Cluster Instances</a>&nbsp;(FCI) and&nbsp;<a href="https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-ver15" rel="noopener noreferrer" target="_blank">Always On Availability Groups</a>&nbsp;(AGs).</p> 
<p>In our <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/optimize-cpu.html">previous post</a> we showed how to use Optimize CPU for License Included feature for core licensing optimization. In this post, we show you how to reduce<a href="https://docs.aws.amazon.com/sql-server-ec2/latest/userguide/sql-high-availability.html"> SQL Server high availability costs</a> on <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html" rel="noopener noreferrer" target="_blank">Amazon Elastic Compute Cloud (Amazon EC2)</a> by up to 40% using the new Amazon EC2 High Availability for SQL Server feature with <a href="https://docs.aws.amazon.com/sql-server-ec2/latest/userguide/sql-server-on-ec2-amis.html" rel="noopener noreferrer" target="_blank">SQL Server License Included (LI)</a>.</p> 
<h2>Prerequisites</h2> 
<ul> 
 <li>An&nbsp;<a href="https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fportal.aws.amazon.com%2Fbilling%2Fsignup%2Fresume&amp;client_id=signup" rel="noopener noreferrer" target="_blank">AWS account</a></li> 
 <li>Deploy highly available SQL Server LI Enterprise or Standard edition on Amazon EC2. You can use <a href="https://docs.aws.amazon.com/launchwizard/latest/userguide/launch-wizard-sql.html" rel="noopener noreferrer" target="_blank">AWS Launch Wizard</a> to deploy SQL Server if you prefer and still take advantage of the savings.</li> 
 <li>Your EC2 instances must have<a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html"> AWS Systems Manager (SSM)</a> Agent version 3.1.x or later installed and configured for Run Command. For setup instructions, see <a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/manually-install-ssm-agent-windows.html" rel="noopener noreferrer" target="_blank">Systems Manager instructions</a>.</li> 
 <li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/attach-iam-role.html" rel="noopener noreferrer" target="_blank">Attach</a> an <a href="https://aws.amazon.com/iam/" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management (IAM)</a> role to your EC2 instances with the required permissions&nbsp;or use the AWS provided policy <a href="https://docs.aws.amazon.com/sql-server-ec2/latest/userguide/security-iam-awsmanpol.html">AWSEC2SqlHaInstancePolicy</a> and <a href="https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonSSMManagedInstanceCore.html" rel="noopener noreferrer" target="_blank">AmazonSSMManagedInstanceCore</a>.</li> 
</ul> 
<h2>Solution Overview</h2> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-1-13.png"><img alt="Solution Overview of SQL High Availability cluster" class="aligncenter size-full wp-image-10781" height="1789" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/17/image-1-13.png" width="1976" /></a>Figure 1: Solution Overview</p> 
<p>In a typical SQL Server HA deployment as shown in Figure 1, one EC2 instance acts as the active node (Primary Node) while the other acts as the passive/standby node (Secondary Node). This solution uses AWS Systems Manager (SSM) <a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/run-command.html">Run Command</a> document <a href="https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_ListDocuments.html"><strong>AWSEC2-DetectSqlHaState</strong></a> to automatically detect the passive/active state of your SQL Server instances. The SSM Command document runs under the context of Local System User. This user is automatically mapped to the [NT AUTHORITY\SYSTEM] SQL login that has permissions to retrieve HA metadata.</p> 
<p>If your security policies restrict or disable the [NT AUTHORITY\SYSTEM] account, you can alternatively configure <a href="https://aws.amazon.com/secrets-manager/" rel="noopener noreferrer" target="_blank">AWS Secrets Manager</a> to store SQL Server authentication credentials. <a href="https://docs.aws.amazon.com/secretsmanager/latest/userguide/create_secret.html" rel="noopener noreferrer" target="_blank">Create a secret in AWS Secrets Manager</a> with appropriate SQL Server permissions. The minimum permissions required for the SQL Server login to get the information needed to determine SQL HA metadata are :</p> 
<ul> 
 <li> 
  <ul> 
   <li> 
    <ul> 
     <li>VIEW DATABASE STATE : Grants the ability to view conditions about the current database via the database-level dynamic management views or functions.</li> 
     <li>VIEW SERVER STATE : Grants the ability to see server-level configuration information.</li> 
     <li>VIEW ANY DEFINITION : Grants the ability to see the T-SQL code and any metadata for any object within the server.</li> 
     <li>VIEW ANY DATABASE : Grants the ability to see metadata on databases.</li> 
    </ul> </li> 
  </ul> </li> 
</ul> 
<p>In either authentication scenario, the solution continuously monitors your HA configuration state and updates billing automatically. You pay full SQL Server licensing costs only for the active node, while the passive node will only be charged for EC2 Compute and Windows Server licensing costs.</p> 
<h2>Getting Started</h2> 
<h3>Enable Amazon EC2 High Availability for SQL Server LI</h3> 
<p>To enable the passive node benefit, navigate to the EC2 console and select one or more instances running SQL Server Enterprise or Standard edition LI on EC2. For this blog, we’ll select the primary and secondary EC2 instances as shown in Figure 2. From the EC2 console, select your instances, click <strong>Actions</strong>, expand <strong>Instance settings</strong>, and choose <strong>Modify SQL High Availability settings</strong> to begin the configuration process as shown in Figure 2.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-2-7.png"><img alt="" class="aligncenter wp-image-10779" height="586" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-2-7.png" width="1510" /></a></p> 
<p>Figure 2: Modify SQL High Availability settings</p> 
<p><em>Note: Make sure to add all EC2 instances that are part of your SQL Server cluster including Primary and Secondaries.</em></p> 
<p>After choosing <strong>Modify SQL High Availability settings</strong>, you’ll access the <strong>Modify SQL license High Availability settings</strong> wizard. In the Step 1, <strong>“Review prerequisites</strong>” as shown in Figure 3, the wizard verifies two key requirements: the SSM agent installation status and IAM permissions configuration.</p> 
<p>Your EC2 instances must have the SSM agent installed and either the AWS-managed policy <strong>AWSEC2SqlHaInstancePolicy</strong> attached to their instance role, or a custom role with equivalent permissions as listed in prerequisites.</p> 
<p>For this blog as shown in Figure 3, we’re using the IAM role <strong>SSMRoleForInstances</strong> with the required permissions, and the <strong>SSM agent status</strong> shows as “Online.” If the <strong>SSM agent status</strong> shows as “Offline,” follow the instructions in the <a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html" rel="noopener noreferrer" target="_blank">SSM Agent installation guide</a> to install and configure the agent. Once you’ve confirmed these prerequisites are met, choose <strong>Next</strong> to proceed.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-3-7.png"><img alt="" class="aligncenter size-full wp-image-10770" height="594" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-3-7.png" width="1636" /></a></p> 
<p>Figure 3: Step 1-Review prerequisites</p> 
<p>In Step 2, “<strong>Manage SQL High Availability license savings</strong>” configure your SQL Server license settings. You can choose to <strong>Enable</strong> for individual instances or choose the slide toggle to <strong>Enable all instances</strong>. For this blog we selected to enable this on all instances as shown in Figure 4.</p> 
<p><em>Note: In your environment if you have disabled [NT AUTHORITY\SYSTEM] then you’ll need to select the proper AWS Secret that connects to your SQL Server database instance created in prerequisites. </em></p> 
<p>For this blog we select <strong>none</strong> for “SQL Server credentials – optional” After configuring your preferences, choose <strong>Next</strong> to continue.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-4-8.png"><img alt="" class="aligncenter size-full wp-image-10771" height="604" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-4-8.png" width="1635" /></a></p> 
<p>Figure 4: Manage SQL High Availability license savings</p> 
<p>In Step 3 “<strong>Review and apply changes</strong>“, verify your configuration settings before applying changes as shown in Figure 5. Ensure all status indicators appear green, confirming that prerequisites are met for the selected instances. After validating all settings, choose <strong>Apply changes</strong> to enable the SQL Server High Availability feature.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-5-7.png"><img alt="" class="aligncenter size-full wp-image-10772" height="642" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-5-7.png" width="1642" /></a></p> 
<p>Figure 5: Review prerequisites</p> 
<p>After choosing <strong>Apply changes</strong>, you’ll return to the Amazon EC2 console automatically. The instances may take a few minutes to show the updated status. Select your opted-in instance and navigate the new <strong>SQL High Availability</strong> tab to verify your configuration. This tab displays the <strong>SQL High availability status</strong> and <strong>SQL license usage</strong> details for your SQL Server instance as shown in Figure 6.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-6-6.png"><img alt="" class="aligncenter size-full wp-image-10773" height="519" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-6-6.png" width="1639" /></a></p> 
<p>Figure 6: SQL High Availability status.</p> 
<p>The <strong>SQL High Availability</strong> tab displays key metrics for your SQL Server configuration. For the Primary EC2 instance, you’ll observe the <strong>SQL High availability status</strong> showing as “Active” and the <strong>SQL license usage</strong> indicating “Full license included” as shown in Figure 6, which is expected since this is the active node in your HA configuration.</p> 
<p>When viewing the <strong>SQL High Availability status</strong> tab for the Secondary EC2 instance, you’ll see the <strong>High availability status</strong> showing as “Standby” and the <strong>SQL license usage</strong> displaying “Waived,” confirming that the passive node is successfully applied as shown in Figure 7.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-7-7.png"><img alt="" class="aligncenter size-full wp-image-10774" height="510" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/15/image-7-7.png" width="1634" /></a></p> 
<p>Figure 7: SQL High Availability Status for Standby Node</p> 
<h2>Important considerations</h2> 
<ol> 
 <li>A passive SQL Server node must meet <a href="https://www.microsoft.com/licensing/docs/documents/download/SQL_Server_2022_Licensing_guide (1).pdf">Microsoft’s requirements (page 26)</a> to qualify for the benefit, including:<br /> a. Does not serve incoming traffic<br /> b. Does not run active SQL Server workloads<br /> c. Is not a readable secondary (except master, msdb, tempdb, or model databases)<br /> d. There is not a standalone database running outside of the Availability Group (this is specific for AGs only)</li> 
 <li>The option is available with these configurations:<br /> <strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Supported HA deployments</strong>: Always On Availability Groups and Failover Cluster Instances<br /> <strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Supported versions:</strong></li> 
</ol> 
<ul> 
 <li> 
  <ul> 
   <li> 
    <ul> 
     <li> 
      <ul> 
       <li>Microsoft Windows Server 2019 or newer versions</li> 
       <li>Microsoft SQL Server 2017 or newer versions</li> 
       <li>Microsoft SQL Server Enterprise and Standard editions only</li> 
       <li>Windows PowerShell 5.1 or later</li> 
       <li>SQL Server LI on Amazon EC2 running on Windows Server only</li> 
      </ul> </li> 
    </ul> </li> 
  </ul> </li> 
</ul> 
<p>3. The passive instance must be the same size or smaller than the active instance in terms of vCPUs<br /> 4. Supports Multi-AZ deployments within the same region (cross-region not supported)<br /> 5. Compatible with the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/optimize-cpu.html" rel="noopener noreferrer" target="_blank">Optimize CPU for LI</a> feature for core licensing optimization<br /> 6. Only two-node clusters are supported</p> 
<h2>Licensing information</h2> 
<p>SQL Server LI is billed per vCPU per second for on-demand instances on EC2, subject to a four-core licensing minimum for SQL Server. SQL Server LI EC2 instances smaller than four-vCPUs in size will still be billed for four cores of SQL Server. SQL Server EC2 instances using the Passive Node High Availability feature will not be billed for SQL Server LI on the passive secondary SQL Server node, but Windows Server LI costs will still be billed, based on the number of vCPUs of the EC2 instance, and compute costs will still be incurred for the passive secondary node. Windows Server LI is billed per vCPU per second for on-demand instances on EC2, with no minimum vCPU license requirement.</p> 
<p>While this new feature is specific for SQL Server LI on EC2, customers can also BYOL SQL Server on EC2 with License Mobility through Software Assurance, as AWS is an Authorized Mobility Partner. When bringing SQL Server Core licenses with active Software Assurance or eligible subscription, all the vCPUs of the EC2 instance must be licensed, with one core license covering one vCPU. A minimum of four cores of SQL Server licensing must be assigned to each EC2 instance per Microsoft’s <a href="https://www.microsoft.com/licensing/terms/productoffering/SQLServer/EAEAS" rel="noopener noreferrer" target="_blank">Product Terms</a>. SQL Server/CAL licenses are also eligible for License Mobility with Software Assurance or eligible subscriptions, where one Server license is assigned to each EC2 instance, and all users/devices with direct or indirect access have CALs assigned to them. SQL Server CALs must also have active Software Assurance or subscription to be eligible. If deploying SQL Server on Windows Server on shared EC2, Windows Server LI is required per Microsoft’s license terms.</p> 
<h2>Clean Up</h2> 
<p>It is a best practice to delete resources that you are no longer using so that you do not incur unintended charges. You can clean up the resources you created using this tutorial:</p> 
<ul> 
 <li>Terminate your <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html" rel="noopener noreferrer" target="_blank">Amazon EC2 Instances</a></li> 
</ul> 
<h2>Conclusion</h2> 
<p>In this post, we showed you how to enable SQL Server High Availability feature to save costs on Standby License Included Amazon EC2 SQL Server instances. This feature helps you to reduce your SQL HA costs by up to 40%.</p> 
<p>For customers looking to templatize their deployments and take advantage of this optimization, <a href="https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html" rel="noopener noreferrer" target="_blank">AWS CloudFormation</a> support will come soon. The new feature can also be used through the <a href="https://docs.aws.amazon.com/ec2/latest/devguide/ec2-api-intro.html" rel="noopener noreferrer" target="_blank">AWS API</a>. This feature may be terminated, in which case AWS will provide you with as much prior notice as is reasonably practicable under the circumstances.</p> 
<hr />
