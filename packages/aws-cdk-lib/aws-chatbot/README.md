# AWS::Chatbot Construct Library


AWS Chatbot is an AWS service that enables DevOps and software development teams to use Slack chat rooms to monitor and respond to operational events in their AWS Cloud. AWS Chatbot processes AWS service notifications from Amazon Simple Notification Service (Amazon SNS), and forwards them to Slack chat rooms so teams can analyze and act on them immediately, regardless of location.

This module is part of the [AWS Cloud Development Kit](https://github.com/aws/aws-cdk) project.

```ts
import * as chatbot from 'aws-cdk-lib/aws-chatbot';
import * as sns from 'aws-cdk-lib/aws-sns';
import * as iam from 'aws-cdk-lib/aws-iam';

const slackChannel = new chatbot.SlackChannelConfiguration(this, 'MySlackChannel', {
  slackChannelConfigurationName: 'YOUR_CHANNEL_NAME',
  slackWorkspaceId: 'YOUR_SLACK_WORKSPACE_ID',
  slackChannelId: 'YOUR_SLACK_CHANNEL_ID',
});

slackChannel.addToRolePolicy(new iam.PolicyStatement({
  effect: iam.Effect.ALLOW,
  actions: [
    's3:GetObject',
  ],
  resources: ['arn:aws:s3:::abc/xyz/123.txt'],
}));

slackChannel.addNotificationTopic(new sns.Topic(this, 'MyTopic'));
```

## Log Group

Slack channel configuration automatically create a log group with the name `/aws/chatbot/<configuration-name>` in `us-east-1` upon first execution with
log data set to never expire.

The `logRetention` property can be used to set a different expiration period. A log group will be created if not already exists.
If the log group already exists, it's expiration will be configured to the value specified in this construct (never expire, by default).

By default, CDK uses the AWS SDK retry options when interacting with the log group. The `logRetentionRetryOptions` property
allows you to customize the maximum number of retries and base backoff duration.

*Note* that, if `logRetention` is set, a [CloudFormation custom
resource](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cfn-customresource.html) is added
to the stack that pre-creates the log group as part of the stack deployment, if it already doesn't exist, and sets the
correct log retention period (never expire, by default).

## Guardrails

By default slack channel will use `AdministratorAccess` managed policy as guardrail policy.
The `guardrailPolicies` property can be used to set a different set of managed policies.

## User Role Requirement

Administrators can [require user roles](https://docs.aws.amazon.com/chatbot/latest/adminguide/understanding-permissions.html#user-role-requirement) for all current channel members and channels and all channels created in the future by enabling a user role requirement.

You can configure this feature by setting the `userRoleRequired` property.

```ts
import * as chatbot from 'aws-cdk-lib/aws-chatbot';

const slackChannel = new chatbot.SlackChannelConfiguration(this, 'MySlackChannel', {
  slackChannelConfigurationName: 'YOUR_CHANNEL_NAME',
  slackWorkspaceId: 'YOUR_SLACK_WORKSPACE_ID',
  slackChannelId: 'YOUR_SLACK_CHANNEL_ID',
  userRoleRequired: true,
});
```
