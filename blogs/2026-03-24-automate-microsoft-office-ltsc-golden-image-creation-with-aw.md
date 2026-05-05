---
title: "Automate Microsoft Office LTSC golden image creation with AWS Image Builder"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/automate-office-ltsc-golden-image-creation-with-aws-image-builder/"
date: "Tue, 24 Mar 2026 11:56:04 +0000"
author: "Ali Alzand"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p><span>Enterprises worldwide rely on Microsoft Office for critical business operations. AWS makes it straightforward to deploy Microsoft Office on individual </span><a href="https://aws.amazon.com/ec2/" rel="noopener" target="_blank"><span>Amazon Elastic Compute Cloud</span></a><span> (Amazon EC2) instances using the </span><a href="https://aws.amazon.com/marketplace/pp/prodview-bh46d5p2hapns" rel="noopener" target="_blank"><span>Office LTSC Professional Plus</span></a><span> or <a href="https://aws.amazon.com/marketplace/pp/prodview-4riznyn4eqlbw" rel="noopener" target="_blank">Office LTSC Standard</a> from <a href="https://aws.amazon.com/marketplace" rel="noopener" target="_blank">AWS Marketplace</a></span><span>, as in the blog post </span><a href="https://aws.amazon.com/blogs/modernizing-with-aws/how-to-set-up-microsoft-office-on-amazon-ec2/" rel="noopener" target="_blank"><span>How to Set Up Microsoft Office on Amazon EC2</span></a><span>. This approach works well for small-scale deployments, one-off instances, and development environments.</span><span>&nbsp;</span></p> 
<p><span>However, you may need to create golden images based on the Marketplace image—pre-configured with Office LTSC plus all required corporate software, security settings, and configurations ready for your teams to use immediately. As you scale your Windows infrastructure on AWS, deploying these customized Microsoft Office environments consistently across hundreds or thousands of instances becomes a significant operational challenge. Manual creation takes hours per image and introduces configuration drift at scale.</span><span>&nbsp;</span></p> 
<p><span>In this blog post, you will learn how to automate the creation of Microsoft Office LTSC golden images using </span><a href="https://aws.amazon.com/image-builder/" rel="noopener" target="_blank"><span>EC2 Image Builder</span></a><span>, complete with automated license validation and compliance checking. I will demonstrate a production-ready </span><a href="https://aws.amazon.com/cloudformation/" rel="noopener" target="_blank"><span>AWS CloudFormation</span></a><span> solution that orchestrates the license activation process within Image Builder workflows, introducing a wait mechanism that monitors license activation completion and seamlessly resumes the image building process. The result: fully automated creation of compliant Office LTSC golden images that integrate with </span><a href="https://aws.amazon.com/license-manager/"><span>AWS License Manager</span></a><span>, reduce instance launch times, and ensure you always deploy with the latest security patches.</span><span>&nbsp;</span></p> 
<h3><b><span>Solution overview</span></b><span>&nbsp;</span></h3> 
<p><span>The CloudFormation solution implements an Office LTSC golden image pipeline that addresses the reboot challenge. When you launch Office instances from the Marketplace, </span><a href="https://aws.amazon.com/license-manager/" rel="noopener" target="_blank"><span>AWS License Manager</span></a><span> executes </span><a href="https://aws.amazon.com/systems-manager/" rel="noopener" target="_blank"><span>AWS Systems Manager</span></a><span> Run Command to join the computer to your </span><a href="https://aws.amazon.com/directoryservice/" rel="noopener" target="_blank"><span>AWS Directory Service for Microsoft Active Directory</span></a><span> (AWS Managed Microsoft AD) and activate Office licenses. These commands require system reboots that occur outside the expected Image Builder workflow, causing pipeline failures. </span><span>&nbsp;</span></p> 
<p><span>The solution introduces a wait mechanism that monitors license activation completion, then resumes the image building process. </span><b><span>Figure 1</span></b><span> shows the architecture.</span></p> 
<div class="wp-caption aligncenter" id="attachment_11113" style="width: 2725px;">
 <img alt="Office LTSC Golden Image Creation Workflow - End-to-End Process" class="size-full wp-image-11113" height="1659" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/office-ltsc-aws-architecture_Large.png" width="2715" />
 <p class="wp-caption-text" id="caption-attachment-11113">Figure 1 – Office LTSC golden image pipeline architecture</p>
</div> 
<p><span>The workflow includes the following steps:</span><span>&nbsp;</span></p> 
<ol> 
 <li><b><span>Launch the latest </span></b><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html" rel="noopener" target="_blank"><span>Amazon Machine Image (AMI)</span></a><span> – The pipeline launches an EC2 instance with the latest Office LTSC AMI for the selected version. The solution stores the latest AMI in </span><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html" rel="noopener" target="_blank"><span>Parameter Store</span></a><span>, a capability of </span><a href="https://aws.amazon.com/systems-manager/" rel="noopener" target="_blank"><span>AWS Systems Manager</span></a><span>, and updates it weekly. A weekly schedule triggers the pipeline with the option to skip pipeline execution unless there are dependency updates, such as a change to the base image or to a component used in the recipe.</span><span>&nbsp;</span></li> 
 <li><b><span>Initiate wait mechanism</span></b><span> – Image Builder sends a </span><a href="https://docs.aws.amazon.com/imagebuilder/latest/userguide/wfdoc-step-actions.html#wfdoc-step-action-waitfor" rel="noopener" target="_blank"><span>WaitForAction</span></a><span> signal to an AWS </span><a href="https://aws.amazon.com/lambda/" rel="noopener" target="_blank"><span>Lambda</span></a><span> function that discovers the build instance.</span><span>&nbsp;</span></li> 
 <li><b><span>Discover build instance</span></b><span> – The Lambda function discovers the instance that launches as part of the Build phase in EC2 Image Builder using EC2 tags.</span><span>&nbsp;</span></li> 
 <li><b><span>Start automation workflow</span></b><span> – Systems Manager starts an automation that performs the following actions:</span><span>&nbsp;</span> 
  <ol> 
   <li><span>Waits for the instance to complete the domain join process by polling the License Manager User Subscriptions API&nbsp;</span><span>&nbsp;</span></li> 
   <li><span>Resumes the workflow when the status of the instance is Active under License Manager User Subscriptions</span><span>&nbsp;</span></li> 
  </ol> </li> 
 <li><b><span>Apply components</span></b><span> – The pipeline applies managed components. In this post, I select managed components like AWS-CLI, CloudWatchAgent, and Windows-Update. You can use the set of components that you need. There are managed components by Amazon, third-party components, and components from AWS Marketplace. You can also </span><a href="https://docs.aws.amazon.com/imagebuilder/latest/userguide/create-custom-components.html" rel="noopener" target="_blank"><span>develop custom components for your Image Builder image</span></a><span> to fit your needs.</span><span>&nbsp;</span></li> 
 <li><b><span>Create golden AMI </span></b><span>– Image Builder creates the final golden AMI with Office LTSC and all applied components.</span><span>&nbsp;</span></li> 
