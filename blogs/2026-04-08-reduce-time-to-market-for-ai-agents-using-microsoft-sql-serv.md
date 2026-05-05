---
title: "Reduce time-to-market for AI agents using Microsoft SQL Server 2025 and Amazon Bedrock AgentCore"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/reduce-time-to-market-for-ai-agents-using-sql-server-2025-and-amazon-bedrock-agentcore/"
date: "Wed, 08 Apr 2026 16:23:27 +0000"
author: "Baris Furtinalar"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p>Enterprises struggle to integrate AI capabilities into existing applications without costly code changes. Traditional approaches require modifying application layers, updating APIs, and extensive testing cycles—all of which extend time-to-market and increase project risk.</p> 
<p>This post demonstrates how you can reduce time-to-market by invoking AI agents on <a href="https://aws.amazon.com/bedrock/agentcore/" rel="noopener" target="_blank">Amazon Bedrock AgentCore</a> directly from Microsoft SQL Server 2025. This is made possible by <a href="https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-invoke-external-rest-endpoint-transact-sql?view=sql-server-ver17&amp;tabs=request-headers" rel="noopener" target="_blank">sp_invoke_external_rest_endpoint</a>, a new system stored procedure that enables native REST API calls from T-SQL. Using a deep research scenario running on an ERP (Enterprise Resource Planning) application with database triggers as an example, you’ll learn how to connect database workloads to AI agents at the database level, eliminating the need for application modifications. Deep research, in this context, means the agent autonomously investigates related data across these systems and synthesizes its findings into an actionable summary.</p> 
<p>In this walkthrough, you deploy an AI deep research agent to AgentCore Runtime using AWS CDK, create a sample CRM database with tables and data, configure SQL Server 2025 to invoke the agent through database triggers, and observe how inserting a new record automatically triggers the agent to research across multiple ERP data sources — orders, inventory, and shipments — and produce a comprehensive report.</p> 
<p>The solution leverages <a href="https://docs.aws.amazon.com/cdk/v2/guide/home.html" rel="noopener" target="_blank">AWS Cloud Development Kit (AWS CDK)</a> for infrastructure, Strands Agents SDK for the AI agent, <a href="https://docs.aws.amazon.com/nova/latest/nova2-userguide/what-is-nova-2.html" rel="noopener" target="_blank">Amazon Nova 2</a> Lite as the foundation model, and SQL Server 2025’s new <code>sp_invoke_external_rest_endpoint</code> stored procedure.</p> 
<p>The complete source code, including CDK infrastructure and SQL scripts, is available in the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore" rel="noopener" target="_blank">aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore</a> GitHub repository.</p> 
<h3>What’s new in SQL Server 2025</h3> 
<p>SQL Server 2025 introduces <code>sp_invoke_external_rest_endpoint</code>, a system stored procedure that enables direct REST API calls from T-SQL. This capability opens new integration patterns that were previously impossible without middleware or application code changes.</p> 
<p>Key capabilities of <code>sp_invoke_external_rest_endpoint</code> include:</p> 
<ul> 
 <li>Support for HTTP methods: GET, POST, PUT, PATCH, DELETE</li> 
 <li>JSON and XML response handling</li> 
 <li>Configurable timeouts and retry logic</li> 
 <li>Secure credential storage using DATABASE SCOPED CREDENTIAL</li> 
</ul> 
<p>To enable this feature, run the following query in SQL Server Management Studio (SSMS):</p> 
<pre><code class="lang-sql">EXECUTE sp_configure 'external rest endpoint enabled', 1;
RECONFIGURE WITH OVERRIDE;</code></pre> 
<h4>Use cases for sp_invoke_external_rest_endpoint</h4> 
<p>This new feature enables several enterprise integration patterns:</p> 
<ul> 
 <li><strong>AI agents integration:</strong> Trigger AI agents to analyze data and generate research reports when records are inserted or updated</li> 
 <li><strong>Real-time notifications:</strong> Send alerts to external systems (Slack, Teams, PagerDuty) based on database events</li> 
 <li><strong>Cross-system synchronization:</strong> Push data changes to external APIs for real-time sync with other enterprise systems</li> 
 <li><strong>Audit and compliance logging:</strong> Send audit events to external SIEM or logging platforms</li> 
 <li><strong>Workflow orchestration:</strong> Trigger AWS Step Functions or other workflow engines from database events</li> 
