---
layout: page
title: "Making"
category: blog
date: "2017-07-25"
subtitle: "Behind the scenes"
permalink: '/making.html'
---

Building this online résumé was a personal challenge. I wanted to build my first from-scratch site. While it is not really practical to build pretty much everything from scratch, I picked the [Skeleton](http://getskeleton.com/) boiler plate. Having set up a few blogs using [Jekyll](https://jekyllrb.com/), the static site generator, I was confident using Jekyll. So [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll) became the starting point. After spending a couple of hours in setting up the logic to programmatically generate the site from the code, the basic setup was ready.

As an Infrastructure person, setting up the site on the cloud (and not GitHub Pages) intrigued me---this is the first time I'm doing this. Here's a gist of what happens behind the scenes:

1. I add data to the code (basically Markdown files) as posts. These changes are pushed to BitBucket, the source control provider for the site.
2. Wercker (a Docker-based continuous delivery platform) is triggered as soon as there's a push; it picks up the code from BitBucket.
3. Wercker has two pipelines, `build` and `deploy`, which, of course, build the site using Jekyll (on a Ruby box), and deploy it to the AWS&nbsp;S3 bucket that hosts the site.
4. CloudFront is the CDN that delivers the contents of the S3 bucket, to the Web.
5. Amazon's Certificate Manager provides the SSL certificate that encrypts connections to this site.
6. WordPress is my DNS provider---I basically bought this domain from them for blogging, but it can be used with AWS.

Now to the detailed picture. (This part has been published as a [GitHub Gist](https://gist.github.com/theramiyer/d88a00498245a760716ec0c3e3d9230e) as well, for the community.)

#### Setting up Jekyll

This document has been created for Windows&nbsp;10 Pro. While building the site locally is not entirely necessary, it helps when you need to build the site locally to test out its working.

Enable the [Linux Subsystem on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) feature, and install the necessary components required to be able to build sites using Jekyll. While there may be seemingly less-complicated ways to set up Jekyll, setting it up with WSL is much less hassle. [This post](http://daverupert.com/2016/04/jekyll-on-windows-with-bash/) by Dave Rupert helps a great deal with it. Also, some of the [Nokogiri binaries](http://daverupert.com/2016/06/ruby-on-rails-on-bash-on-ubuntu-on-windows/) need to be installed.

#### Building the site locally

There are really many ways to do this, the easiest way being to fork one of the existing templates and going forward by simply customising the content.

I wanted to build an uncomplicated site, so I used the [Skeleton](http://getskeleton.com/) boilerplate to build the site. There are two ways to go forward from here:

1. Use the `jekyll new` command to get the basic Jekyll site, and then add the Skeleton components.
2. Use the [Skeleton Framework for Jekyll](https://github.com/skeleton-framework/skeleton-framework-jekyll) and rebuild `index.html` and move forward.

I chose the latter. Sure, I had to still make a few changes to optimise the site, but hey, what's life without adventures?

#### Configuring the Amazon&nbsp;S3 bucket

Now comes the creation of the Amazon&nbsp;S3 bucket that would host the site contents:

1. Go to the [S3 Management Console](https://console.aws.amazon.com/s3). The prerequisite to this is that you have an AWS account.
2. Click on **Create bucket**.
3. Enter the name of the bucket exactly as the URL to host the site; I entered `resume.ramiyer.me` (because I want the site to be live at http://resume.ramiyer.me). I'll refer to this as `your.bucket.name`, going forward.
4. Select the region and select **Next**. Any region would do, since we're going to use CloudFront anyway. Pick the cheapest one, if you want.
5. In the screen that appears next, click on **Next**; if you want logging or versioning configured, you may want to select the relevant options.
6. In the next screen, under **Manage public permissions**, select **Grant public read access to this bucket** and click **Next**.
7. Review the settings and create the bucket.
8. Once the bucket is created, open the bucket and go to the **Permissions** tab. Click on **Bucket Policy**.
9. Enter the following JSON in the editor area:
``` JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:ListBucket",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::[your.bucket.name]",
                "arn:aws:s3:::[your.bucket.name]/*"
            ]
        }
    ]
}
```
10. Save the configuration.

That completes the bucket setup part.

#### Setup AWS Access and Secret keys

Wercker needs to authenticate itself to push content to the S3 bucket. We'll set up an IAM user for this.

1. Go to the [IAM console](https://console.aws.amazon.com/iam).
2. Go to the **Users** tab and click **Add user**.
3. Add a **User name**.
4. Select the box against **Programmatic access**, and proceed to add permissions.
5. In the permissions screen, select **Add existing policies directly**.
6. Search for **AmazonS3FullAccess**, select it, and proceed to review.
7. Click on **Create user**.
8. In the next screen, you'll get the **Access key ID** and **Secret key**. Note these down in a safe place.

#### Configuring Wercker to build and deploy the site to Amazon&nbsp;S3

This is a little tricky part, since we have outdated documentation surrounding it, on the Internet, for now. New users will have a hard time hunting around to get this information. The reason for this is that Wercker apparently changed the underlying system, by introducing Docker. This has brought in some changes to workflows, which obviously brings in subsequent UI and other changes.

1. Go to the [Wercker home page](https://app.wercker.com/).
2. Login with BitBucket.
3. Create a new application in Wercker by selecting the BitBucket repository where the code is stored.
4. At this point, it should ask you to add a `wercker.yml` file; it would give the code. Ignore that for now.
5. In your code repository on BitBucket, create a new file at the root, called `wercker.yml`.
6. Add the following code to it (careful with the spaces and indentation; YAML is a little fussy):
    ``` YAML
    box: ruby

    build:
        steps:
            - bundle-install
            - script:
                name: generate site
                code: bundle exec jekyll build --trace

    deploy:
        steps:
            - s3sync:
                key-id: $AWS_ACCESS_KEY_ID
                key-secret: $AWS_SECRET_ACCESS_KEY
                bucket-url: $AWS_BUCKET_URL
                source-dir: ./_site/
                opts: --acl-public
    ```
7. Back in the Wercker screen, you should have a link that says, 'I already have a wercker.yml, trigger a build now.' Go ahead and click that to start the pipeline. Ensure the `build` part completes successfully. The code would not be deployed to S3 just yet.
8. You should see a tab on the screen, called **Environment**. Click on it.
9. Add the Amazon Access key ID, the Secret key, and the bucket name here. Ensure to make both they keys **protected**. The key names should _exactly_ match the variable names in the `deploy` part of `wercker.yml` (without the leading `$`). Also, note the `s3://` before the bucket name. That's important, too.
    ```
    AWS_ACCESS_KEY_ID : AKIAJ3R2K6NQLKVKXIJV
    AWS_SECRET_ACCESS_KEY : MpyMJBqY3Gq+5Jx7xGjZkO8/CqURMUefXDA6o59o
    AWS_BUCKET_URL: s3://your.bucket.name
    ```
10. Now, go to the **Workflows** tab of the Wercker app screen.
11. In the **Pipelines** section, click on **Add new pipeline**.
12. In the **Name** and **YML Pipeline name** fields, enter `deploy`.
13. Click on **Create**.
14. Back in the **Workflows** screen, click the `+` next to `build`.
15. At the **Execute pipeline** dropdown, select `deploy`, and click **Add**.
16. Go ahead and make a push from your local repository after making a small change to the code. Come back to the **Runs** tab on Wercker, and you should see the build happen, immediately followed by deployment.
17. Once the build and deployment complete, refresh the S3 bucket to see the site contents appear!

#### Configuring CloudFront to deliver the site

Your site should be live already, and you can access it using the bucket's endpoint URL. To find that URL, you can open your bucket, go to the **Properties** tab, click on **Static website hosting**. The URL mentioned there is the endpoint. However, there are two reasons I chose to go with CloudFront as well.

1. CloudFront makes the content delivery fast, throughout the world. Granted, this is not an issue for small static sites, which brings us to the next point.
2. You can have an SSL certificate, issued by Amazon for free, tagged to your site to encrypt the connections to the site.

Let's first proceed with getting ourselves an SSL certificate. This is optional. I like to encrypt connections to my sites, so I chose to go with it.

1. Open the [AWS Certificate Manager](https://console.aws.amazon.com/acm/) console.
2. Click on **Request a certificate**.
3. Enter the domain (or subdomain) name, which should be the URL where you want to host the site, as well as the name of the bucket, `your.bucket.name`. The prerequisite here is that you have sufficient authority on your domain, including the ability to receive admin emails. An email would be sent to the admin address of the domain for verification.
4. Click on **Review and request**.
5. Go to the mailbox configured with the admin address of your domain. You should've received an email to approve the certificate request. Go ahead and approve it after verifying the details.
6. Refresh the ACM screen, and you should see the certificate that was just issued.

It's now time to set up CloudFront.

1. Go to the [CloudFront console](https://console.aws.amazon.com/cloudfront/).
2. Click on **Create distrubution**.
3. Click on **Get started** in the **Web** section.
4. Clicking in the **Origin Domain Name** field should drop down a few values, the S3 bucket you created for the site being one of them. Select it.
5. In the **Default cache behaviour settings** section, select the radio button against **Redirect HTTP to HTTPS**.
6. Scroll down to **Distribution settings**, and enter the name of your bucket (and the subdomain URL of your site), `your.bucket.name`, in the **Alternate domain names** field.
7. Select **Custom SSL certificate**, and select the certificate you just created, from the dropdown.
8. Enter `index.html` in the **Default Root Object** field.
9. Go ahead and click on **Create Distribution**.
10. Wait. The process of creating the distribution can take anywhere from ten minutes to a few hours.
11. Once the **Status** says `Deployed`, and **State** says `Enabled`, the CloudFront distribution should be live. Note down the **Domain name** value for the distribution.

#### Configuring DNS routing

Your site should now be accessible using the domain name (like `qdw3xburi4bfy.cloudfront.net`) as well. But we want it to be accessible at `your.bucket.name`. This takes some DNS configuration.

1. Log into the DNS console of your DNS provider (your domain provider in most cases).
2. Create a new CNAME record, with `your.bucket.name` in the **Name** field, and `qdw3xburi4bfy.cloudfront.net` in the **Points to** field.
3. Save the zone file.
4. Wait. DNS modifications may take between a few seconds and several hours to propagate.

#### Conclusion

That brings us to the end of the setup. The site should be all set to be accessed using `https://your.bucket.name`. The browser should show the secure connection indicator as well, with the certificate showing, **Verified by Amazon**.
