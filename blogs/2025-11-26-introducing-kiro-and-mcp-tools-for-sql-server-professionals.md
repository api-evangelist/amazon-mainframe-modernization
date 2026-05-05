---
title: "Introducing Kiro and MCP Tools for SQL Server professionals"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/introducing-kiro-and-mcp-tools-for-sql-server-professionals/"
date: "Wed, 26 Nov 2025 16:48:20 +0000"
author: "Vikas Babu Gali"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p>SQL Server professionals manage critical database systems that power enterprise applications, from financial platforms to ecommerce systems. Their responsibilities include optimizing query performance, managing schemas, ensuring high availability, and troubleshooting complex issues. With the advancement of <a href="https://aws.amazon.com/ai/?refid=6da207ce-1440-41a4-b16f-3003bf7e4833">Artificial Intelligence (AI)</a>, businesses are redesigning their workflows, enabling AI to independently complete tasks and achieve business goals. Customers managing SQL Server workloads can now use <a href="https://kiro.dev/docs/cli/" rel="noopener noreferrer" target="_blank">Kiro</a> to automate routine tasks and streamline database operations using natural language commands. Unlike traditional processes, which often follow rigid, predefined workflows,<a href="https://aws.amazon.com/ai/agentic-ai/?trk=6da207ce-1440-41a4-b16f-3003bf7e4833&amp;sc_channel=ps&amp;ef_id=Cj0KCQjwh5vFBhCyARIsAHBx2wxE5nyVS3A1yZ_nsTF6Io0YXNtkiXbC_zqJ5Dh-9HpAHo05NiR36wAaAlsGEALw_wcB:G:s&amp;s_kwcid=AL!4422!3!763097424394!e!!g!!agentic%20ai%20aws!22776923517!187468829492&amp;gad_campaignid=22776923517&amp;gbraid=0AAAAADjHtp9qpP7KPj1-5yNtUiBGknEkU&amp;gclid=Cj0KCQjwh5vFBhCyARIsAHBx2wxE5nyVS3A1yZ_nsTF6Io0YXNtkiXbC_zqJ5Dh-9HpAHo05NiR36wAaAlsGEALw_wcB" rel="noopener noreferrer" target="_blank"> agentic AI</a> introduces autonomous systems capable of reasoning, planning, and adapting to complex, real-world tasks. These intelligent agents are transforming industries by automating critical processes, and database administration is no exception.</p> 
<p>For customers, agentic AI offers a powerful opportunity to streamline routine tasks, enhance productivity, and focus on strategic initiatives. However, with this transformation comes the need to understand emerging protocols like the <a href="https://modelcontextprotocol.io/docs/getting-started/intro" rel="noopener noreferrer" target="_blank">Model Context Protocol</a> (MCP), which standardizes how AI agents connect to data sources and tools, enabling seamless integration and contextual intelligence. The industry is moving toward agentic systems that can handle multi-step workflows, interact with enterprise systems, and maintain contextual awareness across operations.&nbsp;This shift is about automation and about creating intelligent systems that can reason like humans, making decisions based on real-time data and organizational needs.</p> 
<p><a href="https://aws.amazon.com/" rel="noopener noreferrer" target="_blank">Amazon Web Services (AWS)</a> released&nbsp;<a href="https://awslabs.github.io/mcp/" rel="noopener noreferrer" target="_blank">AWS MCP Servers</a> as an open-source project. Customers can use AWS MCP Servers to build lightweight servers that expose data and tools to AI agents. This helps to customize agentic workflows to their specific needs while using the scalable infrastructure of AWS.</p> 
<p>In this blog post, we show you how SQL Server users can boost productivity using AWS AI tools, particularly <a href="https://kiro.dev/docs/cli/" rel="noopener noreferrer" target="_blank">Kiro CLI</a>&nbsp;with MCP integration. We also show how natural language commands and context-aware automation simplify database management tasks, making database maintenance and optimization more efficient for database professionals at all skill levels.</p> 
<h2>Kiro</h2> 
<p>Kiro is an AI-powered conversational assistant designed to assist developers and IT professionals, including DBAs, with tasks ranging from coding to troubleshooting. On platforms such as the command-line interface (CLI), Visual Studio Code, JetBrains IDEs, and mobile apps, it supports natural language queries to generate code, optimize resources, and interact with AWS services. For database professionals, Kiro can:</p> 
<ul> 
 <li>Write and optimize SQL queries based on natural language prompts</li> 
 <li>Analyze database schemas and generate documentation or diagrams</li> 
 <li>Suggest performance optimizations by accessing execution plans and metrics</li> 
 <li>Integrate with AWS services such as <a href="https://aws.amazon.com/cloudwatch/" rel="noopener noreferrer" target="_blank">Amazon CloudWatch</a>, <a href="https://aws.amazon.com/rds/sqlserver/" rel="noopener noreferrer" target="_blank">Amazon Relational Database Service (Amazon RDS) for SQL Server</a>, or <a href="https://aws.amazon.com/rds/aurora/" rel="noopener noreferrer" target="_blank">Amazon Aurora.</a></li> 
