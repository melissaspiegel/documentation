[MWM](README.md) > MWM Deployment Pipeline

# MWM Deployment Pipeline

This document will walk through our deployment pipeline and how to add MWM to other sites.

When pushing to Staging or Development branches, MWM triggers a Gitlab Pipeline.  This pipeline is responsible for the following tasks:

* Build MWM Assets
* Deploy Assets to S3
* Create a Zip Package
* Deploy plugin to Sites defined in .gitlab-ci.yml
    * Deployment can either be via IPI (Sample Plugin Insatller) or SSH
    
## To add MWM to a new site

1. Update .gitlab-ci.yml file, adding a new site to the list
- Example: 
```yaml
Deploy to <Site Name>:
    extends: .deploy_via_ipi
    variables:
        PROD_NAME: <WPEngine Production Name>
        STAGING_NAME: <WPEngine Staging Name>

# Each site must extend either .deploy_via_ipi or .deploy_via_ssh  (most sites should be able to use IPI)
```
2. If using ssh, you need to make sure the jenkins user is added to WPEngine
    - _Note: This user is normally set up during the initial site setup_
    - Add the 'WDev-Hacvk' user as a git user in WPEngine

## Check the status of the Gitlab Pipeline

1. Visit: https://code.corp.sample.com/wpdev/marketing-web-modules/-/pipelines
2. You should see something similar to the below:
![Gitlab Pipeline Image](images/swm-gitlab-ci.png)
    
