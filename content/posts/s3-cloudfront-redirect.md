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

Note that the WWW to root redirect is expected here and HTTP to HTTPS is then handled at the root distribution.


The intended behavior is: 

    HTTP/1.1 301 Moved Permanently
    Server: AmazonS3
    Location: http://bengeorge.me/
    X-Cache: Hit from cloudfront

However we are getting:

    HTTP/1.1 301 Moved Permanently
    Server: CloudFront
    Location: https://www.bengeorge.me/
    X-Cache: Redirect from cloudfront

This is because of the default S3 behavior, a request served via the usual S3 endpoint will result in a response with the following header:

    x-amz-website-redirect-location

While the S3 website endpoint returns a HTTP redirect.

Is the redirect coming from cloudfront:
The intended behavior is:

    X-Cache: Redirect from cloudfront
However we are getting:

    x-cache: Miss from cloudfront

## Ok let's fix this

To stop S3 serving content directly restrict bucket access only to cloudfront in both the www and root buckets:

Add the following policy: (Note the specific domain arn for each bucket) and cloudfront origin access ID.

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

You can check in S3 to see if the buckets are public:
![s3-buckets-non-public](/posts/post-s3-cloudfront-redirect/s3-buckets-non-public.png)

Create an origin access identity in cloudfront and use that ID in the Origin settings.

![cloudfront-origin-domain-identity](/posts/post-s3-cloudfront-redirect/cloudfront-origin-domain-identity.png)

![s3-bucket-policy](/posts/post-s3-cloudfront-redirect/s3-bucket-policy.png)

Now the quite fiddly non obvious step is not using the regular S3 URI but the "s3-website" one. For the WWW origin settings, change the origin to mybucketname.s3-website-us-east-1.amazonaws.com instead of using the bucket directly.

![s3-bucket-origin](/posts/post-s3-cloudfront-redirect/s3-bucket-origin.png)


## Testing

In terminal run the following command:
    
    curl -I http://www.example.com

![terminal-301-moved-permanently](/posts/post-s3-cloudfront-redirect/terminal-301-moved-permanently.png)

And check the hit on the root: 

    curl -I https://example.com

![terminal-200-redirect-cloudfront-hit](/posts/post-s3-cloudfront-redirect/terminal-200-redirect-cloudfront-hit.png)