</ul> 
<h2>MCP</h2> 
<p>The <a href="https://www.anthropic.com/news/model-context-protocol" rel="noopener noreferrer" target="_blank">Model Context Protocol</a>&nbsp;(MCP) developed by <a href="https://www.anthropic.com/" rel="noopener noreferrer" target="_blank">Anthropic</a> is an open protocol that standardizes how applications provide context to large language models (LLMs). This enables Kiro to connect to external data sources and tools through lightweight MCP servers. The MCP servers provide context-aware responses by accessing databases, allowing for more precise automation and task execution. It enables AI assistants, like Kiro, to interact with external tools and data sources such as SQL Server databases through MCP servers. These servers expose tools and resources that allow LLMs to perform tasks like querying database schemas, executing SQL commands, or generating reports, as shown in Figure 1. The Kiro CLI acts as an MCP client, facilitating communication between the LLM and the MCP server to automate database tasks.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-1-20.png"><img alt="MCP Overview" class="aligncenter size-full wp-image-10859" height="573" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-1-20.png" width="1563" /></a></p> 
<p>Figure 1: MCP Overview</p> 
<h2>MCP architecture and relevance to Database Administration</h2> 
<p>With MCP, you can integrate Kiro with database environments, enabling automated tasks through natural language interactions for database administration tasks. MCP standardizes how LLMs connect to external systems, reducing the need for custom integrations. It consists of:</p> 
<ul> 
 <li><strong>MCP Host</strong>: The AI application (for example, Kiro CLI) that interacts with the user.</li> 
 <li><strong>MCP Client</strong>: A component within the host that communicates with MCP servers (for example, the CLI).</li> 
 <li><strong>MCP Server</strong>: A lightweight program that exposes tools or resources, such as SQL Server schema access or query execution.</li> 
 <li><strong>MCP tools</strong>:&nbsp;MCP tools are designed to be model-controlled, meaning the language model can automatically discover and invoke them based on the context of a conversation.</li> 
 <li><strong>Relevance for SQL Server users</strong>: By combining Kiro with MCP tools, users can interact with SQL Server in natural language, automate repetitive tasks, and integrate with other systems without writing complex integration code.</li> 
</ul> 
<h2>Prerequisites</h2> 
<p>Before you begin, make sure you have the following:</p> 
<ul> 
 <li><a href="https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fportal.aws.amazon.com%2Fbilling%2Fsignup%2Fresume&amp;client_id=signup&amp;code_challenge_method=SHA-256&amp;code_challenge=2F-lLcsO1EeMA6nPuqXiwZHripIqYDsB28EQWkStQ8Q" rel="noopener noreferrer" target="_blank">Create an AWS Account</a> if you don’t already have one.</li> 
 <li>Have access to an AWS account through the <a href="https://aws.amazon.com/console/" rel="noopener noreferrer" target="_blank">AWS Management Console</a> and the <a href="https://aws.amazon.com/cli" rel="noopener noreferrer" target="_blank">AWS Command Line Interface (AWS CLI)</a>.</li> 
 <li>Development environment: Visual Studio through <a href="https://github.com/coder/code-server" rel="noopener noreferrer" target="_blank">code-server</a>, hosted on an <a href="https://aws.amazon.com/ec2" rel="noopener noreferrer" target="_blank">Amazon Elastic Compute Cloud (Amazon EC2)</a> Instance. You can optionally choose to use your preferred <a href="https://aws.amazon.com/ai/generative-ai/" rel="noopener noreferrer" target="_blank">generative AI</a> integrated development environment (IDE) such as <a href="https://cursor.com/en" rel="noopener noreferrer" target="_blank">Cursor</a>, <a href="https://kiro.dev/" rel="noopener noreferrer" target="_blank">Kiro</a>, or <a href="https://www.anthropic.com/claude-code" rel="noopener noreferrer" target="_blank">Claude Code</a>.</li> 
 <li>Install&nbsp;<a href="https://kiro.dev/docs/cli/" rel="noopener noreferrer" target="_blank">Kiro CLI</a>&nbsp;on your system.</li> 
 <li>Install the&nbsp;<strong>uv</strong>&nbsp;utility from&nbsp;<a href="https://github.com/astral-sh/uv#installation" rel="noopener noreferrer" target="_blank">Astral</a>&nbsp;or the&nbsp;<a href="https://docs.astral.sh/uv/getting-started/installation/" rel="noopener noreferrer" target="_blank">GitHub README</a>.</li> 
 <li>Use the&nbsp;<strong>uv</strong>&nbsp;utility to install Python version 3.10.</li> 
