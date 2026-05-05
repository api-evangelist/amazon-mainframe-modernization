---
title: "Introducing the AWS Well-Architected Microsoft Workloads Lens"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/introducing-the-aws-well-architected-microsoft-workloads-lens/"
date: "Mon, 02 Feb 2026 21:18:38 +0000"
author: "Luciano Bernardes"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p>In this blog post, we will introduce the <a href="https://docs.aws.amazon.com/wellarchitected/latest/microsoft-workloads-lens/microsoft-workloads-lens.html" rel="noopener" target="_blank">AWS Well-Architected Microsoft Workloads Lens</a>, a comprehensive framework designed to help you build and operate Microsoft-based systems on Amazon Web Services (AWS) with confidence.</p> 
<p>Organizations continue to run Microsoft workloads on AWS to leverage the scalability, reliability, and innovation of the cloud while maintaining their investments in Microsoft technologies. Whether you’re running Windows Server, SQL Server, Active Directory (AD), .NET applications, or a combination of these, architecting these workloads correctly is crucial for success.</p> 
<h2>Why a Microsoft-specific lens?</h2> 
<p>The&nbsp;<a href="https://aws.amazon.com/architecture/well-architected/" rel="noopener" target="_blank">AWS Well-Architected Framework</a> has long been the gold standard for evaluating cloud architectures across six pillars: Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, and Sustainability. However, Microsoft workloads come with unique considerations—from licensing complexities and Active Directory integration to SQL Server high availability and .NET application modernization.</p> 
<p>The Microsoft Workloads Lens extends the core Well-Architected Framework by incorporating best practices gleaned from thousands of customer implementations. AWS Solutions Architects have extensive experience architecting Microsoft solutions across diverse industries, and this lens captures that collective knowledge in a systematic, actionable format.</p> 
<h2>What’s inside the lens?</h2> 
<p>The Microsoft Workloads Lens provides a structured approach to evaluating your Microsoft architectures through real-world scenarios and pillar-specific best practices.</p> 
<h3>Six real-world scenarios</h3> 
<p>The lens addresses the most&nbsp;common challenges&nbsp;organizations face when running Microsoft workloads on AWS:</p> 
<ol> 
 <li><strong>Microsoft SQL Server migration:</strong> Whether you’re looking to lift-and-shift to <a href="https://aws.amazon.com/ec2/" rel="noopener" target="_blank">Amazon Elastic Compute Cloud</a> (Amazon EC2), re-platform to <a href="https://aws.amazon.com/rds/" rel="noopener" target="_blank">Amazon Relational Database Service</a> (Amazon RDS) for SQL Server, or modernize to <a href="https://aws.amazon.com/rds/aurora/" rel="noopener" target="_blank">Amazon Aurora</a>, the lens provides clear guidance on migration approaches, licensing considerations, and optimization strategies.</li> 
 <li><strong>.NET application modernization:</strong> Learn how to evolve from .NET Framework monoliths to cloud-native cross-platform .NET microservices, leveraging containers, serverless architectures, and modern development practices.</li> 
 <li><strong>Active Directory integration:</strong> Establish seamless identity management across hybrid environments using <a href="https://aws.amazon.com/directoryservice/" rel="noopener" target="_blank">AWS Directory Service</a> for Microsoft Active Directory (<a href="https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_microsoft_ad.html" rel="noopener" target="_blank">AWS Managed Microsoft AD</a>), <a href="https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_ad_connector.html" rel="noopener" target="_blank">AD Connector</a>, or self-managed Active Directory on Amazon EC2, with comprehensive guidance on trust relationships and single sign-on (SSO) implementation.</li> 
 <li><strong>Windows Server &amp; File Services migration:</strong> Modernize file services using <a href="https://aws.amazon.com/fsx/windows/" rel="noopener" target="_blank">Amazon FSx for Windows File Server</a> or <a href="https://aws.amazon.com/fsx/netapp-ontap/" rel="noopener" target="_blank">Amazon FSx for NetApp ONTAP</a> while maintaining NTFS permissions and Active Directory integration.</li> 
 <li><strong>Windows-Based infrastructure modernization:</strong> Navigate end-of-support scenarios and hardware refresh cycles with strategic migration approaches using <a href="https://aws.amazon.com/application-migration-service/" rel="noopener" target="_blank">AWS Application Migration Service</a> (AWS MGN) and comprehensive automation.</li> 
 <li><strong>High&nbsp;Availability&nbsp;&amp;&nbsp;Disaster Recovery:</strong> Design resilient architectures with Multi-AZ deployments, cross-region replication, and automated failover procedures that meet your Recovery Time Objective (RTO) and Recovery Point Objective (RPO) requirements.</li> 
