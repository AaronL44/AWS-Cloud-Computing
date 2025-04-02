# Creating a Custom Image (AMI)

## What is an image?
- An Amazon Machine Image (AMI) is a pre-configured template that includes an operating system, software, configurations, and sometimes data (like my MongoDB snapshot). They allow you to quickly launch new EC2 instances with the same setup as your exisiting one.

## How do they work?
- You create an AMI from an existing EC2 instance. This AMI can then be used to launch multiple identical instances without having to reinstall or configure everything manually. The MongoDB snapshot ensures that each new instance starts with pre-loaded data.
  - In this case, the custom image is built from a "snaposhot" of the database instance which contains the pre installed MongoDB resource.

## How AMI's are useful for businesses?
- Consistency: Ensures all servers have the same software and configurations.
- Faster Deployment: New instances are ready to use immediately, saving setup time.
- Scalability: Easily launch multiple identical instances as demand increases.
- Backup & Recovery: AMIs with snapshots can act as backups, allowing quick recovery from failures.
- Automation: Can be integrated with auto-scaling to dynamically add or remove instances.

## How to make one:

