
# Amazon S3 Secure Static Site (S4) Hosting

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



Dont' forget to change NS records with your domain registrar
 http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/SOA-NSrecords.html




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



Impossible to create with CloudFormation  
- origin access identities (for CloudFront)
- SSL Certificates



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






issues, suggestions and help are very welcomed))
