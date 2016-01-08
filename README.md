# cloudformation-docker-registry
[AWS CloudFormation](https://aws.amazon.com/cloudformation/) template to deploy a secure [Docker registry](https://github.com/docker/distribution) with a [Redis](https://hub.docker.com/_/redis/) cache and [web frontend](https://github.com/kwk/docker-registry-frontend)

## Description
This CloudFormation template deploys a [CoreOS](https://coreos.com/os/docs/latest/booting-on-ec2.html) instance with three system units to run the docker registry with a redis cache and web frontend. The template also creates IAM roles so that the instance can access an s3 bucket to use as a storage backend.

The instance is put behind an ELB with an HTTPS and HTTP listener, you can configure through template parameters which IP ranges to allow access to the ELB. If you want other EC2 instances to be able to access this registry, then setup the ELB security group to allow https ingress from the security groups which contain your EC2 instances.

This template does not create a Route53 record as this is generally easier to do manually. Take the ELB DNS adress from the template's outputs and create a CNAME record set pointing to this address.

## Usage

**This template assumes you already have an SSH key pair, an s3 bucket, and an SSL certificate created/uploaded to your AWS account**

* Upload the template to s3
* Go to the CloudFormation service dash and click `Create Stack`
* paste the s3 link to the upload template into the `Specify an Amazon S3 template URL` box
* Fill out the Stack name and parameter fields as per the instructions below
* Click through to the `Review` page and double check your stack details
* Tick the capabilities box that will allow the template to create IAM resources
* Click `Create` !
* Once the stack reaches state: `CREATE_COMPLETE` you can create the appropriate Route53 record set

## Parameters

* **TrustedIpBlock** - The CIDR IP block to allow ssh connections from (ssh user is "core")
* **HttpsIpBlock** - The CIDR IP block to allow incoming registry requests from. As there is no auth on this server, this range should be locked down to a trusted range.
* **VPC** - The AWS Virtual Private Cloud to deploy this registry to
* **Subnet** - The Subnet within the VPC to deploy the registry to
* **Key** - The SSH key pair to use for accessing the instance
* **InstanceType** - t2.micro, t2.small, or t2.medium
* **ElbCertPath** - The path and name of your SSL certificate. This is used to build the cert's ARN which looks like: `arn:aws:iam::<account-id>:server-certificate/yourpath/your_certname` The path by default is just `/` so unless you specified otherwise, this parameter should look like `/your_certname`
* **S3Bucket** - The pre-existing s3 bucket which the register instance will be given access to
* **S3BucketRegion** - The [region](http://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in which your s3 bucket is located

## Contributing

Pull requests are welcome. Consider creating an issue to discuss the feature before doing the development work, or just fork and create a pull request.
