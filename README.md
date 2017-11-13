# Install vatic on Ubuntu 14.04
1. Install apache2 server and mysql first.
2. First install the needed packages, make sure that you leave the MySQL password empty (a password might be used, but for this vatic needs to be configured differently, not tested in this tutorial):

sudo apt-get install -y git python-setuptools python-dev libfreetype6 libfreetype6-dev apache2 libapache2-mod-wsgi mysql-server-5.6 mysql-client-5.6 libmysqlclient-dev

3.Next, install these dependencies, this might take a while:

sudo easy_install -U cython==0.20
sudo easy_install -U SQLAlchemy wsgilog  mysql-python munkres parsedatetime argparse
sudo easy_install -U numpy

4.Install ffmpeg, on Ubuntu 14.04 you need to add an additional ppa:

sudo add-apt-repository ppa:mc3man/trusty-media
sudo apt-get update
sudo apt-get install ffmpeg
5.Download and clone vatic. This consists of three parts:, turkic, pyvision and vatic itself (for example on the desktop):

cd ~/Desktop
mkdir vatic
cd vatic
git clone https://github.com/cvondrick/turkic.git
git clone https://github.com/cvondrick/pyvision.git
git clone https://github.com/cvondrick/vatic.git

6.First install turkic:

cd turkic
sudo python setup.py install
cd ..

7.Do the same for pyvision:

cd pyvision
sudo python setup.py install
cd ..

8.Now configure the webserver (Apache2), first take backup of file (note: in some Ubuntu/Apache version, the file ends with .conf, in others it doesn't so make sure you select the right file:

sudo cp /etc/apache2/sites-enabled/000-default.conf /etc/apache2/sites-enabled/000-default.conf.backup

9. Now edit the file (i.e. delete all information from this file), and replace with content below:

sudo gedit /etc/apache2/sites-enabled/000-default.conf

10. Change the content of this file to the following (make sure all file paths are correct!):

    WSGIDaemonProcess www-data
    WSGIProcessGroup www-data

    <VirtualHost *:80>
        ServerName localhost
        DocumentRoot /home/administrator/Desktop/vatic/vatic/public

        WSGIScriptAlias /server /home/administrator/Desktop/vatic/vatic/server.py
        CustomLog /var/log/apache2/access.log combined
    </VirtualHost>
    
11. Give Apache access to this directory by change the config file:

sudo gedit /etc/apache2/apache2.conf

12.Add access to the directory by adding the following lines:

<Directory /home/administrator/Desktop/vatic/vatic/>
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

Restart Apache2:

sudo cp /etc/apache2/mods-available/headers.load /etc/apache2/mods-enabled
sudo apache2ctl graceful

13. Edit the MySQL database:

mysql -u root
create database vatic;
exit

14. In the vatic/vatic directory:

cd vatic
cp config.py-example config.py
turkic setup --database
turkic setup --public-symlink
Test if vatic is working, by surfing in a browser to localhost, and by testing this in the command line:

turkic status --verify
If the server returns an Internal Server Error 500, remove the "Require all granted" line in the /etc/apache2/apache2.conf file (sometimes not needed depending on version).
An error is seen with Mechanical Turk, however, the other features should return ok. Now give correct permissions for www-data:

sudo usermod -a -G administrator www-data
sudo mkdir /var/www/.python-eggs/
sudo chown www-data:www-data /var/www/.python-eggs/
sudo apache2ctl graceful
Restart Ubuntu (user www-data should relogin). That's it!

From here You can refer the install step provided by Official Vatic.
More detaied information, please refer Efficiently Scaling Up Video Annotation with Crowdsourced Marketplaces. IJCV 2012 http://mit.edu/vondrick/vatic/

