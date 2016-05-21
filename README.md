# Amazon S3 Secure Static Site (S4) Hosting [![Analytics](https://vitr-analytics.appspot.com/UA-75628680-1/amazon-s4-hosting?flat&useReferer)](https://github.com/vitr/google-analytics-beacon)

![image](https://cloud.githubusercontent.com/assets/2770290/15445976/ad175a64-1f52-11e6-82e4-420d809283e3.png)  
You bought a domain, but have no hosting solution yet. This project might help you to set up an affordable AWS hosting for your site in about 30 minutes. Here is what you get in the end:
 - secured static website available on https://yourdomain.com
 - Hosted DNS zone for yourdomain.com in Route 53
 - S3 bucket with your content
 - CDN with CloudFront
 - SSL certificate with Letsencrypt or Amazon

On Amazon's free tier it's gonna cost you **50c a month** (the price for one Hosted zone) and a bit more (depends on how much traffic do you have) if you're not on free tier anymore.

### Quick Step-by-step Instructions

1. Run cloudformation (~15 mins)
2. Update NS records from Route 53 in your domain registrar control panel (give it some time to propagate, I use namecheap, usually it takes no longer than a minute), sometime you may have a timeout
3. Upload site content
4. Get the SSL certificates  
  a) Run sh script 2 times, e.g.  
    `./letsencrypt.sh yourdomain.com youremail@domain.com`  
    `./letsencrypt.sh www.yourdomain.com youremail@domain.com`  
  or  
  b) Use Amazon Certificate Manager
5. Update CDNs (manual), set freshly created certificates



### Process Explanation in Details

cloudformation.json creates:
1. 2 S3 buckets yourdomain.com and www.yourdomain.com
2. 2 CloudFront distributions for yourdomain.com and www.yourdomain.com



3. a hosted zone for yourdomain.com (this is 50c/month)
4. 2 record sets of A type, they point yourdomain.com and www.yourdomain.com to CloudFront distribution domains, which look like d2something78u.cloudfront.net

check the output after successful creation, it will show you 4 important items, that you will need work with.

CDN	E2UTA0ZEJ5VK7P	CloudFront CDN
CDNWWW	E1OMXFD4UUYIJX	CloudFront CDN for www
HostedZone	Z2KT8W0UB16OZF	Dont' forget to change NS records with your domain registrar
BucketName	yourdomain.com	Name of S3 bucket to hold website content

go to Route53 in hosted zone for your domain and get the NS records, e.g.
ns-1190.awsdns-20.org.
ns-1816.awsdns-35.co.uk.
ns-321.awsdns-40.com.
ns-890.awsdns-47.net.
yours will be different on every run
set those NS records in the domain registrar control panel.

Your site is up now, but you won't see anything until you upload index.html to the bucket. You can upload the content of 'coming-soon-site' folder form this repo.



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



Impossible to create with CloudFormation  
- origin access identities (for CloudFront)
- SSL Certificates

More tips and gotchas you can find in [Extra Tips](/extra-tips/README.md)

### Roadmap
- [ ] add letsencrypt.sh
- [ ] Docker image/compose for running aws cli, lego and bash scripts
- [ ] Automated test with [CasperJS](https://github.com/vitr/docker-casperjs)
- [ ] Create SSL/TLS Certificate with Amazon Certificate Manager(requires manual approval via email)
- [ ] Upload content & set permissions
- [ ] Update NS records (Route53) (custom domain for s3 site hosting)
- [ ] Create CloudFront distribution (huge delay) don't forget to specify the alternate Domain Names (CNAMEs) both non www and www domain names
- [ ] Change NS record to CloudFront (for both www and non www names)
- [ ] Create Lambda function to create origin access identities - disable public access to the bucket to avoid content duplication
- [ ] run lego from phyton docker container
- [ ] create a web interface for creating and saving Lambda functions with any domains/distributions you can use cloudformation for this but anyway it doesn't support the schedule.
- [ ] Namecheap API https://www.namecheap.com/support/api/intro.aspx
- [ ] add cloudfront updates for newly created certificates

### Support
Issues, suggestions and help are very welcomed))
