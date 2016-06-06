
# Amazon S4 Hosting Extra Tips

To avoid content duplication the bucket access is restricted to CloudFront agent, you can't access the bucket directly, e.g. in your browser. read more Avoiding the Duplicate Content Penalty with AWS S3 and CloudFront  (this is actually debatable, as Google is smart enough nowadays, I wouldn't worry about this if you don't promote direct bucket link anywhere)
https://bryce.fisher-fleig.org/blog/avoiding-duplicate-content-penalty-with-aws-s3-and-cloudfront/

- [x] Gzip compression handled by cloud front

I'd recommend to format cloudformation.json with aws web based editor.
It formats json, sorts fields, adds spaces, etc. I spotted a few errors in my code using it.
Usually I use Atom

change S3 origin to custom, then we can control the cache!!!

#### Cache headers on S3
add the header for all items in the bucket

    aws s3 cp s3://your.bucket/ s3://your.bucket/ --recursive --metadata-directive REPLACE --cache-control max-age=604800

GOTCHAS
http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-route53-aliastarget.html?shortFooter=true
HostedZoneId
The hosted zone ID. For load balancers, use the canonical hosted zone ID of the load balancer. For Amazon S3, use the hosted zone ID for your bucket's website endpoint. For CloudFront, use **Z2FDTNDATAQYW2**. For examples, see Example: Creating Alias Resource Record Sets in the Amazon Route 53 API Reference.

Type: String

Required: Yes

---

Something went wrong
Error: you have reached your limit of 10 certificates in the last year.
=============================================
AWS Fucking LIMITS!!!!
I would setup a certificate at once for all my domains.....
==============================================

Sample S3 bucket policy
```
{
  "Version":"2008-10-17",
  "Statement":[{
    "Sid":"PublicReadGetObject",
        "Effect":"Allow",
      "Principal": {
            "AWS": "*"
         },
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::yourdomain.com/*"
      ]
    }
  ]
}
```

The last one has been hacked thanks to Ryan Brown (@ryansb)
https://github.com/ryansb/acm-certs-cloudformation
https://serverlesscode.com/post/acm-certificates-in-cloudformation/
I'm actually using his lambda functions (https://serverlesscode.com/examples/2016-02-acm-cloudfront-cdn.json) in my CloudFormation template.
this is actually a big deal
http://stackoverflow.com/questions/35914161/how-to-provision-a-cloudfront-distribution-with-an-acm-certificate-using-cloud-f
someone issued 500 bounty on SO

---   


try also
https://github.com/mindstorms6/letsencrypt-route53


this wizard is something...
https://github.com/ubergeek42/lambda-letsencrypt
I don't like interactive mode, to much manual work



that fucking lambda function doesn't work at all
https://vittegleo.com/blog/letsencrypt-lambda-function/