</ul> 
<h3>Amazon Bedrock AgentCore Runtime overview</h3> 
<p><a href="https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html" rel="noopener" target="_blank">Amazon Bedrock AgentCore Runtime</a> provides managed infrastructure for deploying and running AI agents. Key features relevant to this solution include:</p> 
<ul> 
 <li><strong>Managed infrastructure:</strong> No need to provision or manage compute resources</li> 
 <li><strong>Session management:</strong> Dedicated execution environment (microVM) execution for each agent session</li> 
 <li><strong>Asynchronous processing:</strong> Support for long-running tasks up to 8 hours</li> 
 <li><strong>Multiple authentication options:</strong> IAM, and OAuth2</li> 
</ul> 
<p>For enterprise workloads that require background processing—like researching data across multiple systems—AgentCore Runtime’s asynchronous model is ideal. The agent can work independently while the database trigger returns immediately.</p> 
<h3>Solution overview</h3> 
<div class="wp-caption alignleft" id="attachment_11155" style="width: 3072px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/04/08/solution-architecture-AI-agent-with-SQL-Server-2025v1.png" rel="noopener" target="_blank"><img alt="Solution architecture for ERP deep research AI agent with SQL Server 2025" class="wp-image-11155 size-full" height="1309" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/04/08/solution-architecture-AI-agent-with-SQL-Server-2025v1.png" width="3062" /></a>
 <p class="wp-caption-text" id="caption-attachment-11155">Figure 1. Solution architecture for ERP deep research AI agent with SQL Server 2025</p>
</div> 
<p>The architecture (Figure 1) consists of:</p> 
<ol> 
 <li><strong>SQL Server 2025</strong> running on Amazon EC2 with a CRM database</li> 
 <li><strong>Database trigger</strong> that fires when new customer communications are inserted</li> 
 <li><strong>A private REST API on Amazon API Gateway</strong> with API key authentication (no public internet exposure)</li> 
 <li><strong>AWS Lambda</strong> function that handles both task submission and results retrieval endpoints</li> 
 <li><strong>Amazon Bedrock AgentCore Runtime</strong> hosting the AI agent built with Strands Agents SDK</li> 
 <li><strong>Amazon S3 bucket</strong> for storing agent results with Job ID as filename</li> 
</ol> 
<p>The following table maps each architecture component to its location in the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore" rel="noopener" target="_blank">GitHub repository</a>:</p> 
<table border="1"> 
 <thead> 
  <tr> 
   <th>Component</th> 
   <th>Repository path</th> 
   <th>Deployed to</th> 
   <th>Description</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td>AI Deep Research Agent</td> 
   <td>agent/</td> 
   <td>Amazon Bedrock AgentCore Runtime</td> 
   <td>Strands Agents SDK multi-agent workflow and tools</td> 
  </tr> 
  <tr> 
   <td>CDK Infrastructure</td> 
   <td>cdk/</td> 
   <td>AWS CloudFormation</td> 
   <td>VPC, API Gateway, Lambda, S3, and AgentCore Runtime</td> 
  </tr> 
  <tr> 
   <td>SQL Scripts</td> 
   <td>sql/</td> 
   <td>SQL Server 2025 on Amazon EC2</td> 
   <td>Schema, credentials, stored procedures, and triggers</td> 
  </tr> 
 </tbody> 
</table> 
<h4>Use case: ERP deep research</h4> 
<p>In this scenario, a company uses a CRM database to store customer communications—support tickets, emails, and call transcripts. The <code>CustomerCommunications</code> table captures:</p> 
<table border="1"> 
 <thead> 
  <tr> 
   <th>Column</th> 
   <th>Description</th> 
  </tr> 
 </thead> 
 <tbody> 
  <tr> 
   <td>CommunicationId</td> 
   <td>Primary key (used as Job ID for the AI agent)</td> 
  </tr> 
  <tr> 
   <td>CustomerId</td> 
   <td>Reference to the customer</td> 
  </tr> 
  <tr> 
   <td>CustomerSegment</td> 
   <td>Enterprise, SMB, or Startup</td> 
  </tr> 
  <tr> 
   <td>CustomerRegion</td> 
   <td>West, Central, or East</td> 
  </tr> 
  <tr> 
   <td>CustomerTier</td> 
   <td>Gold, Silver, or Bronze</td> 
  </tr> 
  <tr> 
   <td>CommunicationText</td> 
   <td>The actual communication content</td> 
  </tr> 
  <tr> 
   <td>AdditionalNotes</td> 
   <td>Populated by the AI agent with research results</td> 
  </tr> 
 </tbody> 
