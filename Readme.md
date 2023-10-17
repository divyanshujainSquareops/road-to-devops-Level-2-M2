

# Create Resource

	<img width="529" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/8921d7be-2aa1-4f3f-a411-05d112676c72">



# Create Virtual network

 - ## public_subnet
 - ## private_subnet

## RouteTable

 - public_route_table 
 - private_route_table

## NAT gateways

## virtual machine

create 4 virtual machine by following these steps :-

 - create one public-VM for ssh in private VM
 - **create one private VM and install word-press infrastructure**
	install apache

	
	    sudo apt update 
    	sudo apt install apache2  
    	sudo systemctl stop    apache2.service  
    	sudo systemctl start apache2.service  
    	sudo systemctl enable apache2.service


Install PHP and Related Modules

    sudo apt install php php-mysql php-cgi php-cli php-gd
    sudo systemctl restart apache2

install wordpress

    wget https://wordpress.org/latest.zip
    sudo apt install unzip
    unzip latest.zip	
    cd wordpress
    sudo cp -r * /var/www/html/
    cd /var/www/html/
    sudo chown -R www-data:www-data /var/www/html/
    sudo chmod -R 755 /var/www/html/
    sudo rm -rf index.html

 - Create ami of wordpress-VM  and create two VM **wordpress1** and **wordpress2**
 - **Create mysql master-slave infrastructure**
	 - create Master-MySql VM
		MySql installation :-
		
			sudo apt install mysql-server
			sudo systemctl stop mysql.service
			sudo systemctl start mysql.service
			sudo systemctl enable mysql.service
			sudo mysql_secure_installation
			sudo mysql -u root -p (Enter your password, and it should login you to the mysql console.)
	Create the WordPress Database :-
	
			 sudo mysql -u root -p 

CREATE DATABASE wordpressdb :-
			
			SHOW databases;
			CREATE USER 'wpuser'@'%' IDENTIFIED BY 'your_password_here';
			GRANT ALL PRIVILEGES on wordpressdb.* to "wpuser"@"%";
			FLUSH PRIVILEGES;


Master installation in MySql :-

		-> sudo /etc/mysql/my.cnf
			bind-address = mysql-master-private-ip
			server-id=1
			expire_logs_days=10
			max_binlog_size=100M
		-> sudo systemctl restart mysql.service
		-> mysql -u root
			grant replication slave on *.* to 'wpuser'@'%';
		-> mysqldump -uroot --all-databases --master-data > masterdump.sql
		-> grep CHANGE *sql | head -l;(slave log file and log pos)
		-> scp masterdump.sql <slave_ip>:	

 - Create Slave-Sql VM :-
	 - install mysql (same as mysql-master setup)
	 - slave installation
	 
				-> sudo /etc/mysql/my.cnf
							bind-address = mysql-slave-private-ip
							server-id=2
							expire_logs_days=10
							max_binlog_size=100M
				-> sudo systemctl restart mysql.service
				-> mysql -uroot < masterdump.sql
				-> mysql -u root
						CHANGE MASTER TO MASTER_HOST='10.0.2.5',MASTER_USER='mysql_master',MASTER_PASSWORD='Deepu@123';
						start slave;
						show slave status\G;

# Azure file share permanent mount in wordpress:

	
Create crediansials file :-

		if [ ! -d "/etc/smbcredentials" ]; then
		   sudo mkdir /etc/smbcredentials
		fi
		if [ ! -f "/etc/smbcredentials/storagemultiinstance.cred" ]; then
	    sudo bash -c 'echo "username=storagemultiinstance" >> /etc/smbcredentials/storagemultiinstance.cred'
        sudo bash -c 'echo "password=V3Afsvr6JKmhZ2NtmvKY0aPK7HCg49Ga1+I9X81+                                                                          5TkMW+zMIWleM6Io/z40wnJ1AZoRH4iy1b3A+ASt2p05IQ==" >> /etc/smbcredentials/storagemultiinstance.cred'
		fi


Create fstab file :-	

		sudo bash -c 'echo "//storagemultiinstance.file.core.windows.net/filesharemultiinstancewordpress /var/www/html/mnt/filesharemultiinstancewordpress/wp-content/uploads cifs nofail,credentials=/etc/smbcredentials/storagemultiinstance.cred,dir_mode=0777,file_mode=0777,serverino,nosharesock,actimeo=30" >> /etc/fstab'

mount file-share address permanent in wordpress-VM :-

		sudo mount -t cifs //storagemultiinstance.file.core.windows.net/filesharemultiinstancewordpress  /var/www/html/mnt/filesharemultiinstancewordpress/wp-content/uploads -o credentials=/etc/smbcredentials/storagemultiinstance.cred,dir_mode=0777,file_mode=0777,serverino,nosharesock,actimeo=30


# Create Applicate gateway (layer 7 ) :

	I followed this link to create AG(LB7) :-
		[Create Application gateway](https://learn.microsoft.com/en-us/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)
	
			