</ol> 
<h3>Pillar-specific best practices</h3> 
<p>Each of the six Well-Architected pillars includes Microsoft-specific guidance:</p> 
<p><strong>Cost Optimization</strong> helps you maximize value through right-sizing, flexible licensing models (including bring your own license, or BYOL), managed services adoption, and strategic modernization. The lens covers everything from Windows EC2 optimization to SQL Server edition selection and containerization strategies.</p> 
<p><strong>Operational Excellence</strong> focuses on comprehensive observability using Microsoft Performance Counters, Windows Event Logs, and Application Performance Monitoring (APM) for .NET applications, combined with operational automation through Infrastructure as Code (IaC) and <a href="https://aws.amazon.com/systems-manager/" rel="noopener" target="_blank">AWS Systems Manager</a>.</p> 
<p><strong>Performance Efficiency</strong> guides you in selecting appropriate cloud resources, optimizing compute and storage solutions, and establishing performance measurement practices that ensure your Microsoft workloads run efficiently.</p> 
<p><strong>Reliability</strong> emphasizes designing for failure, implementing robust monitoring and incident response, and automating recovery procedures to maintain high availability for mission-critical Microsoft applications.</p> 
<p><strong>Security</strong> extends core security principles with Microsoft-specific configurations, identity integration patterns, and data protection strategies that leverage both Microsoft and AWS security capabilities.</p> 
<p><strong>Sustainability</strong> addresses efficient resource&nbsp;utilization&nbsp;and sustainability-driven architecture design, helping you minimize environmental impact while&nbsp;maintaining&nbsp;business effectiveness.</p> 
<h2>Key design principles</h2> 
<p>The lens&nbsp;establishes&nbsp;foundational design principles specifically for Microsoft workloads:</p> 
<ul> 
 <li><strong>Assess holistically:</strong> Use tools like <a href="https://aws.amazon.com/optimization-and-licensing-assessment/" rel="noopener" target="_blank">AWS Optimization and Licensing Assessment</a> (AWS OLA) and <a href="https://aws.amazon.com/migration-evaluator/" rel="noopener" target="_blank">Migration Evaluator</a> (ME) to understand your current state before designing or migrating.</li> 
 <li><strong>Define clear goals:</strong> Whether reducing licensing costs, improving availability, or enhancing security, let your objectives shape architectural decisions.</li> 
 <li><strong>Leverage AWS native services:</strong> Reduce operational burden with managed offerings like Amazon RDS for SQL Server, Amazon FSx for Windows File Server, and AWS Managed Microsoft AD.</li> 
 <li><strong>Optimize licensing and costs:</strong> Right-size resources, automate scheduling, and evaluate BYOL strategies to maximize value.</li> 
 <li><strong>Design for resilience:</strong> Implement Multi-AZ deployments, SQL Server Always On, and regular disaster recovery testing.</li> 
 <li><strong>Embrace modernization incrementally:</strong> Where feasible, adopt containers and serverless solutions to unlock agility and cost optimization.</li> 
</ul> 
<h2>How to use the lens</h2> 
<p>We designed the Microsoft Workloads Lens for architects, developers, and operations teams working with Microsoft technologies on AWS. You can:</p> 
<ol> 
 <li><strong>Conduct systematic reviews:</strong> Use the lens questions to evaluate your current Microsoft architecture and identify improvement areas.</li> 
 <li><strong>Access through the <a href="https://aws.amazon.com/well-architected-tool/" rel="noopener" target="_blank">AWS Well-Architected Tool</a>:</strong> Perform structured reviews and track remediation progress over time.</li> 
 <li><strong>Leverage hands-on labs:</strong> Explore practical implementations through <a href="https://www.wellarchitectedlabs.com/" rel="noopener" target="_blank">AWS Well-Architected Labs</a>.</li> 
 <li><strong>Engage with AWS Partners:</strong> Work with <a href="https://aws.amazon.com/partners/" rel="noopener" target="_blank">AWS Partner Network</a> (APN) Partners who specialize in Microsoft workloads for expert guidance.</li> 
