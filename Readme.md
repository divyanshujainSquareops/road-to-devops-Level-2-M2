

# Create Resource

<img width="529" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/8921d7be-2aa1-4f3f-a411-05d112676c72">

<img width="952" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/661e6bd9-8c64-4f67-b4eb-eb81f38ac90c">




# Create Virtual network

<img width="768" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/881519c0-241b-4f9c-972f-b1b3de32a9e3">
  
 - ## private_subnet (for private VM)
 - ## Application-Gateway-Subnet (for load balancer)
 - ## Bastion-Gateway (for bastion service)

<img width="798" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/79ffea5d-ddb6-4530-92d5-49d4efd903e5">


## RouteTable

 - private_route_table
 - public_route_table

<img width="960" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/f8af91d5-670b-420d-bb32-8c6fa1c7595e">



## virtual machine

create 4 virtual machine by following these steps :-

 <img width="950" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/3207965b-72b1-4728-ad44-1f80bf3e73f7">

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

			CREATE DATABASE wordpressdb;
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
		-> grep CHANGE *sql | head -1;
			(slave log file and log pos)
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

<img width="951" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/e758a281-7b28-4ceb-9b28-2826b8c785b9">


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
	
			