</ul> 
<p><code>uv python install 3.10</code></p> 
<ul> 
 <li>A SQL Server database instance, such as an <a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html" rel="noopener noreferrer" target="_blank">Amazon RDS for SQL Server instance</a> or <a href="https://docs.aws.amazon.com/sql-server-ec2/latest/userguide/sql-server-on-ec2-overview.html">SQL Server on Amazon EC2</a>, with SQL Server logs <a href="https://aws.amazon.com/blogs/database/monitor-your-microsoft-sql-server-using-custom-metrics-with-amazon-cloudwatch-and-aws-systems-manager/" rel="noopener noreferrer" target="_blank">configured to stream</a> to Amazon CloudWatch for monitoring and analysis.</li> 
 <li>This solution works with multiple AI models available through <a href="https://aws.amazon.com/bedrock/" rel="noopener noreferrer" target="_blank">Amazon Bedrock</a>. For this blog we are using <a href="https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html" rel="noopener noreferrer" target="_blank">Anthropic’s models</a> in Amazon Bedrock. For more information, see <a href="https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html" rel="noopener noreferrer" target="_blank">Add or remove access to Amazon Bedrock foundational models.</a></li> 
</ul> 
<p><strong>Basic knowledge:</strong></p> 
<ul> 
 <li>Familiarity with database administration tasks such as deadlocks, index rebuilds, statistics, and SQL agent jobs</li> 
 <li>Basic familiarity with MCP use-cases.&nbsp;For more information, see <a href="https://github.com/awslabs/mcp/tree/main/samples" rel="noopener noreferrer" target="_blank">MCP overview</a></li> 
</ul> 
<p><strong>IAM permissions:</strong></p> 
<ul> 
 <li>An&nbsp;<a href="https://aws.amazon.com/iam" rel="noopener noreferrer" target="_blank">AWS Identity and Access Management (IAM)</a> user or role that has permissions to make the necessary AWS service calls, such as by <a href="https://aws.amazon.com/cloudwatch" rel="noopener noreferrer" target="_blank">Amazon CloudWatch</a> in this example.</li> 
 <li>When providing permissions to an IAM user, follow the <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege" rel="noopener noreferrer" target="_blank">principle of least privilege</a>.</li> 
</ul> 
<h2>Configuring MCP Servers</h2> 
<h2>AWS MCP Server to Kiro CLI configuration</h2> 
<ol> 
 <li><a href="https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-files.html" rel="noopener noreferrer" target="_blank">Set up AWS profile</a> in your environment</li> 
</ol> 
<p style="text-align: left;"><code>aws configure --profile [Profile Name]<br /> AWS Access Key ID: [Your access key]<br /> AWS Secret Access Key: [Your secret key]<br /> Default region name: [your-region]<br /> Default output format: json</code> </p> 
<ol start="2"> 
 <li>Make sure the Kiro CLI version is 1.9.x or later to use MCP features. You can check the Kiro CLI version by executing&nbsp;<code>kiro-cli --version</code>&nbsp;in your terminal.</li> 
 <li>Configure MCP server Kiro CLI supports two levels of MCP configuration:</li> 
 <li><strong>Global configuration</strong>:&nbsp;<code>~/.kiro/settings/mcp.json</code>applies across all your projects.</li> 
 <li><strong>Local configuration</strong>:&nbsp;<code>.kiro/settings/mcp.json</code>is specific to the current project.</li> 
 <li>Choose your preferred configuration level and add the below CloudWatch MCP server configuration to the corresponding mcp.json file. Replace the&nbsp;AWS_PROFILE&nbsp;and&nbsp;AWS_REGION&nbsp;placeholders with your specific AWS profile and region.</li> 
