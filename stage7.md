Cleanup!

This last step cleans up the project demonstration and returns the AWS account to one without billable resources.

First, click on Load Balancers in the EC2 console and delete the ELB. Select the CIS194WordPressALB, click actions, and select Delete. Confirm.

Click Target Groups, and select the target group created earlier, click Actions, and then Delete. Confirm.

Delete the Auto Scaling group.

steps herein.

Delete the EFS file system.

these are the steps.

Delete RDS from the RDS console.

Check that EFS and all the EC2 instances, and the ASG have finished deleting. Wait until they're gone. poof!

Delete the Launch Template. In the EC2 console, Click on Launch Templates, select WordPress, click Actions, and then Delete. Confirm.

Remove the RDS snapshot. In the RDS console, click on the snapshot, click Actions, and then Delete. Confirm.

The last step is to go to the Cloudformation console, select the CIS194 VPC stack, click Delete, and confirm by clicking on Delete stack.

This is the end of the Cleanup phase.
