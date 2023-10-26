

# Create Resource

<img width="529" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/8921d7be-2aa1-4f3f-a411-05d112676c72">

<img width="952" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/661e6bd9-8c64-4f67-b4eb-eb81f38ac90c">




# Create Virtual network

<img width="768" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/881519c0-241b-4f9c-972f-b1b3de32a9e3">
  
 - ## private_subnet (for private VM)
 - ## Application-Gateway-Subnet (for load balancer)
 - ## Bastion-Gateway (for bastion service)

<img width="798" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/79ffea5d-ddb6-4530-92d5-49d4efd903e5">

<img width="286" alt="Screenshot 2023-10-26 145641" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/6b231998-bd87-4d15-9f6a-f9832f0e81ee">


<img width="293" alt="Screenshot 2023-10-26 145632" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/7b26b37c-44c9-4b04-8baa-7626c783a458">

## NatGateway

<img width="797" alt="Screenshot 2023-10-26 145657" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/af4d82c2-a6aa-4761-8b1e-6982535f51d5">

![image](https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/bdb83824-f2e3-4384-a2d1-03243bb5c8a4)


![image](https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/a23512db-206c-47cb-ac00-19c2e17b6c25)


## RouteTable

 - private_route_table
 - public_route_table

<img width="960" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/f8af91d5-670b-420d-bb32-8c6fa1c7595e">

<img width="957" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/b1d074f3-1c3d-43f3-af57-4434ddd77020">

<img width="793" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/60db7739-8b7f-4ef7-9ea6-713889b1518e">

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
	
<img width="320" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/db1d9545-2119-494e-a887-b804794f1e78">


<img width="466" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/b5adbc60-ddfc-4884-ac98-15e63046fbd6">


<img width="943" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/f3b035cd-9263-46c0-bb37-6993aa0b4862">


<img width="357" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/82ab6694-5f41-456d-afea-cd17b505bf30">


<img width="423" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/241ecec9-5a0c-42c0-9cd6-03f9ce1f253d">


 <img width="564" alt="image" src="https://github.com/divyanshujainSquareops/road-to-devops-Level-2-M2/assets/148210383/cd6beec5-b4a1-4299-ad00-d3b5b159044a">