</ol> 
<p>In the JSON configuration, you need to replace the&nbsp;<code>AWS_PROFILE</code>&nbsp;with an AWS profile having permissions to access AWS services.</p> 
<div class="hide-language"> 
 <pre><code class="lang-css">{
   "mcpServers": {
      "awslabs.cloudwatch-mcp-server": {
          "autoApprove": [],
          "disabled": false,
          "command": "uvx",
          "args": [
             "awslabs.cloudwatch-mcp-server@latest"
          ],
          "env": {
             "AWS_PROFILE": "[The AWS Profile Name you used in step1 to use for AWS access]",
             "AWS_REGION": "Add your AWS Region",
             "FASTMCP_LOG_LEVEL": "ERROR"
          },
          "transportType": "stdio"
      }
    }
}</code></pre> 
</div> 
<ol start="7"> 
 <li>Start the conversation with command&nbsp;kiro-cli For more information, see <a href="https://kiro.dev/docs/chat/" rel="noopener noreferrer" target="_blank">Using chat on the command line</a> .Note: You might encounter the error 0 of 1 mcp servers initialized. Servers still loading. The errors indicates that Kiro CLI could not properly initialize the MCP server at the time of loading related to path issues with uvx command. You can run which uvx command to find the full path and use it in the configuration until the path issue is resolved.</li> 
 <li>Use&nbsp;/tools&nbsp;command to view available tools and capabilities as shown in Figure 2.</li> 
</ol> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-3-12.png"><img alt="List of available tools" class="aligncenter size-full wp-image-10852" height="1794" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-3-12.png" width="1708" /></a></p> 
<p>Figure 2: List of available tools</p> 
<h2>Solution Overview</h2> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-4-14.png"><img alt="Solution Overview" class="aligncenter size-full wp-image-10853" height="655" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-4-14.png" width="1626" /></a></p> 
<p>Figure 3: Solution Overview</p> 
<p>As shown in Figure 3:</p> 
<ol> 
 <li>DBA (user) input to Kiro CLI.</li> 
 <li>The Kiro CLI (MCP client) sends the natural language prompt to the LLM processing and tool selection.</li> 
 <li>The LLM interprets the prompt and selects the appropriate tool from the&nbsp;<a href="https://awslabs.github.io/mcp/servers/cloudwatch-mcp-server/" rel="noopener noreferrer" target="_blank">AWS CloudWatch MCP Server</a> (for example, <code>execute_log_insights_query</code> or <code>get_logs_insight_query_results</code>).</li> 
 <li>Using appropriate tools will generate results and responses.</li> 
</ol> 
<p><strong>Real-world use case example interaction:</strong></p> 
<p>In this example, we will analyze the deadlock information that occurred on the EC2 instance named SQLServerDB.</p> 
<p>Prompt: <code>“Analyze the deadlock events of SQLServerDB EC2 instance and provide recommendations”</code></p> 
<p>Response as shown in Figure 4:</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-6-10.png"><img alt="" class="aligncenter wp-image-10869 size-large" height="311" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-6-10-1024x311.png" width="1024" /></a></p> 
<p>Figure 4: Ask Kiro CLI to identify the cause of the issue</p> 
<p>The MCP server connects to the Amazon CloudWatch APIs, executes and returns the results to the CLI, which the LLM formats into a human-readable response, such as in Figures 5, 6, and 7.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-7-12.png"><img alt="" class="aligncenter wp-image-10870 size-full" height="869" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-7-12.png" width="1775" /></a></p> 
<p>Figure 5: Output of the Kiro CLI showing the RCA and Remediation Steps</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-8-5.png"><img alt="" class="aligncenter size-full wp-image-10871" height="856" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-8-5.png" width="1770" /></a></p> 
<p>Figure 6: Output of the Kiro CLI showing the RCA and Remediation Steps-Continued</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-9-9.png"><img alt="" class="aligncenter size-full wp-image-10872" height="724" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2025/11/24/image-9-9.png" width="1723" /></a></p> 
<p>Figure 7: Output of the Kiro CLI showing the RCA and Remediation Steps – continued</p> 
<p>As shown in Figures 5, 6, and 7, Kiro provides detailed deadlock analysis, recommendations, and next steps through a single prompt interaction. This MCP integration with your databases increases your team productivity. We discussed how you can use AWS MCP for CloudWatch; you can extend the capabilities by building or using custom-built MCP for your own requirements. Here are some potential use cases with example prompts that you may use:</p> 
<ol> 
 <li><strong>Schema exploration</strong>: List all tables in my SQL Server database.</li> 
 <li><strong>Query optimization:</strong> Can you provide recommendations to optimize the stored procedure?</li> 
 <li><strong>Query generation</strong>: Write a query to find the top 10 customers by sales volume from the Orders table.</li> 
 <li><strong>One-time reporting queries</strong>: Write a query to analyze sales data from last month and project trends for the next six months.</li> 
 <li><strong>Security and compliance</strong>: What security concerns do you identify in this SQL Server instance?</li> 
 <li><strong>Maintenance tasks</strong>: Generate a script to rebuild indexes based on fragmentation levels and provide recommendations considering table size and activity patterns.</li> 
 <li><strong>Documentation</strong>: Create an ER diagram for my SQL Server database.</li> 
 <li><strong>Backup and recovery</strong>: Check for any failed backup attempts on my server.</li> 
 <li><strong>Routine analysis</strong>: Create a daily report analyzing SQL Server error logs and agent logs and provide recommendations for any errors or potential issues identified.</li> 
