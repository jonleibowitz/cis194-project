EFS Setup

Run the following to install the Amazon EFS utilities:

```
sudo yum install -y amazon-efs-utils
```

Next run the following to migrate media content into EFS. Copy the content to a temporary location:

```
cd /var/www/html
sudo mv wp-content/ /tmp
sudo mkdir wp-content
```

Then get the EFS file system ID from parameter store:

```
EFSFSID=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/EFSFSID --query Parameters[0].Value)
EFSFSID=`echo $EFSFSID | sed -e 's/^"//' -e 's/"$//'`
```

Edit the /etc/fstab to mount as /var/www/html/wp-content/:

```
echo -e "$EFSFSID:/ /var/www/html/wp-content efs _netdev,tls,iam 0 0" >> /etc/fstab
mount -a -t efs defaults
```

Copy the original content back in and fix file permissions:

```
mv /tmp/wp-content/* /var/www/html/wp-content/
chown -R ec2-user:apache /var/www/
```

Test that wordpress can load media by rebooting the EC2 instance

```
sudo reboot
```

Update the launch template to automate loading EFS

Access the EC2 console and click Launch Templates

Check the box next to the WordPress launch template, click `Actions`, and click `Modify Template (Create new version)`.

Enter "App only, uses EFS" in the template version description. 

Scroll to advanced details and expand.

Access the User Data field and expand. After the `#!/bin/bash` line, add the following:

```
EFSFSID=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/EFSFSID --query Parameters[0].Value)
EFSFSID=`echo $EFSFSID | sed -e 's/^"//' -e 's/"$//'`
```

Add `amazon-efs-utils` to the list of packages to install in the yum command:

```
yum install -y mariadb-server httpd wget amazon-efs-utils
```

Locate the `systemctl start httpd` section and add the following lines:

```
mkdir -p /var/www/html/wp-content
chown -R ec2-user:apache /var/www/
echo -e "$EFSFSID:/ /var/www/html/wp-content efs _netdev,tls,iam 0 0" >> /etc/fstab
mount -a -t efs defaults
```

Scroll down and click "Create template version". Click "View Launch Template." Select the template again, click Actions, and select Set Default Version. Under Template version, select 3. Click Set as default version.

End of the EFS section
