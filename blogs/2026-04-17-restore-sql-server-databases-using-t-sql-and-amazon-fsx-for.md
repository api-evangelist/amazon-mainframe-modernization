---
title: "Restore SQL Server databases using T-SQL and Amazon FSx for NetApp ONTAP Snapshots"
url: "https://aws.amazon.com/blogs/modernizing-with-aws/restore-sql-server-databases-using-t-sql-and-amazon-fsx-for-netapp-ontap-snapshots/"
date: "Fri, 17 Apr 2026 14:59:41 +0000"
author: "Vikas Babu Gali"
feed_url: "https://aws.amazon.com/blogs/modernizing-with-aws/feed/"
---
<p>In modern database environments, the ability to quickly restore databases is just as critical as creating reliable backups. An 11-hour database restore window is no longer acceptable in today’s enterprise workloads. Here’s how to cut that to under 10 minutes<em>.</em></p> 
<p>In our previous <a href="https://aws.amazon.com/blogs/modernizing-with-aws/mastering-sql-server-backups-with-t-sql-snapshots-and-amazon-fsx-for-netapp-ontap/" rel="noopener noreferrer" target="_blank">blog</a>,&nbsp;we discussed how to create application consistent snapshot backups for Microsoft SQL Server databases using T-SQL and <a href="https://aws.amazon.com/fsx/netapp-ontap/" rel="noopener noreferrer" target="_blank">Amazon FSx for NetApp ONTAP (FSxN</a>).</p> 
<p>In this post you will learn how to restore SQL Server databases from application-consistent backups using T-SQL commands and FSxN snapshots. This approach reduces database restore times and simplifies the restore process using FSxN snapshots.</p> 
<h2>Prerequisites</h2> 
<ol> 
 <li>Deploy highly available SQL Server 2022 on <a href="https://aws.amazon.com/ec2/" rel="noopener noreferrer" target="_blank">Amazon Elastic Compute Cloud</a> (Amazon EC2) with FSxN referring to the guidelines on this <a href="https://aws.amazon.com/blogs/modernizing-with-aws/sql-server-high-availability-amazon-fsx-for-netapp-ontap/" rel="noopener noreferrer" target="_blank">blog</a>.</li> 
 <li>Create Application consistent T-SQL Snapshot backups using this <a href="https://aws.amazon.com/blogs/modernizing-with-aws/mastering-sql-server-backups-with-t-sql-snapshots-and-amazon-fsx-for-netapp-ontap/" rel="noopener noreferrer" target="_blank">blog</a>.</li> 
 <li>SSH connect using <a href="https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.5" rel="noopener noreferrer" target="_blank">PowerShell7</a>.</li> 
</ol> 
<h2>Solution Overview</h2> 
<p>This solution combines SQL Server’s native restore capabilities with FSxN snapshots to provide fast, consistent database restores. The following architecture in Figure 1 shows the key components and their interactions.</p> 
<h2><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-1.png"><img alt="Architecture diagram showing the interaction between SQL Server on EC2 and FSx for ONTAP for database restores" class="aligncenter size-full wp-image-11021" height="821" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-1.png" width="1378" /></a></h2> 
<h2></h2> Figure 1 Solution diagram
<p></p> 
<h2>Step-by-Step Workflow</h2> 
<p>The flowchart below in figure 2 outlines the complete process for restoring T-SQL snapshot backups.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-2.png"><img alt="A diagram of a flowchart." class="aligncenter size-full wp-image-11020" height="1523" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-2.png" width="961" /></a></p> 
<p>Figure2 Workflow diagram</p> 
<h2>Walkthrough</h2> 
<h2>Connect to FSxN</h2> 
<p>In this blog, we use PowerShell 7 to connect to FSxN and execute ONTAP CLI commands.The DNS name or management IP address for accessing FSxN is available in the AWS Console after selecting the relevant file system (see Figure 3).</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-3.png"><img alt="Screenshot showing the FSx for ONTAP connection information in AWS Console" class="aligncenter size-full wp-image-11019" height="1586" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-3.png" width="2654" /></a></p> 
<p>Figure 3 Connection Info for FSxN</p> 
<p>As shown in Figure 4, establish a connection to the file system using PowerShell 7 with ‘fsxadmin’ credentials to execute the necessary ONTAP commands.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-4.png"><img alt="Screenshot showing successful PowerShell connection to FSx for ONTAP" class="aligncenter size-full wp-image-11018" height="730" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-4.png" width="1930" /></a></p> 
<p>Figure 4 Connect to FSxN Filesystem using Powershell 7</p> 
<h2>Database Restore Scenarios</h2> 
<p>You can use this solution for several common restore scenarios:</p> 
<ol> 
 <li><strong>Data Corruption Recovery: </strong>Restore from the last known good backup when data becomes corrupted.</li> 
 <li><strong>Development and Testing: </strong>Create database copies for non-production environments</li> 
 <li><strong>Database Migration: </strong>During the process of migrating data from one server to another, restore backup to the new servers.</li> 
 <li><strong>Compliance Requirements: </strong>Support audit and compliance verification.</li> 
 <li><strong>Accidental Deletion Recovery: </strong>If critical data is accidentally deleted, restoring from a backup can recover the lost information.</li> 
