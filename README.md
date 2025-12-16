# aws-infra
Sets up the infra for AWS. The following stacks are deployed:
1. Control Tower


## Prerequisites
This requires Github signing and configuring with AWS Identity Provide. This is done manually using these steps:

### Configure an OIDC Identity Provider in AWS:

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