</ol> 
<p><span>This solution uses Parameter Store to securely store the Office LTSC AMI IDs. When AWS releases new Office LTSC AMIs with security patches, the AMI Discovery Lambda automatically updates the parameters. The EC2 Image Builder pipeline references these parameters dynamically, so the next pipeline execution automatically uses the latest AMI without requiring any manual updates or stack redeployment.</span><span>&nbsp;</span></p> 
<p><span>This solution assigns the minimal permissions required for each resource using </span><a href="https://aws.amazon.com/iam/" rel="noopener" target="_blank"><span>AWS Identity and Access Management (IAM)</span></a><span> roles.&nbsp;</span><span>&nbsp;</span></p> 
<h3><b><span>Prerequisites</span></b><span>&nbsp;</span></h3> 
<p><span>To implement this solution, you need:</span><span>&nbsp;</span></p> 
<ul> 
 <li><span>An AWS account that you have permissions to deploy this into.</span></li> 
 <li><a href="https://docs.aws.amazon.com/license-manager/latest/userguide/user-based-subscriptions.html" rel="noopener" target="_blank"><span>AWS License Manager User Subscriptions configured for Office LTSC</span></a><span>. This would allow you to use <a href="https://aws.amazon.com/marketplace/pp/prodview-bh46d5p2hapns" rel="noopener" target="_blank">Office LTSC Professional Plus</a> or <a href="https://aws.amazon.com/marketplace/pp/prodview-4riznyn4eqlbw" rel="noopener" target="_blank">Office LTSC Standard</a> from <a href="https://aws.amazon.com/marketplace" rel="noopener" target="_blank">AWS Marketplace</a></span></li> 
 <li><a href="https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_microsoft_ad.html" rel="noopener" target="_blank"><span>AWS Managed Microsoft AD</span></a><span>. License Manager joins build instances to this directory to activate Office licenses</span></li> 
 <li><span>An </span><a href="https://aws.amazon.com/vpc/" rel="noopener" target="_blank"><span>Amazon Virtual Private Cloud</span></a><span> (Amazon VPC) with subnets for Image Builder instances. The VPC and subnet should be accessible to the AWS Managed Microsoft AD to join the instance to the domain.</span></li> 
 <li><span>Download the <a href="https://github.com/aws-samples/sample-ec2-imagebuilder-office-ltsc-golden-image-pipeline/blob/a42ea54881a24edbba383df174c16400213703cb/Templates/CloudFormation/OfficeLTSC-AMI-Automation.yml" rel="noopener" target="_blank">CloudFormation template</a>.</span><span>&nbsp;</span></li> 
</ul> 
<h3><b><span>Solution walkthrough</span></b><span>&nbsp;</span></h3> 
<p><span>Navigate to the </span><a href="https://console.aws.amazon.com/cloudformation/" rel="noopener" target="_blank"><span>AWS CloudFormation console</span></a><span> and ensure you have the correct AWS Region selected. First, you will need to upload the template.</span><span>&nbsp;</span></p> 
<ol> 
 <li><span>Choose </span><b><span>Create stack.</span></b><span>&nbsp;</span></li> 
 <li><span>In the </span><b><span>Prerequisite – Prepare template </span></b><span>section, select “Choose an existing template”</span><span>&nbsp;</span></li> 
 <li><span>In the </span><b><span>Specify template</span></b><span> section, select “Upload a template file.”</span><span>&nbsp;</span></li> 
 <li><span>Press </span><b><span>Choose file</span></b><span> and select the file “OfficeLTSC-AMI-Automation.yml” from the repository from the “CloudFormation” directory in the repository.</span><span>&nbsp;</span></li> 
 <li><span>Choose </span><b><span>Next</span></b><span>, then supply the parameters needed to deploy the solution (Figure 2):</span><span>&nbsp;</span> 
  <ol> 
   <li><b><span>Stack name </span></b><span>– This will be name of the AWS CloudFormation stack used in the solution. If you want to deploy this more than once in an AWS account, you will need to choose a different stack name for each.</span><span>&nbsp;</span></li> 
   <li><b><span>InstanceType </span></b><span>– This parameter specifies the EC2 instance type used for the temporary build instances that EC2 Image Builder launches during the image creation process. These build instances exist only for the duration of the pipeline execution and are automatically terminated after the AMI is created. This instance type is independent of the instance type you choose when deploying the golden AMI for actual use—you can select any compatible instance type at deployment time. The instance type selected here affects only build performance and cost.</span><span>&nbsp;</span></li> 
   <li><b><span>OfficeLTSCVersion</span></b><span> –</span> <span>This parameter determines which Office LTSC version and Windows Server combination will be used as the base AMI for your golden image. The solution automatically discovers and uses the latest AMI matching your selection from the AWS Marketplace.</span><span>&nbsp;</span></li> 
   <li><b><span>VpcId – </span></b><span>This is the VPC where EC2 Image Builder will launch temporary build instances. The VPC must have connectivity to the License Manager User Subscriptions Managed Active Directory for license validation to succeed.</span><span>&nbsp;</span></li> 
   <li><b><span>SubnetIds – </span></b><span>These are the subnets where EC2 Image Builder will launch temporary build instances during the image creation process. The subnets must have connectivity to the License Manager User Subscriptions Managed Active Directory for the license validation workflow to complete successfully.</span><span>&nbsp;</span></li> 
  </ol> </li> 
