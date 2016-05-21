
# Amazon S3 Secure Static Site Hosting
Secure static website hosting with AWS

![image](https://cloud.githubusercontent.com/assets/2770290/15445976/ad175a64-1f52-11e6-82e4-420d809283e3.png)  


1. run cloudformation (~15 mins)
2. update NS records from Route 53 in your domain registrar control panel (give it some time to propagate, I use namecheap, usual it takes no longer than a minute), sometime you may have a timeout
3. upload site content
4. run sh script 2 times, e.g.
    ./my.sh larawhale.com vitdotonline@gmail.com
    ./my.sh www.larawhale.com vitdotonline@gmail.com

5. update CDNs (manual(()))
    General set freshly created certificate and behavior



I'd recommend to format cloudformation.json with aws web based editor.
It formats json, sorts fields, adds spaces, etc. I spotted a few errors in my code using it.
Usually I use Atom

cloudformation.json creates:
1. 2 S3 buckets yourdomain.com and www.yourdomain.com
2. 2 CloudFront distributions for yourdomain.com and www.yourdomain.com

change S3 origin to custom, then we can control the cache!!!

3. a hosted zone for yourdomain.com (this is 50c/month)
4. 2 record sets of A type, they point yourdomain.com and www.yourdomain.com to CloudFront distribution domains, which look like d2something78u.cloudfront.net

check the output after successful creation

CDN	E2UTA0ZEJ5VK7P	CloudFront CDN
CDNWWW	E1OMXFD4UUYIJX	CloudFront CDN for www
HostedZone	Z2KT8W0UB16OZF	Dont' forget to change NS records with your domain registrar
BucketName	vitr.website	Name of S3 bucket to hold website content

go to Route53 in hosted zone for your domain and get the NS records, e.g.
ns-1190.awsdns-20.org.
ns-1816.awsdns-35.co.uk.
ns-321.awsdns-40.com.
ns-890.awsdns-47.net.
yours will be different on every run
set those NS records in the domain registrar control panel.

Your site is up now, but you won't see anything until you upload index.html to the bucket. You can upload the content of 'coming-soon-site' folder form this repo.

To avoid content duplication the bucket access is restricted to CloudFront agent, you can't access the bucket directly, e.g. in your browser. read more Avoiding the Duplicate Content Penalty with AWS S3 and CloudFront   
https://bryce.fisher-fleig.org/blog/avoiding-duplicate-content-penalty-with-aws-s3-and-cloudfront/


Now let's make your site secure with Letsencrypt.
https://vittegleo.com/blog/letsencrypt-lambda-function/
but we need two of them, one




use lego
https://github.com/xenolf/lego
https://disjoint.ca/til/2016/03/26/lets-encrypt-tls-certificates-using-route53-dns-verification/



1. Run the CloudFormation template with your domain name
2. Confirm 2 ACM Certificates (usually I get the emails in 3-4 minutes after the running)
3. Change NS records on your domain registrar
4. Upload content to S3 bucket. No time for this? Use 'coming soon' html page from this repo
I set `"MinTTL": 0` but it still takes a minute to see the content uploaded to S3.
http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html#DownloadDistValuesMinTTL

running CloudFormation may take time (up to ~30 mins) because of CloudFront distribution.

The price is 50c a month for 1 domain on amazon free tier. this is the price of 1 hosted zone in Route53


GOTCHAS
http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-route53-aliastarget.html?shortFooter=true
HostedZoneId
The hosted zone ID. For load balancers, use the canonical hosted zone ID of the load balancer. For Amazon S3, use the hosted zone ID for your bucket's website endpoint. For CloudFront, use **Z2FDTNDATAQYW2**. For examples, see Example: Creating Alias Resource Record Sets in the Amazon Route 53 API Reference.

Type: String

Required: Yes

Dont' forget to change NS records with your domain registrar
 http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/SOA-NSrecords.html


 Something went wrong
 Error: you have reached your limit of 10 certificates in the last year.


- [ ] Add favicon.ico & favicon.png
- [x] Add domain to Route53
- [x] Create SSL/TLS Certificate (requires manual approval via email)
-  ~~Create S3 bucket (don't need www bucket)~~
- [ ] Upload content & set permissions
- [ ] Update NS records (Route53) (custom domain for s3 site hosting)
- [ ] Create CloudFront distribution (huge delay) don't forget to specify the alternate Domain Names (CNAMEs) both non www and www domain names
- [ ] Change NS record to CloudFront (for both www and non www names)
- [ ] Create Lambda function to create origin access identities - disable public access to the bucket to avoid content duplication
- [x] Gzip compression
- [x] run lego from phyton docker container

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


```
echo cd /root/.aws >> ~/.bashrc

DOMAIN="larawhale.com"

aws route53 create-hosted-zone --name $DOMAIN --caller-reference "`date`" --hosted-zone-config Comment="created by amazon-s4-hosting"

ZONE="ZXMGFL45UP69N"

aws route53 change-resource-record-sets --hosted-zone-id $ZONE --change-batch file://resource-record-sets.json


aws acm request-certificate --domain-name larawhale.com

aws s3api create-bucket --bucket $DOMAIN

```

Impossible to create with CloudFormation  
- origin access identities (for CloudFront)
- SSL Certificates

The last one has been hacked thanks to Ryan Brown (@ryansb)
https://github.com/ryansb/acm-certs-cloudformation
https://serverlesscode.com/post/acm-certificates-in-cloudformation/
I'm actually using his lambda functions (https://serverlesscode.com/examples/2016-02-acm-cloudfront-cdn.json) in my CloudFormation template.
this is actually a big deal
http://stackoverflow.com/questions/35914161/how-to-provision-a-cloudfront-distribution-with-an-acm-certificate-using-cloud-f
someone issued 500 bounty on SO



---   



Step 6 (Optional): Speed Up Your Website by Using CloudFront  
http://docs.aws.amazon.com/gettingstarted/latest/swh/getting-started-create-cfdist.html

Cloudfront custom-origin distribution returns 502 “ERROR The request could not be satisfied.” for some URLs  
http://stackoverflow.com/questions/20664018/cloudfront-custom-origin-distribution-returns-502-error-the-request-could-not-b


-------------------
include this image on the top, aka banner

images
https://cdn-images-1.medium.com/max/800/1*on4Cdx8sK80tylZhc5ZCPw.jpeg



### Roadmap
create a web interface for creating and saving Lambda functions with any domains/distributions
you can use cloudformation for this but anyway it doesn't support the schedule.

-------------------------------
more idea
https://www.namecheap.com/support/api/intro.aspx

https://hub.docker.com/r/anigeo/awscli/
try to use this on docker images
https://github.com/dlapiduz/letsencrypt-s3front


add cloudfront updates for new created certificates



try also
https://github.com/mindstorms6/letsencrypt-route53


this wizard is something...
https://github.com/ubergeek42/lambda-letsencrypt
I don't like interactive mode, to much manual work



that fucking lambda function doesn't work at all
https://vittegleo.com/blog/letsencrypt-lambda-function/



issues, suggestions and help are very welcomed))
