Step 1 : Create S3- bucket in aws mgmt console

Enable static website, uncheck public access restriction, update bucket policy
Upload index.html , error.html
**Bucket policy to give public access

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::s3-static-websitebkt/*"
    }
  ]
}

 Test website url under 'properties' tab.


Step2 : Create CF distribution for CDN and HTTPS

* Create CF in mgmt console, give s3 as origin, select 'Redirect HTTP - HTTPS'
* update S3 bucket policy to allow CF to access S3
* By default, CloudFront cannot access your S3 bucket unless explicitly allowed. This policy:
	Allows only CloudFront to read your files
	Prevents direct public access (better security)


Replace above policy with CF access as below,

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::YOUR_AWS_ACCOUNT_ID:distribution/your-cloudfront-id"
        }
      }
    }
  ]
}


	* Update Bucket Permissions in S3 for CloudFront(to access s3)
	* Create Invalidations (if required)
	* Update default root object to "index.html"


Step 3: Setup route 53 for your domain

Route 53 : Gives DNS name (abc.com)
	    Does health check and routes requests to healthy resources
            Has hosted zones, to manage DNS records


STEPS:
1. Create Route53- hosted zone
Enter Domain name, select public hosted zone - create
Route 53 generate 4 NS(name server) records and SOA(Start of Authority)

2. Goto your Domain providers website
Find DNS or Name server settings and replace existing NS with route53 NS records and SOA
Now your domain is managed by route53.


Different Record types:
For hosting a website → A, CNAME, ALIAS
For CloudFront/S3/ELB → ALIAS
For email setup → MX, TXT
For domain verification → TXT
For DNS delegation → NS


A	Maps domain to IPv4				example.com → 192.0.2.44
AAAA	Maps domain to IPv6				example.com → 2001:db8::ff00:42:8329
CNAME	Alias domain to another domain			www.example.com → example.com
ALIAS	AWS-specific alias (root domain support)	example.com → CloudFront
MX	Email routing					example.com → mail.google.com
TXT	Store metadata (SPF, DKIM, verification)	example.com → "v=spf1 include:_spf.google.com"
NS	Points domain to name servers			example.com → ns-123.awsdns-45.net
SRV	Service-specific records			VoIP/SIP settings
PTR	Reverse DNS lookup				192.0.2.44 → example.com
SOA	DNS settings & admin info			Primary NS: ns-123.awsdns-45.net

Price: New Register of Domain name - ~12/year
	DNS - 0.5$/month

New Register Name: Buying a website name (like we do from GoDaddy, NAmecheap etc)
DNS : You already have a register name (Can be from AWS Route 53 or other DNS provider) , AWS manages DNS records for a domain (in either cases) 





Step 4: Secure website with HTTPS (Using ACM- AWS Certificate Manager)

AWS Certificate Manager (ACM) provides free SSL/TLS certificates to secure your website with HTTPS. 
Since CloudFront only supports ACM certificates in us-east-1, we'll request the certificate in that region.

STEPS:
COnsole - ACM - Request a certificate
Enter domain name
CLick DNS validation - Request
Create Record in Route 53 (If you have Route 53 already)
ACM will add CNAME record in Route53	
Wait for ACM to validate the certificate
Now your domain name is setup in Route 53 and secured with SSL
Point domain to CloudFront (Using A Record)

Note: Having Route53 setup already only makes validation step(DNS) faster and easier
	If Route53 is not there, you can still request Certificate, ACM will issue CNAME Record
	You can go and add this manually in DNS Provider (Godaddy)
	Once you add , AWS will validate the certificate

Once ACM shows Certificate as 'Issued' , take and upload it to CloudFront.
Incase using Route 53, Point Domain name to CF using A record (IP address of CF)


If we want Certificate from ACM - we need to have domain name!!!
Because we need to validate the certificate (using AWS or using DNS provider websites) saying we own the website, 
otherwise ACM will not issue the certificate

If we are using CF, it provides HTTPS by default but we should always use ssfdfdf.cloudfront.net url

If we want HTTPS for our custom domain or use SSL with AWS services like ALB, API GAteway etc we need ACM 
ACM secures ALB and API Gateway also



