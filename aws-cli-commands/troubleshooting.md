-----------------------------------------------------------------------------------------------------------------------------

# Wrong Access key
Issue: I had configured wrong access key by mistake 
Errors: It said "Security token included is invalid"
I assumed wrong region is setup but then it matched in CLI and mgmt. console also, then i realized wrong access key was setup

#Installed Apache webserver in EC2 private-ip
Usecases: It can only be accessed by other Ec2 or ECS within the same VPC
	   Not available publicly
Solution: Terminated it to avoid unnecessary costs

-----------------------------------------------------------------------------------------------------------------------------

#Installed in public-ip, but says 'Took too long to respond' when public ip is opened.
Steps: 1. check the status using sudo systemctl status httpd
	  If active(running)
	2. Check Security group rules
		httpd and ssh enabled
Reason: When clicked on 'open address' it was directing https by default which is not added in SG of this EC2.

-----------------------------------------------------------------------------------------------------------------------------

#Access denied for CF
- created new origin with OAI
- updated AWS account ID removing _ in between
- Creating OAI gave a policy and updated this in S3
- CF didnot allow me to delete the old origin, 
	under CF - behaviors tab, update the origin to new origin created and then delete the old origin 
- Still got 'Access Denied' for CF url,
	Go to CF-Invalidations, create new /* and invalidate

With CF domain url - 'Access Denied'
But with CF url/index.html --> Page was loading

Fix: UPdate Cloudfront- General- 'Default root object' to index.html


------------ Below didnot make any difference --------------------------------------------------------------------------
Also in S3-Permissions,
	Only check below 2,
		-Block public access to buckets and objects granted through new access control lists (ACLs)
		- Block public access to buckets and objects granted through new public bucket or access point policies


-----------------------------------------------------------------------------------------------------------------------------






