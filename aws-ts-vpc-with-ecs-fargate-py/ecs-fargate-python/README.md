[![Deploy](https://get.pulumi.com/new/button.svg)](https://app.pulumi.com/new)

# NGINX on AWS ECS Fargate using Python with a vpc built in Typescript

### What Is This?

This example shows authoring Infrastructure as Code in `python` for ecs fargate with a [crosswalk vpc](https://www.pulumi.com/docs/guides/crosswalk/aws/vpc/) built in `typescript`.  It
provisions a full [Amazon Elastic Container Service (ECS) "Fargate"](https://aws.amazon.com/ecs) cluster and
related infrastructure, running a load-balanced NGINX web server accessible over the Internet on port 80.
This example is inspired by [Docker's Getting Started Tutorial](https://docs.docker.com/get-started/).

### Why would you do this?  
An example showing that you can easily infrastructure written in a different language than the one you are used to.  The vpc outputs from vpc-crosswalk-ts folder are used as inputs via [StackReference](https://www.pulumi.com/docs/intro/concepts/organizing-stacks-projects/#inter-stack-dependencies)

## Prerequisites

* [Install Pulumi](https://www.pulumi.com/docs/get-started/install/)
* [Configure Pulumi to Use AWS](https://www.pulumi.com/docs/intro/cloud-providers/aws/setup/) (if your AWS CLI is configured, no further changes are required)


### Mandatory AWS Pre-REQ: AWS Console Fix for Tags:

This is necessary so that the tags work properly in ecs
[Tagging your Amazon ECS resources](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-using-tags.html)

As per AWS:  `You must opt in to the new Amazon Resource Name (ARN) and resource identifier (ID) formats.`

This has to be done per region until AWS enables it as default across the board(April 1, 2021).

AWS Console -> Elastic Container Service ->  Account Settings -> 

BEFORE
```
Resource                My IAM user or role account settings 
Container Instance      Undefined
Service                 Undefined
Task                    Undefined
```

AFTER
```
Resource                My IAM user or role account settings 
Container Instance      Enabled
Service                 Enabled
Task                    Enabled
```

## Running the Example

 1. Initialize a new stack called: `ecs-fargate-dev` via [pulumi config](https://www.pulumi.com/docs/reference/cli/pulumi_config_set/). 
      ```
      $ pulumi stack init ecs-fargate-dev
      ```

2. Create a Python virtualenv, activate it, and install dependencies:

    This installs the dependent packages [needed](https://www.pulumi.com/docs/intro/concepts/how-pulumi-works/) for our Pulumi program.

    ```bash
    $ python3 -m venv venv
    $ venv/bin/python -m pip install --upgrade pip setuptools wheel
    $ venv/bin/python -m pip install -r requirements.txt
    ```

2. View the current config settings. This will be empty.
   ```
   $ pulumi config
   ```
   ```
   KEY                     VALUE
   ```

4. Populate the config.

   Here are aws [endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)

   Note: the mystackpath is the key.  The value for it will be your `stackreference` from the vpc:

   e.g.:  `team-qa/crosswalk-vpc/vpc-fargate-dev`
   ```
   $ pulumi config set aws:region us-east-2 # must match vpc region
   $ pulumi config set config set mystackpath shaht/crosswalk-vpc/vpc-fargate
   ```
   
4. View the current config settings
   ```$ pulumi config```
   ```
   KEY                     VALUE
   aws:region           us-east-2
   mystackpath          shaht/crosswalk-vpc/vpc-fargate
   ```

5. Launch
 ```$ pulumi up```

6. Pulumi Console to view everything.  Note, you will have a url that shows up that will look similar to the url below.  The `shaht` will be replaced with your own org, for example if your org name is: `team-qa`:

   console view that matches above code as an example: 

   https://app.pulumi.com/`shaht`/fargate-with-crosswalk-vpc/ecs-fargate-dev/

   console view with YOUR ORG NAME:

   https://app.pulumi.com/`team-qa`/fargate-with-crosswalk-vpc/ecs-fargate-dev/

7.  View the outputs

```$ pulumi stack output```

```
   Current stack outputs (2):
    OUTPUT             VALUE
    ECS Cluster Tags   {"Name":"pulumi-fargate-ecs-cluster","application":"fargate","costcenter":"1234","crosswalk-vpc":"yes","demo":"yes","env":"dev","pulumi:Config":"Pulumi.ecs-fargate-dev.yaml","pulumi:project":"fargate-with-crosswalk-vpc","pulumi:stack":"ecs-fargate-dev","vpc_cidr":"10.0.0.0/24","vpc_name":"vpc-fargate-dev"}

    Load Balancer URL  pulumi-fargate-alb-7467631-1452059497.us-east-2.elb.amazonaws.com
   ```

7. Cleanup.
   ```
   $ pulumi destroy
   $ pulumi rm ecs-fargate-dev
   ```