</ol> 
<p>In this blog we focus on use cases 1 and 5 where you can use the volume snapshot restore feature to restore the specific database on the same EC2 instance. In other cases, 2,3 and 4 you can create a new database from the snapshot using <a href="https://www.netapp.com/learn/aws-fsxn-blg-reduce-costs-and-increase-efficiency-with-fsx-for-ontap-cloning/#h_h2" rel="noopener noreferrer" target="_blank">flexclone</a> feature.</p> 
<p>In this blog we focus on two primary restore approaches:</p> 
<ol start="6"> 
 <li>Full Database Restore using full metadata backup and FSxN snapshot</li> 
 <li>Point-in-Time Recovery (PITR) using full metadata backup, FSxN snapshot and Transaction log backups</li> 
</ol> 
<h2>Restore SQL Server Database</h2> 
<p>Before starting the restore process, you need to identify available backups. Here’s how:</p> 
<ol> 
 <li>Connect to your SQL Server instance using SSMS</li> 
 <li>Run this T-SQL query to list available backups:</li> 
</ol> 
<pre><code class="lang-sql">SET NOCOUNT ON;
SELECT bs.database_name, bs.backup_finish_date, bm.physical_device_name 
FROM msdb.dbo.backupset bs INNER JOIN msdb.dbo.backupmediafamily bm ON bs.media_set_id = bm.media_set_id
WHERE bs.database_name = 'testdb1';</code></pre> 
<p>The query returns list of available backups, as shown in Figure 5.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-5.png"><img alt="Query results showing available backups for the database" class="aligncenter size-full wp-image-11017" height="544" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-5.png" width="1138" /></a></p> 
<p>Figure 5 Query results showing available backups for the database</p> 
<p>For this tutorial, we will demonstrate 2 types of restore:</p> 
<ol> 
 <li>Full backup restore to snapshot and metadata backup taken at ‘2025-08-22T13:18:19</li> 
 <li>Point in time restore (PITR) to the point in time ‘2025-08-22T13:32:36’.</li> 
</ol> 
<p>You’ll need:<br /> For full backup restore:</p> 
<ul> 
 <li>FSxN snapshot:&nbsp;testdb1_20250822131819</li> 
 <li>Metadata backup file:&nbsp;D:\MSSQL\Backup\testdb1_20250822131819.bkm</li> 
</ul> 
<p>For PITR restore:</p> 
<ul> 
 <li>FSxN snapshot:&nbsp;testdb1_20250822131819</li> 
 <li>Metadata backup file:&nbsp;D:\MSSQL\Backup\testdb1_20250822131819.bkm</li> 
 <li>Transaction log files: 
  <ul> 
   <li>D:\mssql\backup\testdb1_tran20250822132310.trn</li> 
   <li>D:\mssql\backup\testdb1_tran20250822132442.trn</li> 
   <li>D:\mssql\backup\testdb1_tran20250822132942.trn</li> 
   <li>D:\mssql\backup\testdb1_tran20250822133442.trn</li> 
  </ul> </li> 
</ul> 
<h3>Database Restore Process</h3> 
<ol> 
 <li>Connect to SQL Server EC2 instance using SSMS and execute the following T-SQL to take database offline</li> 
</ol> 
<pre><code class="lang-sql">ALTER DATABASE [testdb1]SET OFFLINE WITH ROLLBACK IMMEDIATE</code></pre> 
<ol start="2"> 
 <li>As the SQL Server is configured in Always on Failover cluster, remove cluster dependencies by running following commands in PowerShell as shown in Figure 6.</li> 
</ol> 
<pre><code class="lang-powershell">Remove-ClusterResourceDependency -Resource "SQL Server" -Provider "testdb1-Data" 
Remove-ClusterResourceDependency -Resource "SQL Server" -Provider "testdb1-Log"</code></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-6.png"><img alt="PowerShell output showing successful removal of cluster dependencies" class="aligncenter size-full wp-image-11016" height="676" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-6.png" width="2566" /></a></p> 
<p>Figure 6 Remove SQL dependency for Cluster disks</p> 
<ol start="3"> 
 <li>The next step is to unmap the LUNs by running the following ONTAP commands after connecting to FSxN, as shown in Figure 3.</li> 
