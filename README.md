# data-sync-s3-3-accounts
CFN template to manage DataSync jobs across 3 different accounts (DS resources, source data, destination accounts)

CFTs needed to automate the DataSync task creation for Customer’s scenario of 3 accounts (DataSync administration account, source bucket account, destination bucket account). The templates factor in the KMS encryption of your S3 buckets

The CFTs span across 3 accounts:
- Account 1 – DataSync resources
* Account 2 – Source S3 Bucket
+ Account 3 – Destination S3 Bucket
 

There are 4 CFTs uploaded at the above location:
- Account 1 - CFT to create DataSync Policies to copy S3 data and encrypt/decrypt using KMS keys. Attach the policies to a IAM role.
* Account 2 – CFT to create S3 bucket policy
+ Account 3 – CFT to create S3 bucket policy
- Account 1 – CFT to create DataSync location and task
 
Currently, we couldn’t find a CFT for updating KMS key policy. You will have to get the Account 1 and Account 2 folks update them manually.
```
{
    "Sid": "Allow use of the key",
    "Effect": "Allow",
    "Principal": {
         "AWS": "<DataSync Role ARN>"
    },
    "Action": [
          "kms:Encrypt",
          "kms:Decrypt",
          "kms:ReEncrypt*",
          "kms:GenerateDataKey*",
          "kms:DescribeKey"
    ],
    "Resource": "<KMS Key ARN>"
}
```
 
Further, if you don’t want to run the CF stack creation using the DataSync role and want to use another principal, please update the Principal in the Bucket Policy of Account 2 and Account 3 (statement starting at line 62 in the corresponding yaml files).
```
- Effect: Allow
      Principal: 
        AWS:
          - !Ref <Principal ARN>
      Action:
        - "s3:ListBucket"
        - "s3:GetObject"
        - "s3:GetEncryptionConfiguration"
        - "s3:PutEncryptionConfiguration"
      Resource: 
        - !Sub "arn:aws:s3:::${sourceS3Bucket}"
        - !Sub "arn:aws:s3:::${sourceS3Bucket}/*"
```
