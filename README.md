# google-action-tiger

A Google Assistant Action (with an invocation name of "tiger") which controls a Honeywell VISTA-20P alarm panel and opens/closes garage doors.  This Action is for private use only and is not published to the public Google Assistant directory. 

## Initial Setup

### Dependencies

1. [Amazon Web Services](https://aws.amazon.com) account
2. Google account
3. Download and install [gactions CLI](https://developers.google.com/actions/tools/gactions-cli)
4. Download and install [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)
5. A working and Internet accessible installation of [vistaicm-server](https://github.com/bradyholt/vistaicm-server)

### Lambda function

Follow these steps on the AWS Management Console to create an AWS Lambda function and corresponding API Gateway endpoint to handle requests for this Action from the Google Assistant service.

1. Go to the [Lambda AWS Management Console](https://console.aws.amazon.com/lambda/)
2. Create Lambda function
3. Choose "Blank Function" blueprint
4. On "Configure Triggers" step:
   1. Select "API Gateway"
   2. Use "google-action-tiger" as name
   3. Select "Open" for security
5. On "Configure function" step
   - Name: "google-action-tiger"
   - Runtime: Node.js 4.3
   - Handler: index.Handler
   - Role: Choose an existing Role
   - Existing role: lambda_basic_execution
7. Proceed to review and then click "Create function"
8. On the "Triggers" tab, note the API Gateway URL which will looks like this: https://0000ABC123.execute-api.us-east-1.amazonaws.com/prod/google-action-tiger

Although I would like to script the intial creation of the AWS Lambda function and corresponding API Gateway trigger, doing so through the AWS CLI is non-trival.  There is a [guide here](https://ig.nore.me/2016/03/setting-up-lambda-and-a-gateway-through-the-cli/) which walks through the process and would be a good reference for future enhancement.

### Config

Copy the `config.example` file to a new file named `config` and update the config values:

- INVOCATION_NAME - The word used to initiate a conversation with the Google Action
- HTTP_EXECUTION_URL - The AWS API Gateway URL that points to the AWS Lambda function
- LAMBDA_FUNCTION_NAME - Name of the AWS Lambda function
- VISTA_ICM_ADDRESS - The URL for the vistaicm-server server
- VISTA_ICM_COMMAND_ARM - The vistaicm-server command to arm the alarm
- VISTA_ICM_COMMAND_DISARM - The vistaicm-server command to disarm the alarm (this is the security code)
- VISTA_ICM_COMMAND_PANIC - The vistaicm-server command to initiate the panic status
- VISTA_ICM_COMMAND_LEFT_GARAGE - The vistaicm-server command to trigger the left garage door to open/close
- VISTA_ICM_COMMAND_RIGHT_GARAGE - The vistaicm-server command to trigger the right garage door to open/close

## Deploy

To deploy the AWS Lambda function and the Google Assistant Action (in preview mode for 24 hours), run the following command:

`./deploy.sh`

## Preview Refresher

Even if a very long `preview_mins` value is provided when previewing the action, there is a limit to the preview duration and the action will eventually stop working.  So, the `preview_refresher` directory contains an Ansible playbook that will provision a server to run `gactions preview ...` with a cron job every 24 hours.  This way the Action will be available in preview mode indefinitely.  To provision a server as a Preview Refresher:

1. Copy the `preview-refresher/config.example` file to a new file named `preview-refresher/config` and update the config values:
 - [server] - On the line immediately following the `[server]` block, replace `0.0.0.0` and provide the IP address for the target server
 - deploy_user - The name of the user on <the></the> targer server under which the Preview Refresher will be installed
 - deploy_directory - The directory on the target server to use for installation

2. Run `./preview-refresher/provision.sh`