</table> 
<p>The complete schema with sample data is available in <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/03-crm-schema.sql" rel="noopener" target="_blank">03-crm-schema.sql</a>.</p> 
<p>After you complete the steps in this walkthrough, when a support representative logs a new customer communication, the solution automatically:</p> 
<ul> 
 <li>Analyzes the communication to identify products, orders, and issues mentioned</li> 
 <li>Researches related ERP data (inventory levels, order status, delivery tracking)</li> 
 <li>Generates a comprehensive research report</li> 
 <li>Stores the report back in the <code>AdditionalNotes</code> column</li> 
</ul> 
<p>All of this happens without any changes to the CRM application — the integration is entirely at the database level through triggers.</p> 
<h4>Data flow</h4> 
<ol> 
 <li>A new customer communication record is inserted into the CRM database</li> 
 <li>The database trigger fires and calls <code>sp_invoke_external_rest_endpoint</code></li> 
 <li>The stored procedure sends a POST request to the private API Gateway with the record’s primary key as Job ID</li> 
 <li>API Gateway validates the API key and forwards the request to Lambda</li> 
 <li>Lambda invokes the AI agent on AgentCore Runtime</li> 
 <li>The stored procedure returns immediately (asynchronous pattern)</li> 
 <li>The AI agent analyzes the communication, researches related ERP data, and generates a deep research report</li> 
 <li>The agent stores results in S3 with the Job ID as filename</li> 
 <li>A SQL Server Agent job periodically queries the APICallLog table for jobs with “Submitted” status and polls the Results Retrieval API for each one</li> 
 <li>The API reads results from S3 and returns them to SQL Server</li> 
 <li>CRM users see deep research reports without any application changes</li> 
</ol> 
<h3>Security design</h3> 
<p>This solution uses a private API Gateway with API key authentication. All components reside within a VPC with no public internet exposure. (Figure 2)</p> 
<div class="wp-caption alignnone" id="attachment_11165" style="width: 2254px;">
 <a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/04/08/security-architecture-using-a-private-API-Gateway-and-Bedrock-AgentCore.png" rel="noopener" target="_blank"><img alt="Security architecture using a private API Gateway and Bedrock AgentCore within a VPC." class="wp-image-11165 size-full" height="2202" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/04/08/security-architecture-using-a-private-API-Gateway-and-Bedrock-AgentCore.png" width="2244" /></a>
 <p class="wp-caption-text" id="caption-attachment-11165">Figure 2. Security architecture using a private API Gateway and Bedrock AgentCore within a VPC.</p>
</div> 
<p>Key security controls:</p> 
<ul> 
 <li><strong>Private API Gateway:</strong> Accessible only via VPC endpoint</li> 
 <li><strong>API key authentication:</strong> Stored securely using DATABASE SCOPED CREDENTIAL</li> 
 <li><strong>VPC isolation:</strong> SQL Server and API Gateway in private subnets</li> 
 <li><strong>NAT Gateway:</strong> Controlled outbound access for the agent</li> 
</ul> 
<h4>Securing the API key with DATABASE SCOPED CREDENTIAL</h4> 
<p>This solution stores the API key using DATABASE SCOPED CREDENTIAL rather than hardcoding it in stored procedures. The credential name must match the URL being called—this is a security feature of <code>sp_invoke_external_rest_endpoint</code> that prevents credentials from being used with unintended endpoints. You will run this as part of the deployment steps in <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/02-create-credentials.sql" rel="noopener" target="_blank">02-create-credentials.sql</a>:</p> 
<pre><code class="lang-sql">-- Create master key (required for credential encryption) 
CREATE MASTER KEY ENCRYPTION BY PASSWORD = '&lt;strong-password&gt;'; 
 