</ol> 
<pre><span style="color: #333300;">lun mapping delete -vserver SQL_Cluster -path /vol/testdb1_Data/data -igroup SQL_igroup
lun mapping delete -vserver SQL_Cluster -path /vol/testdb1_Log/log -igroup SQL_igroup</span></pre> 
<ol start="4"> 
 <li>After unmapping the LUNs, restore data and log volumes from snapshot. Run the following ONTAP commands after connecting to FSxN. Accept the prompts as shown in Figure 7 and Figure 8.</li> 
</ol> 
<pre><span style="color: #333300;">volume snapshot restore -vserver SQL_Cluster -volume testdb1_Data -snapshot testdb1_20250822131819
volume snapshot restore -vserver SQL_Cluster -volume testdb1_Log -snapshot testdb1_20250822131819</span></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-7.png"><img alt="" class="aligncenter size-full wp-image-11015" height="1036" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-7.png" width="2676" /></a></p> 
<p>Figure 7 Data Volume snapshot restore</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-8.png"><img alt="Log Volume snapshot restore" class="aligncenter size-full wp-image-11014" height="1048" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-8.png" width="2644" /></a></p> 
<p>Figure 8 Log Volume snapshot restore</p> 
<p><strong>Note 1</strong>: For shared database volumes with each database on independent LUNs, use file-level restore commands.</p> 
<pre><span style="color: #333300;">volume snapshot restore-file -vserver SQL_Cluster -volume testdb1_Data -snapshot testdb1_20250822131819 -path /vol/testdb1_Data/data&nbsp;
volume snapshot restore-file -vserver SQL_Cluster -volume testdb1_Log -snapshot testdb1_20250822131819 -path /vol/testdb1_Log/log</span></pre> 
<p><strong>Note 2</strong>: Alternatively if you are using <a href="https://docs.netapp.com/us-en/ontap/consistency-groups/" rel="noopener noreferrer" target="_blank">ONTAP consistency groups</a> run the following ONTAP commands to restore the group containing your data and log volumes.</p> 
<pre><span style="color: #333300;">vserver consistency-group snapshot restore -vserver SQL_Cluster -consistency-group testgroup -snapshot testdb1_20250822131819</span></pre> 
<ol> 
 <li>Remap the LUNs back to the initiator group (igroup) by executing the following commands after connecting to FSxN, as shown in Figure 3.</li> 
</ol> 
<pre><span style="color: #333300;">lun mapping create -vserver SQL_Cluster -path /vol/testdb1_Data/data -igroup SQL_igroup
lun mapping create -vserver SQL_Cluster -path /vol/testdb1_Log/log -igroup SQL_igroup</span></pre> 
<ul> 
 <li> 
  <ol type="a"> 
   <li>Check if the LUNs are online after restore</li> 
  </ol> </li> 
</ul> 
<pre><span style="color: #333300;">lun show -vserver SQL_Cluster -path /vol/testdb1_Data/data -fields statelun show -vserver SQL_Cluster -path /vol/testdb1_Data/log -fields state</span></pre> 
<ul> 
 <li> 
  <ol type="a"> 
   <li>If the LUNs are not online, bring them online by running</li> 
  </ol> </li> 
</ul> 
<pre><span style="color: #333300;">lun modify -vserver SQL_Cluster -path /vol/testdb1_Data/data -state onlinelun modify -vserver SQL_Cluster -path /vol/testdb1_Data/log -state online</span></pre> 
<ol> 
 <li>Rescan the disks on Windows so that it discovers the restored disk. Run below command in PowerShell,&nbsp;as shown in Figure 9.</li> 
</ol> 
<pre><code class="lang-powershell">echo "RESCAN" | diskpart</code></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-9.png"><img alt="Disk rescan" class="aligncenter size-full wp-image-11013" height="552" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-9.png" width="1556" /></a></p> 
<p>Figure 9 Rescan the disks for changes</p> 
<ol start="2"> 
 <li>Bring the cluster resources online and restore SQL Server dependencies. Run the following PowerShell commands as shown in Figure 10.</li> 
