# aws-infra

The purpose of this repo is to enable mult-account deployments for different projects. So, that we can have a Project A with staging and production accounts, and Project B with their own stagin and production account.

This is handled using AWS Organizations, where each Project is an Organizational Unit (OU).

An AWS account, called the TOOLING ACCOUNT is responsible for setting up these staging and production accounts and is separate from them for security and to follow the principle of least privilege. 

The Tooling acount is registered as the delegated administrator by the root.

## Deployment Workflow Steps
1. Sign in using the role GitHubActionsDeployRole into AWS TOOLING ACCOUNT
2. Give GitHubActionsDeployRole permissions to create an S3 bucket. This would be used to house the cross-acount-roles infrastructure code.
3. Create an S3 bucket called `deployment-template`.
4. Copy the `cross-acount-roles.yml` to the `deployment-template`. Accesss is restricted by the Organizational Unit.  *** This needs to be removed. AWS TOOLING ACCOUNT should not know of the organization ***
5. Check if we have a stack set  
    5.1. If does not exist then create a stack set and an instance of that stack set for that OUID.  
    5.2. If it does exist then update the stack set. Then check if we have a stack instance  
        5.2.1. If yes, then update stack will handle
        5.2.2. If no, then create a stack instance
6. In the end the CrossAccountDeployRole would be deployed to all the Accounts in the Organization.

## Why have CrossAccountDeployRole?
Once, the CrossAccountDeployRole has been set up staging and production accounts, then GitHub can assume this role to deploy the infrastructure in these accounts. It doesn't need to sign in to each accounts. It supports the principle of least privilege, promotes tracking, and easier to maintain, as all permissions should be listed there.

## Cleanup

The infrastructure can be deleted by manually running the clean workflow.

### Prerequisites
This requires Github signing and configuring with AWS Identity Provide. This is done manually using these steps:

#### Configure an OIDC Identity Provider in AWS:

1. In the **IAM Console**, navigate to **Identity providers** and choose **Add provider**.

- Select **OpenID Connect** as the provider type.

- For the **Provider URL**, enter https://token.actions.githubusercontent.com.

- Choose Get thumbprint to verify the server certificate.

- For **Audience**, enter `sts.amazonaws.com`.

2. Create an IAM Role:

- In the **IAM Console**, navigate to **Roles** and choose Create role.

- For Select type of trusted entity, choose **Web identity**.

- Select the Identity provider you created in the previous step.

- For Audience, select sts.amazonaws.com.

- For CloudFormation deployment, **CloudFormationFullAccess** was added

- The name of the role is **GitHubActionsDeployRole**

- Add to **GitHubActionsDeployRole** the `AWSCloudFormationFullAccess` and `IAMFullAccess` permissions