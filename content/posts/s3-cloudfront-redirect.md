+++
title = "Amazon S3 and CloudFront redirect without a double redirect."
description = ""
author = "Ben George"
type = ["posts","post"]
tags = [
    "AWS",
    "S3",
    "Cloudfront",
    "redirect",
    "HTTPS",
]
date = "2019-10-20"
categories = [
    "Development",
    "aws",
]

+++

## HTTP to HTTPS and WWW to non www root domain

Performing redirects for a static site in AWS the right way is a little tricky, in this post I’m going to go through the steps to redirect a non secure HTTP and www address (e.g. http://www.bengeorge.me) to a secure HTTPS root domain (e.g. https://bengeorge.me) for a static site stored in S3 and delivered through CloudFront.


## Is my redirect performing as expected?


In terminal run the following command: 

    curl -I http://www.example.com

Note that both the HTTP to HTTPS redirect and WWW to root are expected.

The intended behavior is: ‘HTTP/1.1 301 Moved Permanently’
However we are getting ‘HTTP/2 200’.

This is because of the default S3 behavior, a request served via the usual S3 endpoint will result in a response with the ‘x-amz-website-redirect-location header’, while the S3 website endpoint returns a HTTP redirect.

Is the redirect coming from cloudfront:
The intended behavior is:: ‘X-Cache: Redirect from cloudfront’
However we are getting ‘x-cache: Miss from cloudfront’.

On S3 Restrict bucket access to cloudfront in both the www and root buckets:

![s3-buckets](/posts/post-s3-cloudfront-redirect/s3-buckets.png)

Add the following policy: (Note the specific domain arn for each bucket).
First create an origin access identity in cloud front and use that id.

    {
        "Version": "2008-10-17",
        "Id": "PolicyForCloudFrontPrivateContent",
        "Statement": [
            {
                "Sid": "1",
                "Effect": "Allow",
                "Principal": {
                    "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity xx12345678xx"
                },
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::example.com/*"
            }
        ]
    }

![s3-bucket-policy](/posts/post-s3-cloudfront-redirect/s3-bucket-policy.png)


