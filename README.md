# Secure S3 Static Site Hosting
Secure static website hosting with AWS


- [ ] Add domain to Route53
- [ ] Create SSL/TLS Certificate (requires manual approval via email)
- [ ] Create S3 bucket (don't need www bucket)
- [ ] Upload content & set permissions
- [ ] Update NS records (Route53) (custom domain for s3 site hosting)
- [ ] Create CloudFront distribution (huge delay) don't forget to specify the alternate Domain Names (CNAMEs) both non www and www domain names
- [ ] Change NS record to CloudFront (for both www and non www names)


```
DOMAIN="larawhale.com"
aws route53 create-hosted-zone --name $DOMAIN --caller-reference "`date`" --hosted-zone-config Comment="created by amazon-s4-hosting"


```



---   



Step 6 (Optional): Speed Up Your Website by Using CloudFront  
http://docs.aws.amazon.com/gettingstarted/latest/swh/getting-started-create-cfdist.html

Cloudfront custom-origin distribution returns 502 “ERROR The request could not be satisfied.” for some URLs  
http://stackoverflow.com/questions/20664018/cloudfront-custom-origin-distribution-returns-502-error-the-request-could-not-b

Avoiding the Duplicate Content Penalty with AWS S3 and CloudFront   
https://bryce.fisher-fleig.org/blog/avoiding-duplicate-content-penalty-with-aws-s3-and-cloudfront/
