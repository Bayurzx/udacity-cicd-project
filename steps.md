# Project 3: Give Your Application Auto-Deploy Superpowers
### Selling CI/CD :
- Convince your managers and human resources on why you need CICD in the language they understand... money, gains and losses

### Getting Started:
- Fork the starter code

```
.
├── ansible
│   ├── configure-server.yml
│   ├── deploy-backend.yml
│   ├── inventory
│   └── roles
│       ├── configure-prometheus-node-exporter
│       │   ├── files
│       │   │   └── node_exporter.service
│       │   └── tasks
│       │       └── main.yml
│       ├── configure-server
│       │   └── tasks
│       │       ├── main.yml
│       │       └── readme.md
│       └── deploy
│           └── tasks
│               ├── main.yml
│               └── readme.md
├── config.yml
├── config2.yml
└── files
    ├── backend.yml
    ├── cloudfront.yml
    └── frontend.yml
├── backend  # Do not run npm commands here (on your local machine). 
│   ├── src
│   └── test
├── frontend # Do not run npm commands here (on your local machine). 
│   ├── src
│   └── types
└── util     # Files relevant for the running the app locally (Optional).

```
- cloudfront.yml - This will instruct you to use this file to manually create a stack.
- backend.yml and frontend.yml - Your CircleCI job will run these files. We will hold you along the step-by-step instructions ahead.
> Note that no changes are required in three files mentioned above, except updating the EC2 key pair name and suitable AMI name in the backend.yml file later.
- Ansible Playbooks and Roles - The /.circleci/ansible directory contains the playbook files and roles that you will develop, per instructions on the next page.

- CircleCI config.yml file - We left a scaffolded/incomplete /.circleci/config.yml file to help you get started with CirlcCI's configuration. This file has intentionally failing/incomplete jobs. To call attention to unfinished jobs, we left some "non-zero error codes" (e.g. exit 1) 

### Deploying Working on CircleCI
- Build Phase
  - We used `- image: circleci/node:13.8.0` for most of our builds
  - We were able to `save_cache` by selecting a `path` and `key` to cache to use in `testing` and `scanning` phase by using `restore_cache`
  - Added some test which we failed and passed on purpose [test build](\backend\src\main.ts)