</ol> 
<pre><code class="lang-powershell">Start-ClusterResource -Name 'testdb1-Data'Start-ClusterResource -Name 'testdb1-Log'
Add-ClusterResourceDependency -Resource "SQL Server" -Provider "testdb1-Data'
Add-ClusterResourceDependency -Resource "SQL Server" -Provider "testdb1-Log"</code></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-10.png"><img alt="Adding back cluster disks to SQL server and bringing online" class="aligncenter size-full wp-image-11012" height="1514" src="https://d2908q01vomqb2.cloudfront.net/8effee409c625e1a2d8f5033631840e6ce1dcb64/2026/03/12/V1944601746-10.png" width="2948" /></a></p> 
<p>Figure 10 Add Cluster disks to SQL Server dependency</p> 
<ol start="3"> 
 <li>Restore database by choosing one of these restore options:</li> 
</ol> 
<p><strong>Option 1: Restore Full backup</strong></p> 
<ul> 
 <li> 
  <ol type="a"> 
   <li>For a simple restore without transaction logs</li> 
  </ol> </li> 
</ul> 
<p>RESTORE DATABASE [testdb1] FROM DISK=’D:\mssql\backup\ testdb1_20250822131819.bkm’ WITH REPLACE,METADATA_ONLY;</p> 
<p><strong>Option 2: Restore Full backup and transactional logs to achieve PITR</strong></p> 
<ul> 
 <li> 
  <ul> 
   <li> 
    <ol type="i"> 
     <li>Restore from the last metadata backup with NORECOVERY</li> 
    </ol> </li> 
  </ul> </li> 
</ul> 
<pre><code class="lang-sql">RESTORE DATABASE [testdb1] FROM DISK='D:\mssql\backup\ testdb1_20250822131819.bkm' WITH METADATA_ONLY, NORECOVERY</code></pre> 
<p>ii.Restore transaction logs in sequence before the last intended transaction log backup with NORECOVERY.</p> 
<pre><code class="lang-sql">RESTORE LOG [testdb1] FROM&nbsp; DISK = N'D:\mssql\backup\testdb1_tran20250822132310.trn' WITH&nbsp; FILE = 1,&nbsp; NOUNLOAD,&nbsp; STATS = 5, NORECOVERY</code></pre> 
<pre><code class="lang-sql">RESTORE LOG [testdb1] FROM&nbsp; DISK = N'D:\mssql\backup\testdb1_tran20250822132442.trn’ WITH&nbsp; FILE = 1,&nbsp; NOUNLOAD,&nbsp; STATS = 5, NORECOVERY

RESTORE LOG [testdb1] FROM&nbsp; DISK = N' D:\mssql\backup\testdb1_tran20250822132942.trn’ WITH&nbsp; FILE = 1,&nbsp; NOUNLOAD,&nbsp; STATS = 5, NORECOVERY</code></pre> 
<p>iii.Restore final transactional log backup to the desired timestamp to achieve PITR.</p> 
<pre><code class="lang-sql">RESTORE LOG [testdb1] FROM&nbsp; DISK = N' D:\mssql\backup\testdb1_tran20250822133442.trn’ WITH&nbsp; FILE = 1,&nbsp; NOUNLOAD,&nbsp; STATS = 5,&nbsp; STOPAT = N'2025-08-22T13:32:36'</code></pre> 
<p>8. Bring Database online</p> 
<pre><code class="lang-sql">ALTER DATABASE [testdb1] SET ONLINE;</code></pre> 
<p><strong>Note:</strong> optionally, you can run integrity check of the database</p> 
<pre><code class="lang-sql">DBCC CHECKDB(testdb1) WITH NO_INFOMSGS, ALL_ERRORMSGS;</code></pre> 
<p>If you would like to automate the restore solution you can use this <a href="https://aws-samples.github.io/technical-notes-for-microsoft-workloads-on-aws/SQL%20Server/Guides/Automate_SQL_restore_using_snapshots_on_Amazon_FSx_for_ONTAP/" rel="noopener noreferrer" target="_blank">PowerShell</a> script. In addition, for dependent backup solution you can use this <a href="https://aws-samples.github.io/technical-notes-for-microsoft-workloads-on-aws/SQL%20Server/Guides/Automate_SQL_backup_using_snapshots_on_Amazon_FSx_for_ONTAP/" rel="noopener noreferrer" target="_blank">PowerShell</a> script. Both these need to be used in conjunction.</p> 
<h2>Important considerations</h2> 
<ol> 
 <li>Though the steps in this document considers separate data and log volume for database as best practise, it works the same if you have both data and log files shared in the same volume.</li> 
 <li>FSxN snapshot restore process is quick regardless of the volume size or activity of the Amazon FSx for ONTAP filesystem.</li> 
 <li>If multiple databases are sharing same volume without being segregated in to separate LUNs, restore operation will create downtime for all the databases sharing the same LUN. All databases would be restored to old point in time when the volume snapshot was created. It is always recommended to separate the production databases into independent LUNs/disks.</li> 
 <li>If you have automatic backups enabled or have manually created backups from the AWS FSx for ONTAP console, here’s what you need to do first: Look for any backups that were created after the snapshot you want to restore. Remove all backups that are more recent than your target snapshot. FSxN won’t allow you to restore a snapshot if there are newer snapshots linked to existing backups. Removing these later snapshots first ensures the restore completes successfully without conflicts.</li> 
