# Examples of how cfn_nag works

This repository contains several CloudFormation templates that demonstrate the various capabilties of cfn_nag, a static analyzer of CloudFormation templates. It will look at your CloudFormation template files, and look for known anti-patterns that are best avoided.

### Install cfn-nag

cfn_nag is a ruby gem. [Assuming you have Ruby installed already](https://www.ruby-lang.org/en/documentation/installation/), installing cfn_nag is a snap:

    gem install cfn-nag

### Simple example: Encrypted EBS Volumes

Encrypted EBS volumes are an excellent example of something cfn_nag will help enforce. There's really no reason you _don't_ want your EBS volumes encrypted, so if cfn_nag detects you are provisioning an unencrypted EBS volume, it'll issue and error.

    cfn_nag_scan --input-path cfn/volume.yml
    
    ------------------------------------------------------------
    volume.yml
    ------------------------------------------------------------------------------------------------------------------------
    | FAIL F1
    |
    | Resources: ["EBSVolume"]
    |
    | EBS volume should have server-side encryption enabled

    Failures count: 1
    Warnings count: 0

However, if we encrypt the volume, cfn_nag succeeds:     

    cfn_nag_scan --input-path cfn/volume-encrypted.yml

    ------------------------------------------------------------
    volume-encrypted.yml
    ------------------------------------------------------------
    Failures count: 0
    Warnings count: 0


### Less simple example: Warnings vs Errors

cfn_nag will complain in two different ways: it will issue **warnings** for patterns that are _probably_ a bad idea, and will issue **errors** for patterns that are _definitely_ a bad idea.

For example, let's look at a tempalte for a EC2 instance, with an IAM role attached, behind an ELB:

    cfn_nag_scan --input-path cfn/stack.yml
    
    ------------------------------------------------------------
    cfn/stack.yml
    ------------------------------------------------------------------------------------------------------------------------
    | FAIL F1
    |
    | Resources: ["EBSVolume"]
    |
    | EBS volume should have server-side encryption enabled
    ------------------------------------------------------------
    | WARN W26
    |
    | Resources: ["LoadBalancer"]
    |
    | Elastic Load Balancer should have access logging enabled
    ------------------------------------------------------------
    | FAIL F3
    |
    | Resources: ["InstanceRole"]
    |
    | IAM role should not allow * action on its permissions policy
    ------------------------------------------------------------
    | WARN W11
    |
    | Resources: ["InstanceRole"]
    |
    | IAM role should not allow * resource on its permissions policy
    ------------------------------------------------------------
    | WARN W5
    |
    | Resources: ["EC2SecurityGroup"]
    |
    | Security Groups found with cidr open to world on egress
    ------------------------------------------------------------
    | WARN W29
    |
    | Resources: ["EC2SecurityGroup"]
    |
    | Security Groups found egress with port range instead of just a single port

    Failures count: 2
    Warnings count: 4
    
When used as part of your continuous delivery pipeline, **failures** will cause cfn_nag to return a non-zero error code, which will fail your build.  However, **warnings** will just be printed out, and cfn_nag will exit successfully so your build can continue. 

If the failures are fixed, the build can continue:

