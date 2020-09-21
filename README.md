# aws-sso-cloudformation-sample
Sample CloudFormation template for assigning an AWS accounts in AWS SSO.

## About Resource IDs
Creating an AWS SSO resource in CloudFormation requires various IDs such as InstanceArn, Identiy-Store-Id, UserId, and GroupId.  
These IDs are not available in the AWS SSO console and must be obtained via API.  

Here is an example of how to get various resource IDs in the AWS CLI.
To use the AWS SSO API (sso-admin), make sure you have AWS CLI version 1.18.136 or 2.0.48 or higher.

### InstanceArn/IdentityStoreId
IdentityStoreId is required to get the user ID and group ID with the IdentityStore API

```shell
$ aws sso-admin list-instances
{
    "Instances": [
        {
            "InstanceArn": "arn:aws:sso:::instance/ssoins-xxxxxxxxxxxxxxxx",
            "IdentityStoreId": "d-xxxxxxxxxx"
        }
    ]
}
```

### PermissionSetArn
```shell
$ aws sso-admin list-permission-sets \
> --instance-arn arn:aws:sso:::instance/ssoins-xxxxxxxxxxxxxxxx 
{
    "PermissionSets": [
        "arn:aws:sso:::permissionSet/ssoins-xxxxxxxxxxxxxxxx/ps-xxxxxxxxxxxxxxxx",
        "arn:aws:sso:::permissionSet/ssoins-xxxxxxxxxxxxxxxx/ps-yyyyyyyyyyyyyyyy",
        "arn:aws:sso:::permissionSet/ssoins-xxxxxxxxxxxxxxxx/ps-zzzzzzzzzzzzzzzz"
    ]
}

$ aws sso-admin describe-permission-set \
> --instance-arn arn:aws:sso:::instance/ssoins-xxxxxxxxxxxxxxx \
> --permission-set-arn arn:aws:sso:::permissionSet/ssoins-xxxxxxxxxxxxxxx/ps-xxxxxxxxxxxxxxx
{
    "PermissionSet": {
        "Name": "AdministratorAccess",
        "PermissionSetArn": "arn:aws:sso:::permissionSet/ssoins-xxxxxxxxxxxxxxx/ps-xxxxxxxxxxxxxxx",
        "CreatedDate": "2020-09-09T19:01:06.758000+09:00",
        "SessionDuration": "PT1H"
    }
}
```

### UserId/GroupId
```shell
$ aws identitystore list-users \
> --identity-store-id d-xxxxxxxxxx \
> --filters AttributePath=UserName,AttributeValue="user@example.com"
{
    "Users": [
        {
            "UserName": "userXX@examle.com",
            "UserId": "f81d4faxge-7dec11d8-a765-3at5-80e4-00a0c91e6bf6"
        }
    ]
}

$ aws identitystore list-groups \
> --identity-store-id d-xxxxxxxxxx \
> --filters AttributePath=DisplayName,AttributeValue="TestGroup"
{
    "Groups": [
        {
            "GroupId": "f81d4faxge-789fcfa5-005c-4379-89ba-10a11e641c17"
            "DisplayName": "TestGroup"
        }
    ]
}
```

## References
AWS CloudFormation User Guide - SSO resource type reference  
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_SSO.html

AWS CLI Command Reference - sso-admin  
https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sso-admin/index.html

AWS CLI Command Reference - identitystore  
https://awscli.amazonaws.com/v2/documentation/api/latest/reference/identitystore/index.html
