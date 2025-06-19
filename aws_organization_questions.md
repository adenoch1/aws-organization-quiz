What is AWS Organizations?
AWS Organizations is a service for centrally managing and governing multiple AWS accounts. It lets you group accounts into Organizational Units (OUs), apply Service Control Policies (SCPs), and consolidate billing.

What is the difference between a Management (formerly “Master”) account and a Member account?
The Management account creates the organization and pays the bills. Member accounts are linked into the organization and inherit SCPs, but cannot see consolidated billing details or invite other accounts.

What are Organizational Units (OUs)?
OUs are hierarchical groupings of AWS accounts within an organization. You can attach SCPs at the OU level to manage permissions for all accounts in that OU.

What is a Service Control Policy (SCP)?
An SCP defines the maximum permissions that IAM principals (users and roles) within an account can have. SCPs are “deny‑list” or “allow‑list” policies applied at the root, OU, or account level.

How does SCP inheritance work?
SCPs attached to the root apply to all accounts. SCPs on an OU apply to accounts and nested OUs. Effective permissions are the intersection of all SCPs in the path.

Can SCPs grant permissions?
No. SCPs can only restrict permissions. Even if an SCP allows an action, the account’s IAM policies must also allow it.

What’s the default SCP when you first create an organization?
By default, there is a “FullAWSAccess” SCP on the root, which allows all actions and does not restrict anything.

How do you whitelist actions using SCPs?
You attach a deny‑list SCP that denies everything ("Effect": "Deny", "Action": "*", "Resource": "*") and then explicitly allow needed actions using "NotAction" or conditional statements.

What AWS Organizations features relate to billing?
Consolidated billing allows all member accounts’ charges to roll up to the management account, enabling volume discounts and easier cost tracking.

Can you invite an existing AWS account to join an organization?
Yes, you can invite existing accounts by email or account ID; the owner must accept the invitation before joining.

What happens if you delete your organization’s management account?
You cannot delete the management account itself. You must first remove all member accounts (by either removing or migrating them) and then delete the organization.

How many accounts can you have in an AWS Organization by default?
By default, 10 accounts per organization. You can request a quota increase up to a soft limit (often 1,000).

How do you delegate administrative access for specific OUs?
Use Delegated Administrators for select AWS services (like AWS SSO or Config), assigning admin rights to member accounts for that service only.

What is “trusted access” in AWS Organizations?
Trusted access enables a service to integrate with AWS Organizations. For example, enabling AWS Config’s trusted access allows AWS Config to record across all member accounts.

Explain the difference between SCPs, IAM policies, and resource policies.

SCPs set account‑level max permissions.

IAM policies grant or deny permissions to a specific user/role within an account.

Resource policies set permissions on the resource itself (e.g., S3 bucket policy).

How can you use AWS Organizations to enforce tagging policies?
Use the Tag Policies feature to define allowed tag keys and values. AWS Organizations validates tags during resource creation.

What is AWS Control Tower and how does it relate to Organizations?
AWS Control Tower automates the setup of a best‑practice multi-account environment on top of AWS Organizations, provisioning OUs, SCPs, guardrails, and SSO.

How do you track service usage across all accounts in an organization?
Enable CloudTrail at the organization level and deploy it to a centralized S3 bucket. Use AWS Config Aggregator to roll up configuration data.

Can member accounts leave an organization on their own?
Yes, but only if they have no delegated services and are not the management account. They can then operate independently.

What is a Billing Conductor in AWS Organizations?
The AWS Billing Conductor lets you define custom billing structures (e.g., cost allocation) within an organization for more granular allocation.

How do you enforce MFA for root users across all accounts?
Create an SCP that denies any action if aws:MultiFactorAuthPresent is false for the root principal on member accounts.

How can you share resources like Route 53 Resolver rules across accounts?
Use the Resource Access Manager (RAM). You enable AWS RAM as a delegated administrator and share resources across OUs.

What is the AWS Organizations API operation to create an OU?
Use CreateOrganizationalUnit with parameters for the parent ID and the OU name.

How do AWS Organizations help with compliance requirements?
By applying SCPs and Tag Policies to enforce security configurations and by aggregating logs/config data centrally for auditing.

What are some best practices for OU structure design?
• Group by environment (e.g., Prod, Dev, Test)
• Group by function (e.g., Security, Networking)
• Keep OU depth shallow (max 5 levels)
• Plan for growth

How does consolidated billing affect Savings Plans or Reserved Instances?
These benefits automatically apply across all linked accounts, maximizing cost savings and utilization.

How can you automate organization management?
Use the AWS CLI or SDKs to call Organizations API operations in scripts, CloudFormation with the AWS::Organizations::* resources, or Terraform’s aws_organizations_* resources.

What is the difference between AWS Organizations and AWS SSO (Identity Center)?
AWS Organizations manages accounts and governance. AWS SSO (Identity Center) provides centralized user identity and single sign‑on across AWS accounts and apps. They integrate but serve different purposes.

How do you clean up an organization for decommissioning?
• Remove member accounts (invite them to leave)
• Delete OUs
• Detach SCPs and Tag Policies
• Delete the organization via the management account

Give an example of an SCP that denies S3 bucket deletion in production accounts.

json
Copy
Edit
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "DenyS3DeletionInProd",
    "Effect": "Deny",
    "Action": "s3:DeleteBucket",
    "Resource": "*",
    "Condition": {
      "StringEquals": {
        "aws:ResourceTag/Environment": "Production"
      }
    }
  }]
}