</ol> 
<div class="wp-caption aligncenter" id="attachment_11121" style="width: 1369px;">
 <img alt="CloudFormation Stack Configuration for Office LTSC AMI Automation" class="wp-image-11121 size-full" height="841" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/OFFICEBOOTSTRAP-F2-1.png" width="1359" />
 <p class="wp-caption-text" id="caption-attachment-11121">Figure 2 – CloudFormation stack parameter configuration</p>
</div> 
<p><span>When you have finished entering the parameters, choose </span><b><span>Next</span></b><span>. The </span><b><span>Configure stack options</span></b><span> will be displayed. Here, you can add tags and configure other settings for the stack. Then select the checkbox next to </span><b><span>I acknowledge that AWS CloudFormation might create IAM resources</span></b><span>. For this example, choose </span><b><span>Next</span></b><span>.</span><span>&nbsp;</span></p> 
<p><span>On the </span><b><span>Review</span></b><span> page, confirm that the parameters reflect what you are expecting to deploy. Then choose </span><b><span>Submit.</span></b><span>&nbsp;</span></p> 
<p><span>The stack will take several minutes to deploy. Once complete, go to the stack </span><b><span>Outputs </span></b><span>tab as shown in </span><b><span>Figure 3</span></b><span> and note the </span><b><span>ImagePipelineArn</span></b><span> Export name. This name will be referenced in the EC2 Image Builder console.</span><span>&nbsp;</span></p> 
<div class="wp-caption aligncenter" id="attachment_11122" style="width: 1741px;">
 <img alt="CloudFormation Stack Outputs - Image Pipeline ARN and Parameters" class="wp-image-11122 size-full" height="407" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/OFFICEBOOTSTRAP-F3-1.png" width="1731" />
 <p class="wp-caption-text" id="caption-attachment-11122">Figure 3 – CloudFormation stack outputs</p>
</div> 
<h3><b><span>Testing</span></b><span>&nbsp;</span></h3> 
<p><span>In the </span><a href="https://console.aws.amazon.com/imagebuilder/home" rel="noopener" target="_blank"><span>EC2 Image Builder console</span></a><span> select the Pipeline name noted from the CloudFormation Outputs to see its configuration details, scheduled run time, and Image recipe. You can wait for the scheduled time or you can invoke the pipeline by choosing the Actions drop down and selecting Run pipeline, as shown in </span><b><span>Figure 4</span></b><span>&nbsp;</span></p> 
<p><b><span>If you require additional customizations</span></b><span>, you can add your own </span><b><span>custom components</span></b><span> to the Image Builder pipeline before invoking it. For example:</span><span>&nbsp;</span></p> 
<ul> 
 <li><span>Use </span><b><span>AWS Marketplace</span></b><span> components for pre-built solutions.</span><span>&nbsp;</span></li> 
 <li><span>Develop </span><b><span>custom components</span></b><span> (scripts, tools, or configurations) tailored to your environment.</span><span>&nbsp;</span></li> 
 <li><span>Modify the </span><b><span>Image recipe</span></b><span> to include these components during the build process.</span><span>&nbsp;</span></li> 
</ul> 
<p><span>This ensures your golden AMI includes all necessary software, security settings, and configurations for your specific use case.</span><span>&nbsp;</span></p> 
<div class="wp-caption aligncenter" id="attachment_11123" style="width: 1691px;">
 <img alt="EC2 Image Builder Pipeline Summary and Manual Run Option" class="wp-image-11123 size-full" height="964" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/OFFICEBOOTSTRAP-F4-1.png" width="1681" />
 <p class="wp-caption-text" id="caption-attachment-11123">Figure 4 – Manual pipeline invocation</p>
