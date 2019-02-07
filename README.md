# linux-server-configuration
1. By Venkata Ramana Yenamandra 
2. IP Address 100.26.97.182
3. Url http://100.26.97.182.xip.io/

# Ubuntu Linux Server  instance on amazon EC2:
1. Create an amazonaws account
2. Login with your credentials
3. Choose EC2 and launch instance
4. Check for IPv4 public Ip and download pem file with created key value pair
5. run ssh -i pemfilename.pem ubuntu@ip
6. like ssh pair.pem ubuntu@100.26.97.182
7. 22 is the port by default
8. Change the port by 2200

# Update Packages
1. sudo apt-get update
2. sudo apt-get upgrade
3. sudo apt-get install unattended-upgrades
4. sudo apt-get dist-upgrade

# Changing of Port from 22 to 2200
1. sudo vi /etc/ssh/sshd_config
2. Edit Port number 22 to 2200
3. PubkeyAuthentication yes
4. PasswordAuthentication no
5. Save and exit using esc and confirm with :wq
6. Restart: sudo service ssh restart
7. Change inbound rules in Amazon EC2
8. Add: Custom TCP Rule as 2200
9. Add: Custom TCP Rule as 123
10. Add: HTTP as 80
11. ssh -i pair.pem -p 2200 ubuntu@100.26.97.182

# Configure UFW:
1. sudo ufw status
2. sudo ufw default deny incoming
3. sudo ufw default allow outgoing
4. sudo ufw allow 2200/tcp
5. sudo ufw allow www
6. sudo ufw allow 123/udp
7. sudo ufw deny 22
8. sudo ufw enable
10. sudo ufw status
11. it displays status to be active.

# Create user grader
1. sudo adduser grader
2. enter password
3. edit files as sudo visduo
4. at root All=(ALL:ALL) ALL
5. add grader ALL=(ALL:ALL) ALL
6. save and exit 
7. run su - grader
8. enter password

# SSH key pair for grader
1. create .ssh folder by mkdir /home/grader/.ssh
2. su grader
3. sudo cp /home/ubuntu/.ssh/authorized_keys /home/grader/.ssh/authorized_keys
4. chown grader.grader /home/grader/.ssh
5. sudo su and goto root directorie after next command
6. sudo Group add usermod -aG sudo grader
7. change permissions for .ssh folder chmod 0700 /home/grader/.ssh/, for authorized_keys chmod 644 authorized_keys
8. Check in vi /etc/ssh/sshd_config file if PermitRootLogin is set to No
9. Restart SSH: sudo service ssh restart
10. grader account Working or Not by RUNNING this command : ssh -i linux_31.pem -p 2200 grader@3.90.89.13
11. Configure the local timezone to UTC Logged On grader Account
12. TIME ZONE: sudo dpkg-reconfigure tzdata. Choose time zone UTC

# Deploying Project:
1. sudo apt-get install apache2
2. sudo apt-get install libapache2-mod-wsgi-py3
3. sudo a2enmod wsgi

# Setting up google oauth2
1. login to console.developers.google.com 
2. create a project
3. create oauth consent screen
4. Javascript origin http://ip.xip.io
5. redirect URI
6. http://ip.xip.io/login
7. http://ip.xip.io/gconnect
8. http://ip.xip.io/callback
9. Download client_secrets.json and paste the data

# Install POSTGRESQL
1. sudo apt-get install libpq-dev python-dev
2. sudo apt-get install postgresql postgresql-contrib
3. sudo su - postgres
4. psql
5. CREATE USER catalog WITH PASSWORD 'catalog';
6. ALTER USER catalog CREATEDB;
7. CREATE DATABASE catalog WITH OWNER catalog;
8. \c catalog
9. REVOKE ALL ON SCHEMA public FROM public;
10. GRANT ALL ON SCHEMA public TO catalog;
11. \q
12. exit
13. Switch back to the grader user: exit

# Install GIT
1. sudo apt-get install git
2. clone the project repository
3. cd /var/www/ clone the project
4. sudo git clone 'http://github.com/username/catalog.git'
5. sudo chown -R grader:grader catalog/
6. cd /var/www/catalog/catalog
7. rename the project main file ti __inti__.py
8. change create_engine statement in __init__.py , database.py , data.py
9. engine=create_engine('postgresql://catalog:catalog@localhost/catalog')

# ENable new Virtual Host
1. sudo nano /etc/apache2/sites-available/catalog.conf
2. '''<VirtualHost *:80>
    ServerName 100.26.97.182.xip.io
    ServerAlias ec2-100-26-97-182.compute-1.amazonaws.com
    ServerAdmin ubuntu@54.210.140.47
    WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/catalog/venv3/lib/python3.6/site-packages
    WSGIProcessGroup catalog
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>'''
3. save and exit
4. ENable sudo a2ensite catalog
5. service apache2 reload

# FLask Application Set up
1. cd /var/www/catalog/catalog.wsgi
2. '''import sys
import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0, "/var/www/catalog/")
  from catalog import app as application
  application.secret_key = 'supersecretkey''''
3. sudo service apache2 restart
4. create and activate the virtual environment
5. . /venv/bin/activate
6. pip install httplib2
7. pip install requests
8. pip install --upgrade oauth2client
9. pip install sqlalchemy
10. pip install flask
11. sudo apt-get install libpq-dev
12. pip install psycopg2-binary
13. run database.py file
14. deactivate the virtual environment
15. sudo service apache2 reload
16. sudo service apache2 restart

# Security Updates and package updates
1. sudo apt-get update
2. sudo apt-get upgrade
3. sudo apt-get dist-upgrade

# Run the project
1. Open your browser
2. Enter http://100.26.97.182.xip.io

# Resources
1. I have gained help from many online resources and mentors who encouraged and supported us.
2. I could surely say that videos provided in fullstack course have provided me a lot of knowledge in this project.
3. I used online resources like stack over flow , reddit.com , chatterboxissues.
4. I can surely say that this project cts as a testimony for my skill set I gained from this course.
5. I thank udacity for everything.
