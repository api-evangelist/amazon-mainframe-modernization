---
title: "Gain organization-wide visibility into software licensing with AWS License Manager license asset groups"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/gain-organization-wide-visibility-into-software-licensing-with-aws-license-manager-license-asset-groups/"
date: "Thu, 12 Feb 2026 00:23:37 +0000"
author: "Chase Lindeman"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p>Enterprises are under increasing pressure to optimize software licensing costs, maintain vendor compliance, and make data-driven renewal decisions while managing fragmented license data across multiple Amazon Web Services (AWS) accounts and regions. Centralizing and governing this license data facilitates cost optimization, audit readiness, and strategic procurement planning.</p> 
<p>In this blog post, we’ll show you how <a href="https://aws.amazon.com/license-manager/">AWS License Manager</a> helps you build comprehensive visibility into your commercial software portfolio through automated discovery and centralized license asset groups. You can use License Manager to standardize license management processes and support your compliance, cost optimization, and renewal planning initiatives. We’ll show how to get started with AWS License Manager, providing step-by-step guidance on how to use <strong>automated discovery</strong> and <strong>license asset groups</strong> to track software licenses across your organization. These features will help teams monitor consumption, identify optimization opportunities, and maintain audit-ready documentation for enterprise-wide compliance and cost management.</p> 
<h2>The software licensing challenge</h2> 
<p>When managing hundreds or thousands of Microsoft SQL Server, Windows Server, Red Hat, or Oracle licenses across multiple regions and AWS accounts, you may face time-consuming, error-prone manual tracking processes. In addition, when audit season arrives, teams scramble to compile spreadsheets and reconcile data from different sources. Meanwhile, unused licenses sit idle while procurement teams purchase more capacity—a cycle that costs organizations millions of dollars annually. With licenses spread across dozens of AWS accounts and multiple regions, enterprises lack a centralized view of what they own, what they’re using, and what’s sitting idle, making it nearly impossible to maintain accurate license counts or plan renewals based on actual usage.</p> 
<h2>Centralized software asset management with AWS License Manager</h2> 
<p>AWS License Manager now provides centralized software asset management across all AWS regions and accounts in your organization through license asset groups. Instead of manually tracking licenses account-by-account and region-by-region, you can now automatically discover your commercial software installations and gain a unified, product-centric view of your entire software portfolio.</p> 
<h4>Automated Discovery Across Your Organization</h4> 
<p>AWS License Manager automatically discovers commercial software running on <a href="https://aws.amazon.com/ec2/">Amazon Elastic Compute Cloud (Amazon EC2)</a> instances across all regions and accounts in your <a href="https://aws.amazon.com/organizations/">AWS Organization</a>. License Manager continuously tracks your instances and maps them to your license inventory. Use AWS-managed rulesets for popular products like Microsoft SQL Server, Windows Server, Red Hat Enterprise Linux, and SUSE Linux Enterprise Server—or custom rulesets for specialized software.</p> 
<h4>Product-Centric License Tracking</h4> 
<p>Organize licenses by product rather than by account or region. Create license asset groups for each software product you use, such as “SQL-Server-Enterprise” or “Windows-Server-Production,” and License Manager automatically associates all relevant instances across your organization. This product-centric approach makes it easy to understand total consumption, identify optimization opportunities, and plan renewals based on actual usage patterns.</p> 
<h4>Real-Time Dashboard and Analytics</h4> 
<p>Access comprehensive dashboards showing license consumption trends, instance inventories, and utilization metrics. View your top 5 license asset groups at a glance, then drill down to see detailed information for specific products. Track usage trends over time with flexible date ranges, compare self-managed (Bring Your Own License) versus License Included consumption, and identify instances that might be running without proper licensing coverage.</p> 
<h4>Proactive Expiration Alerts</h4> 
<p>Configure license expiration dates and receive proactive <a href="https://aws.amazon.com/sns/">Amazon SNS</a> notifications 7, 30, or 90 days before licenses expire. This gives your procurement team sufficient lead time to negotiate renewals, analyze usage trends to right-size quantities, and avoid emergency purchases at less favorable terms.</p> 
<h4>Audit-Ready Reporting</h4> 
<p>Generate comprehensive reports showing historical license consumption, complete instance inventories, and compliance status. Export reports to CSV or schedule automated delivery to <a href="https://aws.amazon.com/s3/">Amazon S3</a> buckets. When vendors arrive for audits, you can provide accurate documentation in minutes instead of weeks.</p> 
<h2>Prerequisites</h2> 
<ul> 
 <li><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html">AWS Systems Manager (SSM) agent</a> installed on your Amazon EC2 instances.</li> 
 <li><a href="https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html">AWS Organizations</a>, if you plan on using cross- account resource discovery.</li> 
 <li>If you are onboarding for the first time, follow the <a href="https://docs.aws.amazon.com/license-manager/latest/userguide/getting-started.html">License Manager getting started guide</a> to set up all required permissions.</li> 