-- Create credentials for each endpoint (name must match URL) 
CREATE DATABASE SCOPED CREDENTIAL [https://&lt;api-id&gt;.execute-api.&lt;region&gt;.amazonaws.com/prod/submit] 
WITH IDENTITY = 'HTTPEndpointHeaders', 
SECRET = '{"x-api-key":"&lt;YOUR_API_KEY&gt;"}'; 
 
CREATE DATABASE SCOPED CREDENTIAL [https://&lt;api-id&gt;.execute-api.&lt;region&gt;.amazonaws.com/prod/results] 
WITH IDENTITY = 'HTTPEndpointHeaders', 
SECRET = '{"x-api-key":"&lt;YOUR_API_KEY&gt;"}'; </code></pre> 
<p>The credential encrypts the API key at rest and restricts access to authorized database principals.</p> 
<h4>AI Deep Research Agent</h4> 
<p>The AI agent uses a multi-agent workflow built with the Strands Agents SDK and deployed to Amazon Bedrock AgentCore Runtime. The agent is powered by <a href="https://aws.amazon.com/blogs/aws/introducing-amazon-nova-2-lite-a-fast-cost-effective-reasoning-model/" rel="noopener" target="_blank">Amazon Nova 2 Lite</a>, a fast, cost-effective reasoning model optimized for agentic workflows. Nova 2 Lite demonstrates reliable function calling for task automation and strong performance in multi-step reasoning—ideal for orchestrating the research workflow across multiple data sources.</p> 
<p>Three specialized agents collaborate to produce comprehensive research reports:</p> 
<ol> 
 <li><strong>Order Researcher</strong> – Investigates order details, shipment tracking, and delivery status</li> 
 <li><strong>Inventory Researcher</strong> – Analyzes product availability across warehouses and pricing history</li> 
 <li><strong>Report Writer</strong> – Synthesizes findings into an actionable report with recommendations</li> 
</ol> 
<p>The following snippet shows the core structure of this multi-agent setup, including how tools are defined, agents are configured, and the entry point is registered with Amazon Bedrock AgentCore Runtime:</p> 
<pre><code class="lang-python">from strands import Agent, tool 
from strands.models.bedrock import BedrockModel 
from bedrock_agentcore.runtime import BedrockAgentCoreApp 
app = BedrockAgentCoreApp() 
model = BedrockModel(model_id="global.amazon.nova-2-lite-v1:0", temperature=0.3) 
# Define tools for ERP data access 
@tool 
def check_inventory(sku: str) -&gt; str: 
    """Check inventory levels across all warehouses.""" 
    # Query ERP inventory data... 
 
# Create specialized agents 
order_researcher = Agent(model=model, tools=[get_order_details, track_shipment]) 
inventory_researcher = Agent(model=model, tools=[check_inventory, lookup_product]) 
report_writer = Agent(model=model, tools=[]) 
 
@app.entrypoint 
async def agent_invocation(payload: dict): 
    # Run multi-agent workflow and store results in S3 
    ... </code></pre> 
<p>The complete agent implementation is available in <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/tree/main/cdk/agent" rel="noopener" target="_blank">cdk/agent/</a>.</p> 
<h4>Infrastructure</h4> 
<p>The AWS CDK stack deploys the agent to AgentCore Runtime along with the supporting infrastructure:</p> 
<pre><code class="lang-javascript">const agentRuntime = new AgentCoreRuntime(this, 'DeepResearchAgent', { 
  agentName: 'erp-deep-research-agent', 
  handler: 'main.agent_invocation', 
  runtime: Runtime.PYTHON_3_12, 
  code: Code.fromAsset('agent'), 
  environment: { RESULTS_BUCKET: resultsBucket.bucketName } 
}); </code></pre> 
<p>The CDK stack includes VPC with private subnets, VPC endpoint for API Gateway, private API Gateway with API key, Lambda handler, S3 bucket for results, and the AgentCore Runtime agent. See the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/tree/main/cdk" rel="noopener" target="_blank">cdk/</a> folder for the complete infrastructure code.</p> 
<h3>Prerequisites</h3> 
<p>To follow along with this walkthrough, you need:</p> 
<ul> 
 <li>An <a href="https://aws.amazon.com/getting-started/" rel="noopener" target="_blank">AWS account</a></li> 
 <li><a href="https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html" rel="noopener" target="_blank">AWS CLI</a> installed and configured</li> 
 <li><a href="https://docs.aws.amazon.com/cdk/v2/guide/getting-started.html" rel="noopener" target="_blank">AWS CDK</a> v2 installed</li> 
 <li><a href="https://www.python.org/downloads/" rel="noopener" target="_blank">Python 3.12</a> or later</li> 
 <li><a href="https://nodejs.org/" rel="noopener" target="_blank">Node.js 18</a> or later (required by AWS CDK)</li> 
 <li><a href="https://docs.astral.sh/uv/getting-started/installation/" rel="noopener" target="_blank">uv</a> installed (required for packaging the agent)</li> 
 <li>SQL Server 2025 installed on an Amazon EC2 instance with <code>sp_invoke_external_rest_endpoint</code> enabled</li> 
</ul> 
<h3>Implementation walkthrough</h3> 
<h4>Step 1: Deploy the AWS infrastructure</h4> 
<p>Clone the repository and deploy the CDK stack:</p> 
<pre><code class="lang-bash">git clone https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore.git 
cd sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/cdk 
npm install 
npm run package-agent 
npx cdk deploy </code></pre> 
<p>The <code>npm run package-agent</code> command bundles the Python agent dependencies for AgentCore Runtime. The <code>cdk deploy</code> command provisions the VPC, private API Gateway, Lambda function, S3 bucket, and AgentCore Runtime agent. Note the API Gateway ID and API key from the CDK outputs — you will need them for the SQL Server configuration.</p> 
<h4>Step 2: Configure SQL Server</h4> 
<p>Connect to your SQL Server 2025 instance and run the SQL scripts from the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/" rel="noopener" target="_blank">sql/</a> folder in order.</p> 
<p>Run <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/01-enable-rest-endpoint.sql" rel="noopener" target="_blank">01-enable-rest-endpoint.sql</a> to enable the REST endpoint feature. Then run <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/02-create-credentials.sql" rel="noopener" target="_blank">02-create-credentials.sql</a> to store your API key securely — replace the placeholders with your API Gateway ID, region, and API key from the CDK outputs.</p> 
<h4>Step 3: Create the CRM database</h4> 
<p>Run <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/03-crm-schema.sql" rel="noopener" target="_blank">03-crm-schema.sql</a> to create the CRM database, the <code>CustomerCommunications</code> and <code>APICallLog</code> tables, and insert sample data.</p> 
<h4>Step 4: Create stored procedures and trigger</h4> 
<p>Run <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/04-stored-procedures.sql" rel="noopener" target="_blank">04-stored-procedures.sql</a> to create the stored procedures for task submission and results retrieval — replace the API Gateway placeholders as you did in Step 2. The trigger fires after INSERT and calls a stored procedure that invokes <code>sp_invoke_external_rest_endpoint</code>. The following snippet shows the core API call:</p> 
<pre><code class="lang-sql">-- Core API call using stored credential (credential name matches URL) 
EXEC @returnValue = sp_invoke_external_rest_endpoint 
     @url = @url, 
     @payload = @payload, 
     @method = 'POST', 
     @credential = [https://&lt;api-id&gt;.execute-api.&lt;region&gt;.amazonaws.com/prod/submit], 
     @timeout = 30, 
     @response = @response OUTPUT; </code></pre> 
<p>Then run <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/05-triggers.sql" rel="noopener" target="_blank">05-triggers.sql</a> to create the database trigger.</p> 
<h4>Step 5: Set up the polling job</h4> 
<p>Run <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/06-polling-job.sql" rel="noopener" target="_blank">06-polling-job.sql</a> and <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/07-agent-job.sql" rel="noopener" target="_blank">07-agent-job.sql</a> to create a SQL Server Agent job that polls for completed results every 10 minutes. The job queries the <code>APICallLog</code> table for records with “Submitted” status and calls the results retrieval API for each one.</p> 
<h3>Testing the solution</h3> 
<p>With the infrastructure deployed and all SQL scripts executed, insert a test record to trigger the AI deep research:</p> 
<pre><code class="lang-sql">INSERT INTO CustomerCommunications  
    (CustomerId, CustomerSegment, CustomerRegion, CustomerTier, CommunicationText) 
VALUES  
    (1001, 'Enterprise', 'West', 'Gold',  
'Subject: URGENT - Where is my order? 
 
Hi Support Team, 
 
I placed order #ORD-2025-78432 on January 15th for 50 units of SKU-WH2000... 
We have a major client presentation on February 10th and absolutely need these headsets. 
 
Can you please check: 
1. Where is my shipment right now? 
2. What is the current stock level - can you ship from another warehouse? 
3. Is expedited shipping possible at this point? 
 
[...see 03-crm-schema.sql for complete sample data...]'); </code></pre> 
<p>After the agent completes processing, query the results. If you configured the SQL Server Agent job from <code>07-agent-job.sql</code>, it automatically polls for completed results every 10 minutes. You can also run the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore/blob/main/sql/04-stored-procedures.sql" rel="noopener" target="_blank">RetrieveAIDeepResearchResults</a> stored procedure manually to retrieve results immediately.</p> 
<pre><code class="lang-sql">SELECT CommunicationId, AdditionalNotes  
FROM CustomerCommunications  
WHERE CommunicationId = 1; </code></pre> 
<p>Sample output (redacted for brevity):</p> 
<pre><code>CommunicationId  AdditionalNotes 
---------------  --------------------------------------------------------------- 
1                ## Internal Deep Research Report 
                  
                 ### Executive Summary 
                 This report details a critical situation involving Gold-tier  
                 enterprise customer Mateo Jackson (AnyCompany Industries,  
                 Customer ID: 1001) regarding order ORD-2025-78432... 
                  
                 ### Order and Shipment Analysis 
                 - **Current Location:** Distribution Center - Chicago IL 
                 - **Estimated Delivery:** February 5, 2025 
                  
                 ### Inventory and Product Analysis 
                 | Warehouse    | Quantity Available | 
                 |--------------|-------------------| 
                 | Seattle-WH01 | 245 units         | 
                 | Austin-WH02  | 180 units         | 
                 | Chicago-WH03 | 92 units          | 
                  
                 ### Recommended Actions 
                 1. Contact FedEx for real-time updates 
                 2. Develop contingency shipping plan from alternate warehouse 
                 3. Customer communication with Gold-tier priority handling 
                  
                 **Urgency Rating: High** </code></pre> 
<p>The complete sample data and testing scripts are available in the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore" rel="noopener" target="_blank">GitHub repository</a>.</p> 
<h3>Best practices</h3> 
<p>When adapting this solution for production workloads, consider the following:</p> 
<ol> 
 <li><strong>Error handling:</strong> Implement robust error handling in stored procedures and log all API responses</li> 
 <li><strong>Timeout configuration:</strong> Set appropriate timeouts based on expected API response times</li> 
 <li><strong>Retry logic:</strong> Consider implementing retry logic for transient failures</li> 
 <li><strong>Polling strategy:</strong> Schedule results retrieval every 5-10 minutes for pending jobs using SQL Server Agent</li> 
 <li><strong>Monitoring:</strong> Use <a href="https://aws.amazon.com/cloudwatch/" rel="noopener" target="_blank">Amazon CloudWatch</a> to monitor API Gateway and Lambda invocations</li> 
 <li><strong>Credential rotation:</strong> Establish a process for rotating API keys stored in DATABASE SCOPED CREDENTIAL</li> 
 <li><strong>S3 lifecycle:</strong> Configure S3 lifecycle policies to clean up old result files</li> 
 <li><strong>Trigger performance:</strong> The trigger in this walkthrough calls <code>sp_invoke_external_rest_endpoint</code> synchronously, which holds the INSERT transaction open until the API responds. In production, decouple this by having the trigger write to a Service Broker queue or a staging table, and process the API calls asynchronously using activation procedures or a SQL Server Agent job.</li> 
</ol> 
<h3>Clean up</h3> 
<p>To avoid ongoing charges for the resources deployed in this walkthrough:</p> 
<ol> 
 <li>Run <code>cdk destroy</code> from the <code>cdk/</code> directory of the cloned repository to remove the API Gateway, Lambda function, AgentCore Runtime, S3 buckets, VPC resources, and associated IAM roles</li> 
 <li>Terminate the SQL Server EC2 instance if it was created for this walkthrough</li> 
</ol> 
<h3>Conclusion</h3> 
<p>SQL Server 2025’s <code>sp_invoke_external_rest_endpoint</code> enables powerful new integration patterns that reduce time-to-market for AI-powered applications. By invoking AI agents directly from database triggers, you can add AI capabilities to existing applications without modifying application code.</p> 
<p>This approach delivers:</p> 
<ul> 
 <li><strong>Faster time-to-market:</strong> No application code changes required</li> 
 <li><strong>Lower risk:</strong> Database-level integration minimizes testing scope</li> 
 <li><strong>Higher ROI:</strong> Leverage existing database investments</li> 
</ul> 
<p>To get started, clone the <a href="https://github.com/aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore" rel="noopener" target="_blank">aws-samples/sample-sql-server-ai-agents-with-amazon-bedrock-agentcore</a> repository and follow the steps in this walkthrough. For more information, explore the <a href="https://docs.aws.amazon.com/bedrock/latest/userguide/agents.html" rel="noopener" target="_blank">Amazon Bedrock AgentCore documentation</a> and <a href="https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-invoke-external-rest-endpoint-transact-sql" rel="noopener" target="_blank">SQL Server 2025 documentation</a>.</p>
