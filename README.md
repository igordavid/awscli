# awscli

AWS CLI scripts

If you want to quickly list instances and their Security groups for particular environment, here is one-liner:

*`aws ec2 describe-instances --filter Name=tag:Environment,Values=ENVIRONMENT_NAME --query 'Reservations[*].Instances[*].{ID:InstanceId,SG:SecurityGroups,Tags:Tags}' --output text --profile profile_name`*

This will produce you nice output of your instances and their assigned Security groups. This can be filtered in multiple ways, but the best way is to select only first element without filters:

*`aws ec2 describe-instances --query 'Reservations[0].Instances[0] --output json`*

That way you will see which elements you have in your response, and based on that you can build filter.

This is VERY important feature which will do logical AND operation for those filters, so only values which are having ALL requirements will be in the output:

*`aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" "Name=tag:Environment,Values=ENV_NAME" "Name=tag:Project,Values=PROJECT_NAME" --profile PROFILE_NAME --query Reservations[*].Instances[*].State`*

If you specify like this (all filters together), output will be produced if ANY of those filters is found:

*`aws ec2 describe-instances --filters "Name=instance-state-name,Values=running,Name=tag:Environment,Values=ENV_NAME,Name=tag:Project,Values=PROJECT_NAME" --profile PROFILE_NAME --query Reservations[*].Instances[*].State`*

If you want to query and output certain values from Tags, for example instance Name, along with other details, you go like this:

*`aws ec2 describe-instances --filters 'Name=tag:KEY,Values=VALUE' --query 'Reservations[].Instances[].[Tags[?Key==`Name`] | [0].Value,InstanceId,InstanceType]' --output table`* 

If you want to query for Tags which doesn't exist, here is how (replace KEY and VALUE with your own) with using jq:

*`aws ec2 describe-volumes --region us-east-1 --filters "Name=tag:VALUE,Values=VALUE" | jq '.Volumes[] | select(contains({Tags: [{Key: "VALUE"} ]}) | not)'`*
