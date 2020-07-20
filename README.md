# About

This workshop content is build using [Material Design theme][2] for [MkDocs][1].

  [1]: https://www.mkdocs.org
  [2]: https://squidfunk.github.io/mkdocs-material/

## Quick start

Install the latest version of Material with `pip`:

``` sh
pip install mkdocs-material
```


Create this CloudFormation template file with name `workshop-site.yaml`
```
AWSTemplateFormatVersion: 2010-09-09
Description: Resources to host documentation.

Parameters:
  SiteName:
      Description: Site name
      Type: String
      Default: 'ejlp-mkdocs-aurora-workshop'

Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
        BucketName: !Ref SiteName
        AccessControl: PublicRead
        WebsiteConfiguration:
            IndexDocument: index.html
            ErrorDocument: error.html

  BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      PolicyDocument:
          Id: MyPolicy
          Version: 2012-10-17
          Statement:
            - Sid: PublicReadForGetBucketObjects
              Effect: Allow
              Principal: '*'
              Action: 's3:GetObject'
              Resource: !Join 
                - ''
                - - 'arn:aws:s3:::'
                  - !Ref S3Bucket
                  - /*
      Bucket: !Ref S3Bucket

Outputs:
  WebsiteURL:
    Value: !GetAtt
        - S3Bucket
        - WebsiteURL
    Description: URL for website hosted on S3
```
Build the site:
```    
mkdocs build
```

Run the CloudFormation template. The stack name will be used as S3 bucket so make it very unique.
```
aws cloudformation create-stack --template-body file://help-site.yaml --stack-name ejlp12-aurora-mysql-workshop
```

```
aws s3 sync ./site s3://ejlp12-aurora-mysql-workshop --recursive
```
