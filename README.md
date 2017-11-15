# Examples of how cfn_nag works

This repository contains several CloudFormation templates that demonstrate the various capabilties of cfn_nag, a static analyzer of CloudFormation templates. It will look at your CloudFormation template files, and look for known anti-patterns that are best avoided.

### Install cfn-nag

cfn_nag is a ruby gem. [Assuming you have Ruby installed already](https://www.ruby-lang.org/en/documentation/installation/), installing cfn_nag is a snap:

    gem install cfn-nag

### Simple example: Encrypted EBS Volumes

Encrypted EBS volumes are an excellent example of something cfn_nag will help enforce. There's really no reason you _don't_ want your EBS volumes encrypted, so if cfn_nag detects you are provisioning an unencrypted EBS volume, it'll issue and error.

    cfn_nag_scan --input-path volume.yml
    
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

    cfn_nag_scan --input-path volume-encrypted.yml

    ------------------------------------------------------------
    volume-encrypted.yml
    ------------------------------------------------------------
    Failures count: 0
    Warnings count: 0