</ul> 
<h2>Getting started</h2> 
<h4>Step 1: Configure Discovery Settings</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>In the left pane, under <strong>Settings</strong>, choose <strong>License asset discovery and ruleset</strong>.</li> 
 <li>In the <strong>License asset discovery</strong> section, choose <strong>Edit</strong>.</li> 
 <li>For <strong>Organization discovery</strong>, select <strong>Enable</strong> to discover license assets across AWS accounts in your AWS Organization.</li> 
 <li>For <strong>Region discovery</strong>, select the additional AWS regions where you run licensed software, then choose <strong>Save changes</strong>.</li> 
</ol> 
<p><img alt="Enabling Organization discovery with two additional regions." class="aligncenter size-full wp-image-10930" height="444" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG1.png" width="882" /></p> 
<p style="text-align: center;"><em>Figure 1. Enabling Organization discovery with two additional regions.</em></p> 
<p>License Manager will begin discovering commercial software installations across your organization.</p> 
<h4>Step 2: Create License Asset Groups</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>From the left pane, choose <strong>License assets</strong>.</li> 
 <li>In the <strong>License asset group</strong> section, select <strong>Create license asset groups</strong>.</li> 
 <li>In the <strong>License asset group template</strong> section, select the AWS-managed ruleset for product you want to track, then select <strong>Next</strong>.<img alt="Creating a license asset group using AWS-managed rulesets to discover Windows Server licenses." class="aligncenter size-full wp-image-10931" height="766" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG9.png" width="1236" /><em><em><em>Figure 2. Creating a license asset group using AWS-managed rulesets to discover Windows Server licenses.</em></em></em></li> 
 <li>(Optional) For <strong>License asset discovery</strong>, select <em>Enable</em> to discover license assets across your AWS Organization or <em>Disable</em> if you want to limit discovery to only your AWS account, then choose <strong>Submit</strong>.</li> 
</ol> 
<p>Discovery begins automatically and typically completes within 24 hours.</p> 
<h4>Step 3: (Optional) Create Custom Rulesets</h4> 
<p>Custom rulesets provide you with adjustable options to track both granted licenses and self-managed licenses, or to track instance configurations like Amazon EC2 billing codes, Amazon Machine Images (AMI), instance types, and more. For example, the following steps show how to track an AMI used to deploy a Bring Your Own License (BYOL) version of Windows Server.</p> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>In the left pane, under <strong>Settings</strong>, choose <strong>License asset discovery and ruleset</strong>.</li> 
 <li>In the <strong>License asset ruleset</strong> section, choose <strong>Create ruleset</strong>.</li> 
 <li>Provide a <strong>Ruleset name</strong>, then choose <strong>Next</strong>.</li> 
 <li>Do not add inclusion rules for license discovery. To skip this step, choose <strong>Next</strong>.</li> 
 <li>To add inclusion rules for instance discovery, choose <strong>Add rules</strong>.</li> 
 <li>In the <strong>Instances meet condition</strong> field, select <strong>AMI ID</strong>, then select <strong>Equals</strong>.</li> 
 <li>For the value field, enter the AMI ID of the BYOL Windows Server image, then select <strong>Next.</strong><strong><img alt="Configuring AMI discovery matching the BYOL Windows Server AMI ID" class="aligncenter size-full wp-image-10935" height="630" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG3.png" width="1346" /></strong><em>Figure 3. Configuring AMI discovery matching the BYOL Windows Server AMI ID</em></li> 
 <li>Review the ruleset configuration, then choose <strong>Submit</strong>.</li> 
 <li>Select the newly created ruleset from the <strong>My ruleset</strong> tab, then choose <strong>Create license asset group with ruleset</strong>.</li> 
 <li>Provide a <strong>License asset group name</strong>.</li> 
 <li>For the <strong>Usage dimension</strong> drop down list, select <strong>Instances</strong>, then choose <strong>Create license asset groups</strong>.</li> 