</div> 
<p><span>&nbsp;</span><span>When invoked, the output image progresses through multiple phases to generate the new AMI. </span><strong>Figure 5 </strong><span>shows the pipeline execution status.</span><span>&nbsp;</span></p> 
<div class="wp-caption aligncenter" id="attachment_11124" style="width: 1639px;">
 <img alt="Image Builder Pipeline Output Status and Build Progress" class="wp-image-11124 size-full" height="747" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/OFFICEBOOTSTRAP-F5-1.png" width="1629" />
 <p class="wp-caption-text" id="caption-attachment-11124">Figure 5 – Pipeline execution progress</p>
</div> 
<p><span>Selecting the output image version will open a new tab with additional details. As shown in </span><b><span>Figure 6,</span></b><span> the Workflow tab shows each step of the image building process. Selecting a Step ID will provide additional information about each step and its application log.</span><span>&nbsp;</span></p> 
<div class="wp-caption aligncenter" id="attachment_11125" style="width: 1640px;">
 <img alt="AMI Image Build Workflow Execution with Step-by-Step Status" class="wp-image-11125 size-full" height="1094" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/OFFICEBOOTSTRAP-F6-1.png" width="1630" />
 <p class="wp-caption-text" id="caption-attachment-11125">Figure 6 – Workflow execution details</p>
</div> 
<p><span>Once the EC2 Image Builder pipeline successfully completes all workflow steps and the Image status changes to Available, the golden AMI is ready for deployment as shown in </span><b><span>Figure 7</span></b><span>. This AMI contains the configurations from your image recipe. It includes Office LTSC, AWS CLI, CloudWatch Agent, and Windows Updates. When you launch an instance from this AMI, License Manager activates it with no manual license configuration required. To connect to the instance you can go through the steps described </span><a href="https://docs.aws.amazon.com/license-manager/latest/userguide/user-based-subscriptions-connect.html"><span>here</span></a><span>.</span><span>&nbsp;</span></p> 
<div class="wp-caption aligncenter" id="attachment_11119" style="width: 478px;">
 <img alt="EC2 Image Builder showing a completed Office LTSC golden AMI with Available status and the EC2 console Launch Instance page using the golden AMI for deployment." class="size-full wp-image-11119" height="260" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/22/OFFICEBOOTSTRAP-F7.png" width="468" />
 <p class="wp-caption-text" id="caption-attachment-11119">Figure7 – Office LTSC golden AMI available for EC2 instance deployment</p>
</div> 
<h3><b><span>Cleanup</span></b><span>&nbsp;</span></h3> 
<p><b><span>To clean up resources</span></b><span>&nbsp;</span></p> 
<ol> 
 <li><span>Open the AWS CloudFormation console.</span><span>&nbsp;</span></li> 
 <li><span>Select the stack you created.</span><span>&nbsp;</span></li> 
 <li><span>Choose </span><b><span>Delete stack</span></b><span>.</span><span>&nbsp;</span></li> 
 <li><span>Confirm the deletion.</span><span>&nbsp;</span></li> 
</ol> 
<p><span>Alternatively, you can use the AWS Command Line Interface (AWS CLI):</span><span>&nbsp;</span></p> 
<pre><code class="lang-bash">aws cloudformation delete-stack --stack-name office-ltsc-image-builder</code></pre> 
<p><span>This removes the IAM roles, Lambda functions, Amazon Simple Notification Service (Amazon SNS) topics, Systems Manager Automation Documents, and Image Builder resources. You need to manually delete any created AMIs and snapshots if you no longer need them.</span><span>&nbsp;</span></p> 
<h3><b><span>Conclusion</span></b><span>&nbsp;</span></h3> 
<p>EC2 Image Builder automates<span> how you deploy and manage Microsoft Office LTSC across AWS. This method ensures all installations are identical and includes built-in tools to track your licenses, replacing manual or inconsistent deployment steps.</span><span>&nbsp;</span></p> 
<p><span>While installing Microsoft Office on individual instances works for small teams, using CloudFormation to create golden images scales to support large organizations. The system automatically validates licenses for compliance and applies security patches on a set schedule. Use the CloudFormation template to start building automated, compliant Office images for your infrastructure.</span><span>&nbsp;</span></p>
