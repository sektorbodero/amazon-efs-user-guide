# Amazon EFS Limits<a name="limits"></a>

Following, you can find out about limitations when working with Amazon EFS\.

**Topics**
+ [Amazon EFS Limits That You Can Increase](#soft-limits)
+ [Resource Limits](#limits-efs-resources-per-account-per-region)
+ [Limits for NFS Clients](#limits-client-specific)
+ [Limits for Amazon EFS File Systems](#limits-fs-specific)
+ [Unsupported NFSv4 Features](#nfs4-unsupported-features)
+ [Additional Considerations](#limits-additional-considerations)

## Amazon EFS Limits That You Can Increase<a name="soft-limits"></a>

Service Quotas is an AWS service that helps you manage your quotas, or limits, from one location\. You can view all Amazon EFS limit values in the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\. You can also request a limit increase for the number of EFS file systems in an AWS Region using the Service Quotas console\. 

 You can also request an increase to the following Amazon EFS limits by contacting AWS Support\. To learn more, see [Requesting a Limit Increase](#request-limit-increase)\. The Amazon EFS service team reviews each request individually\. 


| Resource | Default Limit | 
| --- | --- | 
| Number of file systems for each customer account in an AWS Region | 1,000 | 

The default throughput limits apply to both throughput modes, Bursting and Provisioned\. For more information about these different modes, see [Amazon EFS Performance](performance.md)\.


| Resource | Default Limit | 
| --- | --- | 
| Total bursting throughput for all connected clients |  US East \(Ohio\) Region – 3 GB/s US East \(N\. Virginia\) Region – 3 GB/s US West \(N\. California\) Region – 1 GB/s US West \(Oregon\) Region – 3 GB/s Asia Pacific \(Mumbai\) – 1 GB/s Asia Pacific \(Seoul\) – 1 GB/s Asia Pacific \(Singapore\) – 1 GB/s Asia Pacific \(Tokyo\) – 1 GB/s Canada \(Central\) – 1 GB/s EU \(Frankfurt\) Region – 1 GB/s EU \(Ireland\) Region – 3 GB/s EU \(London\) Region – 1 GB/s EU \(Paris\) Region – 1 GB/s Asia Pacific \(Sydney\) Region – 3 GB/s  | 
| Total provisioned throughput for all connected clients |  All AWS Regions – 1 GB/s  | 

### Requesting a Limit Increase<a name="request-limit-increase"></a>

To request an increase for these limits through AWS Support, take the following steps\. The Amazon EFS team reviews each limit increase request\. 

**To request a limit increase through AWS Support**

1. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, and sign in if necessary\. Then choose **Create Case**\.

1. Under **Create case**, choose **Service Limit Increase**\.

1. For **Limit Type**, choose the type of limit to increase\. Fill in the necessary fields in the form, and then choose your preferred method of contact\.

## Resource Limits<a name="limits-efs-resources-per-account-per-region"></a>

Following are the limits on Amazon EFS resources for each customer account in an AWS Region\. 


| Resource | Limit | 
| --- | --- | 
| Number of mount targets for each file system in an Availability Zone | 1 | 
| Number of mount targets for each VPC | 400 | 
| Number of security groups for each mount target | 5 | 
| Number of tags for each file system | 50 | 
| Number of VPCs for each file system | 1 | 

**Note**  
Clients can also connect to mount targets in another account or VPC\. For more information, see [Mounting EFS File Systems from Another Account or VPC](manage-fs-access-vpc-peering.md)\.

## Limits for NFS Clients<a name="limits-client-specific"></a>

The following limits for NFS clients apply, assuming a Linux NFSv4\.1 client:
+ The maximum throughput you can drive for each NFS client is 250 MB/s\.
+ Up to 128 active user accounts for each client can have files open at the same time\. Each user account represents one local user logged in to the instance\. A user account that is logged in multiple times counts as one active user\.
+ Up to 32,768 files open at the same time on the instance\. Listing directory contents doesn't count as opening a file\.
+ Each unique mount on the client can acquire up to a total of 8,192 locks across a maximum of 256 unique file\-process pairs\. For example, a single process can acquire one or more locks on 256 separate files\. As another example, eight processes can each acquire one or more locks on 32 files\.
+ When connecting to Amazon EFS, NFS clients located on\-premises or in another AWS Region can observe lower throughput than when connecting to EFS from the same AWS Region\. This effect is because of increased network latency\. Network latency of 1 ms or less is required to achieve maximum per\-client throughput\. Use the DataSync data migration service when migrating large datasets from on\-premises servers to EFS\.
+ Using Amazon EFS with Microsoft Windows isn't supported\.

## Limits for Amazon EFS File Systems<a name="limits-fs-specific"></a>

The following are limits specific to the Amazon EFS file systems:
+ Maximum name length: 255 bytes\.
+ Maximum symbolic link \(symlink\) length: 4,080 bytes\.
+ Maximum number of hard links to a file: 177\.
+ Maximum size of a single file: 52,673,613,135,872 bytes \(47\.9 TiB\)\.
+ Maximum directory depth: 1,000 levels deep\.
+ Any one particular file can have up to 512 locks across all instances connected and users accessing the file\.
+ In General Purpose mode, there is a limit of 7,000 file system operations per second\. This operations limit is calculated for all clients connected to a single file system\.

## Unsupported NFSv4 Features<a name="nfs4-unsupported-features"></a>

Although Amazon Elastic File System doesn't support NFSv2, or NFSv3, Amazon EFS supports both NFSv4\.1 and NFSv4\.0, except for the following features:
+ pNFS
+ Client delegation or callbacks of any type
  + Operation OPEN always returns `OPEN_DELEGATE_NONE` as the delegation type\. 
  + The operation OPEN returns `NFSERR_NOTSUPP` for the `CLAIM_DELEGATE_CUR` and `CLAIM_DELEGATE_PREV` claim types\.
+ Mandatory locking

  All locks in Amazon EFS are advisory, which means that READ and WRITE operations don't check for conflicting locks before the operation is executed\. 
+ Deny share

  NFS supports the concept of a share deny\. A *share deny *is primarily used by Windows clients for users to deny others access to a particular file that has been opened\. Amazon EFS doesn't support this, and returns the NFS error `NFS4ERR_NOTSUPP` for any OPEN commands specifying a share deny value other than `OPEN4_SHARE_DENY_NONE`\. Linux NFS clients don't use anything other than `OPEN4_SHARE_DENY_NONE`\.
+ Access control lists \(ACLs\)
+ Amazon EFS doesn't update the `time_access` attribute on file reads\. Amazon EFS updates `time_access` in the following events:
  + When a file is created \(an inode is created\)\.
  + When an NFS client makes an explicit `setattr` call\. 
  + On a write to the inode caused by, for example, file size changes or file metadata changes\.
  + Any inode attribute is updated\.
+ Namespaces
+ Persistent reply cache
+ Kerberos based security
+ NFSv4\.1 data retention
+ SetUID on directories
+ Unsupported file types when using the CREATE operation: Block devices \(NF4BLK\), character devices \(NF4CHR\), attribute directory \(NF4ATTRDIR\), and named attribute \(NF4NAMEDATTR\)\.
+ Unsupported attributes: FATTR4\_ARCHIVE, FATTR4\_FILES\_AVAIL, FATTR4\_FILES\_FREE, FATTR4\_FILES\_TOTAL, FATTR4\_FS\_LOCATIONS, FATTR4\_MIMETYPE, FATTR4\_QUOTA\_AVAIL\_HARD, FATTR4\_QUOTA\_AVAIL\_SOFT, FATTR4\_QUOTA\_USED, FATTR4\_TIME\_BACKUP, and FATTR4\_ACL\.

   An attempt to set these attributes results in an `NFS4ERR_ATTRNOTSUPP` error that is sent back to the client\. 

## Additional Considerations<a name="limits-additional-considerations"></a>

In addition, note the following:
+ For a list of AWS Regions where you can create Amazon EFS file systems, see the [AWS General Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#elasticfilesystem-region)\.
+ You mount your file system from EC2 instances in your VPC by using the mount targets you create in the VPC\. You can also mount your file system on your EC2\-Classic instances, which are not in the VPC\. However, you must first link them to your VPC by using ClassicLink\. For more information about using ClassicLink, see [ClassicLink](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-classiclink.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ You can mount an Amazon EFS file system from on\-premises data center servers using AWS Direct Connect and VPN\.
+ VPC peering within a single AWS Region when using [certain Amazon EC2 instance types](manage-fs-access-vpc-peering.md) is supported\. Inter\-region VPC peering is supported for all instance types\.