</ol> 
<h4>Step 5: View License Asset Groups</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>In the left pane, choose <strong>License assets</strong>.</li> 
 <li style="text-align: left;">License asset groups are shown for both custom rulesets and AWS-managed rulesets.<img alt="Viewing license asset groups configured within the AWS account" class="size-full wp-image-10938 aligncenter" height="520" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG10.png" width="1081" /><em>Figure 4. Viewing license asset groups</em></li> 
 <li>Select a license asset group’s name to see additional details and the discovered resources.<br /> <img alt="License asset group details created from a custom ruleset." class="size-full wp-image-10940 alignleft" height="761" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/custom-ruleset.png" width="952" /></li> 
</ol> 
<p style="text-align: center;"><em>Figure 5. License asset group details created from a custom ruleset.</em></p> 
<h4>Step 5: Monitoring and Reporting</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console.</a></li> 
 <li>In the left pane, choose <strong>License analytics</strong>.</li> 
 <li>Select a single license asset group and time range from the dropdown lists to view its history.<br /> <img alt="License asset group analytics." class="aligncenter size-full wp-image-10943" height="906" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG8.png" width="1592" /><em>Figure 6. License asset group analytics.</em></li> 
 <li>For reporting, choose <strong>Usage reports</strong> in the left pane.</li> 
 <li>Under the <strong>License asset group report</strong> tab, choose <strong>Create license asset group report</strong>.</li> 
 <li>Provide a <strong>License asset group report name</strong> and description.</li> 
 <li>From the <strong>License asset group</strong> drop down, select a license asset group.</li> 
 <li>For the <strong>Date Range</strong>, select the timeframe for the report, then choose <strong>Create usage report</strong>.</li> 
 <li>License reports are generated in CSV format and will be available to download once the report is complete.</li> 
</ol> 
<p><img alt="License asset group report status." class="aligncenter size-full wp-image-10944" height="368" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/reports.png" width="1336" /></p> 
<p style="text-align: center;"><em>Figure 7. License asset group report status.</em></p> 
<p>Review your dashboards regularly to monitor consumption trends, identify unused licenses, and plan renewals. You can also integrate <a href="https://docs.aws.amazon.com/license-manager/latest/userguide/monitoring-cloudwatch.html">Amazon CloudWatch alarms</a> for proactive alerting when consumption approaches license limits.</p> 
<h2>Clean up</h2> 
<h4>Delete license asset groups</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>In the left pane, choose <strong>License assets</strong>.</li> 
 <li>Select the license asset group you want to remove.</li> 
 <li>Select the <strong>Actions</strong> button, then select <strong>Delete</strong>.</li> 
 <li>In the pop-up window, follow the instructions to confirm deletion, then select <strong>Delete</strong>.</li> 
</ol> 
<p><img alt="Deleting a license asset group." class="aligncenter size-full wp-image-10946" height="553" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG4.png" width="802" /></p> 
<p style="text-align: center;"><em>Figure 8. Deleting a license asset group.&nbsp;</em></p> 
<h4>Delete custom rulesets</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>In the left pane, under <strong>Settings</strong>, choose <strong>License asset discovery and ruleset</strong>.</li> 
 <li>In the <strong>License asset ruleset</strong> section, select the <strong>My rulesets</strong> tab.</li> 
 <li>Select the custom ruleset you want to remove.</li> 
 <li>Select the <strong>Actions</strong> button, then select <strong>Delete</strong>.</li> 
 <li>In the pop-up window, follow the instructions to confirm deletion, then select <strong>Delete</strong>.</li> 
</ol> 
<p><img alt="Deleting a license asset group custom ruleset." class="aligncenter size-full wp-image-10947" height="832" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG6.png" width="1488" /></p> 
<p style="text-align: center;"><em>Figure 9. Deleting a license asset group custom ruleset.</em></p> 
<h4>Delete usage reports</h4> 
<ol> 
 <li>Browse to the <a href="https://console.aws.amazon.com/license-manager/">AWS License Manager console</a>.</li> 
 <li>In the left pane, choose <strong>Usage reports</strong>.</li> 
 <li>Select the usage report you want to remove, then select <strong>Delete</strong>.</li> 
 <li>In the pop-up window, follow the instructions to confirm deletion, then select <strong>Delete</strong>.</li> 
</ol> 
<p><img alt="Deleting a license asset group usage report." class="aligncenter size-full wp-image-10948" height="436" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/02/05/LAG7.png" width="1487" /></p> 
<p style="text-align: center;"><em>Figure 10. Deleting a license asset group usage report.&nbsp;</em></p> 
<h2>Conclusion</h2> 
<p>In this blog post, we have reviewed how AWS License Manager helps you build comprehensive visibility into your commercial software portfolio through automated discovery with license asset groups. To get started, visit the <a href="https://aws.amazon.com/license-manager/">AWS License Manager</a> page.</p>
