AWS CodePipeline + CloudFormation (GitHub → Build → Deploy)

Overview
- Source: GitHub via CodeStar Connection
- Region: eu-west-2 (London)
- Build: CodeBuild runs cfn-lint + validate
- Deploy: CloudFormation creates/updates stack `infra-core`

Repo Layout
- pipeline-template.json  (CFN for pipeline + roles + artifact bucket)
- buildspec.yml           (build stage validation)
- infra/infra-template.json (sample infra template)

Prereqs
- An AWS CodeStar Connection to GitHub in eu-west-2 (status: Connected).
- AWS CLI v2 configured for the target account and region.

Deploy the pipeline (eu-west-2)
1) Export parameters
   - OWNER=<your-github-owner>
   - REPO=<your-repo-name>
   - BRANCH=main
   - CONNECTION_ARN=<codestar-connection-arn>

2) Create/Update the stack
   aws cloudformation deploy \
     --region eu-west-2 \
     --stack-name pipeline-infra-core \
     --template-file pipeline-template.json \
     --capabilities CAPABILITY_NAMED_IAM \
     --parameter-overrides \
       RepositoryOwner=$OWNER \
       RepositoryName=$REPO \
       BranchName=$BRANCH \
       CodeStarConnectionArn=$CONNECTION_ARN \
       StackName=infra-core

3) Push repo content to GitHub main
   - Ensure infra/infra-template.json and buildspec.yml are in the repo root as shown.

Notes
- The deploy action expects the infra template at SourceOutput::infra/infra-template.json.
- Adjust build image/size via parameters BuildImage/BuildComputeType if needed.
- For additional IAM beyond S3 in deployments, extend CloudFormationRole policy.

