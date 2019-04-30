---
layout: post
title: Deploying to Amazon S3
category: Deploying your application
---

Amazon Simple Storage Service (Amazon S3) is a cloud storage service which
you can use to store and retrieve any amount of data
(photos, videos, documents etc.) at any time, from anywhere on the web.

You can also easily use Amazon S3 buckets to host a website which uses
client-side technologies (such as HTML, CSS, and JavaScript)
and does not require server-side technologies (such as PHP or Ruby on Rails).

This guide will show you how to easily set up a static website
on Amazon S3 using Semaphore Classic. If this is your first time deploying
a static website to Amazon S3, the following steps will guide you
through some basic concepts.

### Prerequisites
Before you begin, you will need to:

- Create an [AWS account](http://aws.amazon.com/)
- [Connect your GitHub or Bitbucket project to Semaphore Classic](/docs/adding-github-bitbucket-project-to-semaphore.html).

## Retrieving Security Credentials
Before you start, you will need to retrieve your AWS security credentials
which you will use later on to provide Semaphore Classic with access to your
application:

1. Go to your [AWS console](http://aws.amazon.com/) and select
"Security credentials" in the upper right corner below your name.
2. In the left navigation bar, click the "Users" option and create a new user.
The user you are about to create serves as a placeholder for a
set of permissions you will delegate to Semaphore Classic in order to set up
your website.
3. Choose a name for your user, download the credentials and
store them somewhere safe, because you will need those in one of the
following steps.

After you have created the user, you have to assign him the permissions. 
In the users list, click the username to see its page. You can read more
about assigning managed policies to users or groups
[here](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html#intro-access-users).
By clicking the "Attach policy" button under "Permissions", you will see
a list of predefined AWS policies.

If you choose `AmazonS3FullAccess`,
your setup will work, but this is usually not recommended. By following
the standard security advice of granting the least possible privilege,
you should grant only the permission required to perform this task.
You can use the example policy provided at the end of this article.

## Setting Up the Website
Amazon S3 stores data as objects within buckets. An Amazon S3 bucket is a
container for objects and an object consists of a file
and optionally any metadata that describes that file.
To use it for static website hosting, you basically need to create a bucket,
upload your files to it and configure the bucket to act as a website.
Semaphore Classic provides an easy way to do that. Go to Semaphore Classic and add new 
server for deployment to your project. As an option, choose Amazon S3 cloud storage.

### Choosing the Deployment Strategy
In this tutorial we will use Automatic deployment strategy. This means that
every time you commit a change to your repository, your website will be
automatically deployed.

Next, choose which branch contains the files you want to deploy. This
is the branch which will be automatically synced in the future.

### Entering the AWS credentials and selecting region

Semaphore Classic needs the AWS credentials to retrieve a list of your existing 
buckets or create a new one and perform the deployment. Paste the corresponding
values you have saved in the "Retrieving Security Credentials" section.

To reduce latency, minimize costs, or address regulatory requirements,
you can choose a region in the select box that is closer to the users for your
website.

If you pass that screen without getting any error messages, it means that your AWS 
credentials are working.

### Adding Build Commands
The files created during the build phase on Semaphore Classic won't be available
during deployment, so if you need to generate some files before deploying
them, this is where you should enter your build commands.

In case you don't want to upload your entire repository, just type in the
name of the directory that you want to upload. In our example, the build commands
will generate a directory named `build` which will contain the static files
and that is exactly the one we want to upload.
If your entire repository is a static website, you can leave both the
directory and command fields empty.

If you do not have the required AWS setup for listing all available buckets,
Semaphore Classic will fail to list all the buckets for you and will take you to 
the screen where you will need to put your AWS credentials again. However, this does not 
mean that your AWS credentials were incorrect. Please keep reading this documentation page 
to find out more about changing the AWS policy.

### Choosing a Bucket
The S3 Index document is the name of the file which will be your website's
home page. S3 will look for this file within the directory you specified
in the previous step (in our case, it will look in the `build` directory).
This file is usually called `index.html`.

Next, choose which bucket will contain your files. If you already have
buckets previously created, you can choose one of them from the select box.
Otherwise, you can create a new one by clicking the `Create new S3 bucket`
link at the bottom of the page. A text field will appear in which you will
enter the name of your new bucket.

The bucket name you choose must be unique across all existing bucket names
in Amazon S3. You can read more about bucket naming rules in
[Amazon S3 documentation](http://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html).
If you name your bucket `www.example.com` it will be accessible
through the URL that looks something like this:
`http://www.example.com.s3-website-eu-central-1.amazonaws.com`.
If you plan to use Amazon S3 static website hosting with a custom domain
such as `www.example.com` it is recommended that you use it to name
your bucket. If you want to learn how to set up a custom domain, you can
find more information in [this walkthrough](http://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html).
You can still use the predefined Amazon S3 URL if a custom domain
is not important to you (e.g. you just want to show the client how the
website will look like). After you have created the bucket, it will appear
in the select box and you can proceed to the next step.

### Naming your server
The last thing you need to do is name your server and create it.
Also, paste your website URL in the "Server URL" field to make it easily
accessible through Semaphore Classic.

### Uploading your Files
In the last step, click "Deploy" to start copying files from your
repository to Amazon S3.

When all commands have finished executing,
you can click the link to your website to see your homepage.

Congratulations! You have successfully configured your website to be
automatically updated with Semaphore Classic.

### Environment variables exported with S3 integration

You can view [which environment variables are exported during deploy to S3](/docs/available-environment-variables.html#aws-s3-variables),
together with some [common AWS environment variables](/docs/available-environment-variables.html#common-aws-variables).

### Example AWS IAM policy

This example shows a policy which provides Semaphore Classic with access to manage your
S3 buckets in the process of setting up your deployment. [This
section](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#create-managed-policy-console)
of AWS documentation explains how to create a custom policy. You can copy the
policy shown below.

```javascript
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1461232964000",
      "Effect": "Allow",
      "Action": [
        "s3:CreateBucket",
        "s3:DeleteObject",
        "s3:GetBucketLocation",
        "s3:GetBucketPolicy",
        "s3:GetBucketWebsite",
        "s3:GetObject",
        "s3:GetObjectAcl",
        "s3:ListAllMyBuckets",
        "s3:ListBucket",
        "s3:PutBucketWebsite",
        "s3:PutObject",
        "s3:PutObjectAcl"
      ],
      "Resource": [
        "arn:aws:s3:::*"
      ]
    }
  ]
}
```

### Minimal required permissions

Semaphore Classic lists all the S3 buckets with the [ListAllMyBuckets](https://docs.aws.amazon.com/cli/latest/reference/s3api/list-buckets.html)
action. For this to work, your policy should contain the following snippet,
along with the bucket specific policies:

```javascript
{
  "Sid": "VisualEditor1",
    "Effect": "Allow",
    "Action": [
      "s3:GetBucketLocation",
      "s3:ListAllMyBuckets"
    ],
    "Resource": "*"
}
```

When these permissions are missing, you will get an `Access denied` error when
listing the buckets, even if your AWS credentials (added in the first step) are valid.