- Test Phase
  - Some of the test we fixed:
    - [Testing](https://github.com/udacity/cdond-c3-projectstarter/blob/7224513b309e3cf1a34497fdc8682ef4d50413d2/frontend/src/app/components/LoadingMessage/LoadingMessage.spec.tsx#L11)
    - [Testing2](https://github.com/udacity/cdond-c3-projectstarter/blob/7224513b309e3cf1a34497fdc8682ef4d50413d2/backend/src/modules/domain/employees/commands/handlers/employee-activator.handler.spec.ts#L22)

- Analyse Phase: We did some scanning with NPM using `npm audit --audit-level=critical
  - > Note that the --audit-level parameter above specifies the minimum vulnerability level that will cause the command to fail. This option does not filter the report output, it simply changes the command's failure threshold.`
  - need to upgrade the vulnerable packages in the backend/package.json file in your local manually to:
``` json
"class-validator": "0.12.2"
"standard-version": "^7.0.0"
```
  - TO foce above cirtical level fix use `npm audit fix --audit-level=critical --force`

- Alerts
  - The steps for configurinig the Slack notification are
    - [Setup Authentication](https://github.com/CircleCI-Public/slack-orb/wiki/Setup)
    - [Integrate Slack orb](https://circleci.com/developer/orbs/orb/circleci/slack)
  - We added the code below to send messages to slack workspace through their APIs with a job called `- slack/notify` it has `channel`, `event`, `template`
    - **Channel:** the channel ID was used instead of the name for precision. get by checking info
    - **event:** The event was based on either `pass` or `fail` this time. It will activate the a template that will send the msg to slack
    - **template:** There are other advanced templates but we only used these two `basic_fail_1`, `basic_success_1`.
    - Dont forget to add the orb to identify the `slack/notify` job

``` yml
orbs:
  slack: circleci/slack@4.10.1

      # Alerts
      - slack/notify:
          channel: "C03RFUC0BDW, C03QP7VHQTF"
          event: fail
          template: basic_fail_1
      - slack/notify:
          channel: "C03RFUC0BDW, C03QP7VHQTF"
          event: pass
          template: basic_success_1

```

### Configuration Management Tool to Accomplish Deployment to Cloud-Based Servers
- In AWS make sure you have your 
  - Key pairs. [Check out](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)
  - [IAM user for AWS-CLI](https://serverless-stack.com/chapters/create-an-iam-user.html)
  - AWS RDS for postgres. [Tutorial](https://aws.amazon.com/getting-started/tutorials/create-connect-postgresql-db/)
  
- Setup - CloudFront Distribution
  - Here you will manually create `aws bucket` and run `cloudfront.yml` template:
``` bash
aws s3 mb s3://udapeople-<unique_id> --region us-east-1
cd .circleci/files
aws cloudformation deploy \
         --template-file cloudfront.yml \
         --stack-name InitialStack\
         --parameter-overrides WorkflowID=udapeople-<unique_id>
```

  - Note that after the initial stack is created, subsequent executions of the cloudfront.yml template will modify the same CloudFront distribution to make the `blue-to-green` switch without fail.

- Add your key-pair `myEC2.pem` file as a fingerprint
  - Add SSH Key pair from EC2 to the CircleCI Project Settings > SSH Keys > Additional SSH Keys, as [shown here](https://circleci.com/docs/2.0/add-ssh-key/). To get the actual key pair, you'll need to open the pem file in a text editor and copy the contents. Then you can paste them into Circle CI.

- Add the following as env variables
``` env
AWS_ACCESS_KEY_ID=(from IAM user with programmatic access)
AWS_SECRET_ACCESS_KEY= (from IAM user with programmatic access)
AWS_DEFAULT_REGION=(your default region in aws)
TYPEORM_CONNECTION=postgres
TYPEORM_MIGRATIONS_DIR=./src/migrations
TYPEORM_ENTITIES=./src/modules/domain/**/*.entity.ts
TYPEORM_MIGRATIONS=./src/migrations/*.ts
TYPEORM_HOST={your postgres database hostname in RDS}
TYPEORM_PORT=5432 (or the port from RDS if it’s different)
TYPEORM_USERNAME={your postgres database username in RDS}
TYPEORM_PASSWORD={your postgres database password in RDS}
TYPEORM_DATABASE=postgres {or your postgres database name in RDS}
```

- Infrastructure Phase
  - `deploy-infrastructure(Create the Infrastructure): ` Create the frontend, backend and cloudfront infrastructure with the three yml files [here](https://github.com/Bayurzx/udacity-cicd-project/tree/master/.circleci/files)
    - Deploy both frontend and backend using `aws cloudformation deploy`
    - Remember to add `--parameter-overrides` as a dynamic value. If you check the backend.yml file you notice the tag ID was used and overridden

    ``` yml
    # in backend.yml
      EC2Instance:
        Type: AWS::EC2::Instance
        ...
          Tags:
            - Key: Name
              Value: !Sub backend-${ID}

    # in config.yml @ Ensure back-end infrastructure exists & Ensure front-end infrastructure exists
          - run:
              name: Ensure back-end infrastructure exists
              command: |
                aws cloudformation deploy \
                  --template-file .circleci/files/backend.yml \
                  --tags project=Udapeople \
                  --stack-name "udapeople-backend-${CIRCLE_WORKFLOW_ID:0:7}" \
                  --parameter-overrides ID="${CIRCLE_WORKFLOW_ID:0:7}"

    ```
    - added EC2 instance IP to the Ansible inventory, filtered it with the help of the `parameter-overide`
    - Save the `inventory` file to our workspace witht eh help of `persist_to_workspace`
  
  - `configure-infrastructure`
    - `add_ssh_keys`: we need ssh access to the instance we deployed which usees the myEC2.pem key we added. We will use the feature `add_ssh_keys` to do that
    - `attach_workspace` literally attach the workflow’s workspace to the current container. [Learn more](https://circleci.com/docs/configuration-reference#attachworkspace)
    - `restore_cache`: Restores a previously saved cache based on a key. Useful for node_modules. [Learn more](https://circleci.com/docs/configuration-reference#restorecache)

  - `run-migrations`: basically helped us to Send migration status to a 3rd party key-value store such as kvdb.io. I wrote abour kvdb earlier
  - `deploy-frontend`, `deploy-backend` seems straight forward if you read the code
  
  - `smoke-test`: was a bit of trouble 
    - I couldn't curl into the `"http://${BACKEND_IP}:3030/api/status"` because our db didn't allow ajust any ip connect. I simply fixed the security group manually
    - I used `if curl -s -v --connect-timeout 30 --retry-connrefused 5 "${API_URL}" | grep "ok" ` instead of `if curl "${API_URL}" | grep "ok"` because the later quickly gave up
    - Frontend smoke test was not a problem
  
  - `Rollback Phase`: You might have noticed the `revert-migrations` to roll back any migrations that were successfully applied during this CI/CD workflow, and `destroy-environment`, which was useful for terminating our stack incase of an error
    - Trigger rollback jobs if the smoke tests or any following jobs fail.
    - Delete files uploaded to S3.
    - Destroy the current CloudFormation stacks using the same stack names you used when creating the stack earlier (front-end and back-end).

  - `cloudfront-update`: Did some little blue-green deployment here to update our `InitialStack` which we manually deployed in the begining

  - `cleanup`: I was able to create a bash command to identify every single stack that was not the current workflow

### Turn Errors into Sirens
- Manually create an EC2 instance and SSH into it.
- Set up Prometheus Server on EC2 following [these instructions.](https://codewizardly.com/prometheus-on-aws-ec2-part1/)
- Configure Prometheus for AWS Service Discovery following [these instructions.](https://codewizardly.com/prometheus-on-aws-ec2-part3/)
- Setup Back-End Monitoring: In order for EC2 server instances to speak to Prometheus, we need to install an “exporter” in each one. Create a job that uses Ansible to go into the EC2 instance and install the exporter.
  - Use Ansible to install the node exporter and follow this [tutorial](https://codewizardly.com/prometheus-on-aws-ec2-part2/)
  - You need to use `.circleci\ansible\roles\configure-prometheus-node-exporter` but activate it from `.circleci\ansible\configure-server.yml`
- Setting up alert I used [this tutorial](https://codewizardly.com/prometheus-on-aws-ec2-part4/) but instead of email. I used slack 
  - My `/etc/prometheus/alertmanager.yml` was different from the tutorial. I used : [this](.circleci\ansible\roles\configure-prometheus-node-exporter\files\alertmanager.yml) instead from [this tutorial]([https://](https://grafana.com/blog/2020/02/25/step-by-step-guide-to-setting-up-prometheus-alertmanager-with-slack-pagerduty-and-gmail/))
  - Note that 2 instances were required by the tutorial from 1-4

### Workflow Section of config.yml
- requires ensure that the job doesn't run unless it passed the requirement
- filter will allow the job to only continue if it is the specified job
``` yml
          filters:
            branches:
              only: [master]
```

