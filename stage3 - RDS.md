Populate Environment variables:


```
DBPassword=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
```

Take a backup of the local database

```
mysqldump -h $DBEndpoint -u $DBUser -p$DBPassword $DBName > cis194WordPress.sql
```


Update DbEndpoint environment variable:

```
DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /CIS194/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
```

restore database:

```
mysql -h $DBEndpoint -u $DBUser -p$DBPassword $DBName < cis194WordPress.sql 
```

Change WordPress config so it uses RDS:

```
sudo sed -i "s/'localhost'/'$DBEndpoint'/g" /var/www/html/wp-config.php
```

Stop MariaDB

```
sudo systemctl disable mariadb
sudo systemctl stop mariadb
```

Remove these lines from the User Data section of your modified Launch Template:

```
systemctl enable mariadb
systemctl start mariadb
mysqladmin -u root password $DBRootPassword


echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
mysql -u root --password=$DBRootPassword < /tmp/db.setup
rm /tmp/db.setup
```