</ol> 
<h2>Performance Metrics</h2> 
<p><strong>Test Environment</strong></p> 
<p>Our benchmarks were performed on the following configuration:</p> 
<ul> 
 <li><strong>SQL Server</strong>: 2022 Standard Edition (Windows Server 2019 FCI)</li> 
 <li><strong>EC2 Instance</strong>: r6in.4xlarge (16 vCPUs, 128 GiB RAM)</li> 
 <li><strong>Database</strong>: 5 TB TPCC database loaded with HammerDB</li> 
 <li><strong>Storage</strong>: Amazon FSxN (Multi-AZ) with 15,360 GiB SSD storage</li> 
 <li><strong>Performance</strong>: 256 MB/s throughput, 46,080 provisioned IOPS</li> 
</ul> 
<table border="1px" cellpadding="10px" class="styled-table"> 
 <tbody> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Operation</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Metric</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Traditional Method</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>T-SQL Snapshot</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Improvement</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Backup</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Time</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">8 hours</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">&lt;2 minutes</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>240x faster</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">CPU Impact</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 20Avg: 18-20%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 12%Avg: 8%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>2.3x lower</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Network Traffic</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">In: 21-23GBOut: 15-16GB</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">In: 3.5GBOut: 2GB</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>6-8x lower</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Storage Throughput Utilization</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 250-270%Avg: 110-154%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 50%Avg: 35%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>3-5x lower</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>Restore</strong></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Time</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">11 hours</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">&lt;5 minutes</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>132x faster</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">CPU Impact</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 22%Avg: 12%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 2.5%Avg: 1.6%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>8.8x lower</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Network Traffic</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">In: 20GBOut: 35GB</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">In: 252MBOut: 106MB</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>79-330x lower</strong></td> 
  </tr> 
  <tr> 
   <td style="padding: 10px; border: 1px solid #dddddd;"></td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Storage Throughput Utilization</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 340%Avg: 160%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;">Peak: 6.5%Avg: 4%</td> 
   <td style="padding: 10px; border: 1px solid #dddddd;"><strong>52x lower</strong></td> 
  </tr> 
 </tbody> 
</table> 
<h2>Clean Up</h2> 
<p>It is a best practice to delete resources that you are no longer using so that you do not incur unintended charges. You can clean up the following resources you created for this tutorial:</p> 
<ul> 
 <li><a href="https://aws.amazon.com/premiumsupport/knowledge-center/delete-terminate-ec2/" rel="noopener noreferrer" target="_blank">Amazon EC2 Instances</a></li> 
 <li><a href="https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/getting-started-step3.html" rel="noopener noreferrer" target="_blank">Amazon FSx for NetApp ONTAP</a></li> 
</ul> 
<h2>Conclusion</h2> 
<p>In this post, we showed how to restore SQL Server databases using Amazon FSx for NetApp ONTAP snapshots reducing restore times compared to traditional methods. For broader protection and backup policy management across SQL Server versions, consider using <a href="https://aws.amazon.com/blogs/storage/using-netapp-snapcenter-with-amazon-fsx-for-netapp-ontap-to-protect-your-sql-server-workloads/" rel="noopener noreferrer" target="_blank">NetApp SnapCenter</a> with FSx for NetApp ONTAP. Additionally, refer to <a href="https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/create-a-transact-sql-snapshot-backup?view=sql-server-ver16#limitations" rel="noopener noreferrer" target="_blank">Transact-SQL snapshot backup</a> for more details and limitations of the T-SQL based backup mechanism.</p> 
<p>AWS can help you assess how your company can get the most out of cloud. Join the millions of AWS customers that trust us to migrate and modernize their most important applications in the cloud. To learn more on modernizing Windows Server or SQL Server, visit <a href="https://aws.amazon.com/windows/" rel="noopener noreferrer" target="_blank">Windows on AWS</a>. <a href="https://pages.awscloud.com/MAP-windows-contact-us.html" rel="noopener noreferrer" target="_blank">Contact AWS</a> to start your migration journey today.</p>