</ol> 
<h2>Incorporating agentic AI</h2> 
<p>AWS Solutions Architects have extensive experience in layering in agentic AI tooling and capabilities based on your workflows. Architecting Microsoft solutions using modern tooling and this lens is a powerful combination. For example, using <a href="https://aws.amazon.com/transform/windows/" rel="noopener" target="_blank">AWS Transform for full-stack Windows modernization</a> yields amazing results:</p> 
<ul> 
 <li><strong><a href="https://aws.amazon.com/solutions/case-studies/thomson-reuters-case-study/" rel="noopener" target="_blank">Thomson Reuters</a>:</strong> used AWS agentic AI tooling to drive a 4X velocity boost in their modernization efforts — and continue to modernize 1.5 million lines of code every month — cutting application transformation time from months to just a two-week sprint.</li> 
 <li><strong><a href="https://aws.amazon.com/solutions/case-studies/experian-agenticai/" rel="noopener" target="_blank">Experian</a>:</strong> saved 300 engineering days in one year with AWS AI tooling – enhancing engineering productivity.</li> 
 <li><strong><a href="https://aws.amazon.com/solutions/case-studies/csl-agenticai/" rel="noopener" target="_blank">CSL</a>:</strong> drove 17 data center exits in 30 months — and accelerated Microsoft application discovery time by 12x for each application wave.</li> 
</ul> 
<h2>Real-world impact</h2> 
<p>Organizations using the Microsoft Workloads Lens best practices have achieved significant benefits:</p> 
<ul> 
 <li><strong>Reduced licensing costs</strong> through strategic BYOL implementation and edition optimization.</li> 
 <li><strong>Improved availability</strong> with Multi-AZ deployments and automated failover.</li> 
 <li><strong>Enhanced security posture</strong> through integrated Microsoft and AWS security controls.</li> 
 <li><strong>Faster modernization</strong> with clear migration paths and proven patterns.</li> 
 <li><strong>Operational efficiency</strong> through automation and managed services adoption.</li> 
</ul> 
<h2>Getting started</h2> 
<p>The Microsoft Workloads Lens complements rather than replaces the core Well-Architected Framework. We recommend:</p> 
<ol> 
 <li>Start with a general Well-Architected Framework Review to establish baseline cloud architecture principles.</li> 
 <li>Apply the Microsoft Workloads Lens to address Microsoft-specific considerations.</li> 
 <li>Prioritize improvements based on business impact and technical feasibility.</li> 
 <li>Implement changes incrementally and measure the results.</li> 
 <li>Conduct regular reviews as your workloads and AWS services evolve.</li> 
</ol> 
<h2>Conclusion</h2> 
<p>Running Microsoft workloads on AWS offers opportunities for innovation, cost optimization, and operational excellence. The AWS Well-Architected Microsoft Workloads Lens provides the structured guidance you need to make informed decisions and continuously improve your Microsoft-based architecture.</p> 
<p>Whether you’re just beginning your cloud journey or optimizing existing workloads, this lens serves as your roadmap to success. It combines AWS’s deep cloud expertise with Microsoft-specific best practices, ensuring you can confidently architect, migrate, and operate Windows Server, SQL Server, Active Directory, .NET applications, and other Microsoft technologies on AWS.</p> 
<p>Ready to get started? Access the Microsoft Workloads Lens through the <a href="https://docs.aws.amazon.com/wellarchitected/latest/microsoft-workloads-lens/microsoft-workloads-lens.html" rel="noopener" target="_blank">AWS Prescriptive Guidance</a> and the <a href="https://github.com/aws-samples/sample-well-architected-custom-lens/blob/main/microsoft-workloads-lens/microsoft-workloads-lens.json" rel="noopener" target="_blank">lens file</a> or reach out to your AWS account team to learn more about conducting a Well-Architected Review for your Microsoft workloads.</p> 
<h2>Learn more</h2> 
<ul> 
 <li><a href="https://aws.amazon.com/microsoft/" rel="noopener" target="_blank">AWS for Microsoft workloads</a></li> 
 <li><a href="https://aws.amazon.com/blogs/modernizing-with-aws/" rel="noopener" target="_blank">Microsoft workloads on AWS blog channel</a></li> 
 <li><a href="https://docs.aws.amazon.com/prescriptive-guidance/latest/optimize-costs-microsoft-workloads/introduction.html" rel="noopener" target="_blank">Optimize costs for Microsoft workloads on AWS</a></li> 
 <li><a href="https://aws.amazon.com/developer/language/net/modernize/" rel="noopener" target="_blank">Modernize .NET Workloads on AWS</a></li> 
</ul>