</ol> 
<p>Effective prompt usage for Kiro is critical to obtain optimal responses. This comprehensive approach to prompt design maximizes the utility of the responses from Kiro while minimizing potential misunderstandings or irrelevant outputs. Here are some great tips for prompt design:</p> 
<ol start="1"> 
 <li>Start with clear, specific instructions that precisely state your requirements.</li> 
 <li>Provide relevant context about your environment or use case to help generate more accurate solutions.</li> 
 <li>Include examples where applicable to illustrate the desired output format or approach.</li> 
 <li>Incorporate error handling/guardrails by specifying how to manage potential issues or limitations.</li> 
 <li>Define your preferred output formatting structure, whether it’s tables, bullet points, or code blocks.</li> 
</ol> 
<p>The following example shows how to structure prompts when working with Kiro:</p> 
<ol start="1"> 
 <li><strong>Clear instructions</strong>: Show top 10 CPU-intensive queries and format results in a table with columns: QueryID, CPU Time, Execution Count.</li> 
 <li><strong>Provide context</strong>: Given our production database with 500 GB size and 1,000 concurrent users, identify optimal index maintenance window</li> 
 <li><strong>Output formatting</strong>: Present findings in bullet points and include SQL scripts in code blocks.</li> 
 <li><strong>Error handling: </strong>If the query fails because of permissions, provide alternative approaches and required permissions.</li> 
</ol> 
<p>To learn more, see <a href="https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-engineering-guidelines.html" rel="noopener noreferrer" target="_blank">Prompt engineering concepts</a>.</p> 
<h2>Conclusion</h2> 
<p>In this post, we showed how Kiro, integrated with MCP server, offers your team powerful automation capabilities for essential tasks including schema exploration, query generation, and documentation management. This idea can be expanded to multiple database engines. We encourage you to begin your AI journey through pilot projects, which serve as effective demonstrations of generative AI’s potential value to your team. Use your pilot project to build generative AI capabilities, practicing <a href="https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/responsible-ai.html" rel="noopener noreferrer" target="_blank">Responsible AI</a> . For more information about Kiro, see <a href="https://kiro.dev/docs/" rel="noopener noreferrer" target="_blank">Agentic AI development from prototype to production</a> and <a href="https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/generative-ai-lens.html" rel="noopener noreferrer" target="_blank">Generative AI Lens – AWS Well-Architected Framework</a></p> 
<hr /> 
<p>AWS has significantly more services, and more features within those services, than any other cloud provider, making it faster, easier, and more cost effective to move your existing applications to the cloud and build nearly anything you can imagine. Give your Microsoft applications the infrastructure they need to drive the business outcomes you want. Visit our<a href="https://aws.amazon.com/blogs/dotnet/">&nbsp;.NET on AWS</a>&nbsp;and&nbsp;<a href="https://aws.amazon.com/blogs/database/">AWS Database</a>&nbsp;blogs for additional guidance and options for your Microsoft workloads.&nbsp;<a href="https://pages.awscloud.com/MAP-windows-contact-us.html">Contact us</a>&nbsp;to start your migration and modernization journey today.</p> 
<hr />
