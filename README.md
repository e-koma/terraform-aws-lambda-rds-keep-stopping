# AWS Lambda RDS Keep Stopping Module

This Terraform module deploys a Lambda function to continuously stop RDS clusters.

RDS clusters automatically start after being stopped for one week.
Despite being stopped for cost optimization, there is a risk of unintended billing if left unattended, as clusters start automatically.

This Lambda function starts the specified RDS clusters at a cron schedule, and then immediately stops them again. By running this process every week, the Lambda function ensures that RDS clusters remain stopped, supporting cost optimization.
(Currently, only RDS cluster is supported, not RDS instances.)

It is recommended to set the cron schedule according to the maintenance window of the RDS.

## Usage
```hcl
module "lambda-rds-keep-stopping" {
  source  = "e-koma/lambda-rds-keep-stopping/aws"
  version = "1.0.0"

  aws_account_id  = "****"
  db_matcher_name = "sample,test"
}
```

## Specifying RDS Clusters

You can use either method 1 or 2.

1. Specify a matching string for the RDS Cluster name

```
db_matcher_name = "sample,test"
```

2. Add an 'autostop' tag

Add a tag with Key: `autostop` and Value: `true` to the Cluster tags.

## Input Variables

| Name                       | Type   | Default                     | Description                                                                           | Required |
|----------------------------|--------|-----------------------------|---------------------------------------------------------------------------------------|----------|
| aws_account_id             | string |                             | AWS Account ID for deployment                                                         | Yes      |
| aws_region                 | string | ap-northeast-1              | AWS Region for deployment                                                             | No       |
| lambda_function_name       | string | terraform-rds-keep-stopping | Name of the Lambda Function                                                           | No       |
| db_matcher_name            | string | sample                      | A comma-separated list of substrings for matching DB Cluster names, e.g., sample,test | No       |
| cloudwatch_start_schedule  | string | cron(0 22 ? * SUN *)        | A cron expression to periodically start DB Clusters                                   | No       |
| cloudwatch_stop_schedule   | string | cron(30 22 ? * SUN *)       | A cron expression to periodically stop DB Clusters                                    | No       |
