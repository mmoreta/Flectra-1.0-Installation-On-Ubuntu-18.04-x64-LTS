# Flectra-1.0-Installation-On-Ubuntu-18.04-x64-LTS

#!/bin/bash
sudo adduser --system --quiet --shell=/bin/bash --home=/opt/flectra --gecos 'flectra' --group flectra
sudo mkdir /etc/flectra && 
sudo mkdir /var/log/flectra/
sudo apt update && 
sudo apt upgrade -y && 
sudo apt install postgresql postgresql-server-dev-10 build-essential python3-pil python3-lxml python-ldap3 python3-dev python3-pip python3-setuptools npm nodejs git gdebi libldap2-dev libsasl2-dev  libxml2-dev libxslt1-dev libjpeg-dev -y
git clone --depth=1 --branch=1.0 https://gitlab.com/flectra-hq/flectra.git /opt/flectra/server
sudo chown flectra:flectra /opt/flectra/ -R && 
sudo chown flectra:flectra /var/log/flectra/ -R && 
cd /opt/flectra/server && 
sudo pip3 install -r requirements.txt
sudo npm install -g less@3.0.4 less-plugin-clean-css -y && 
sudo ln -s /usr/bin/nodejs /usr/bin/node
cd /tmp && 
wget http://mirrors.kernel.org/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb && 
sudo dpkg -i libpng12-0_1.2.54-1ubuntu1_amd64.deb && 
wget https://downloads.wkhtmltopdf.org/0.12/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb && 
sudo gdebi -n wkhtmltox-0.12.2.1_linux-trusty-amd64.deb && 
rm wkhtmltox-0.12.2.1_linux-trusty-amd64.deb
sudo ln -s /usr/local/bin/wkhtmltopdf /usr/bin/ && 
sudo ln -s /usr/local/bin/wkhtmltoimage /usr/bin/
wget -N http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz && 
sudo gunzip GeoLiteCity.dat.gz && 
sudo mkdir /usr/share/GeoIP/ && 
sudo mv GeoLiteCity.dat /usr/share/GeoIP/
sudo su - postgres -c "createuser -s flectra"
sudo su - flectra -c "/opt/flectra/server/flectra-bin --addons-path=/opt/flectra/server/addons -s --stop-after-init"
sudo mv /opt/flectra/.flectrarc /etc/flectra/flectra.conf
sudo sed -i "s,^\(logfile = \).*,\1"/var/log/flectra/flectra-server.log"," /etc/flectra/flectra.conf
sudo sed -i "s,^\(logrotate = \).*,\1"True"," /etc/flectra/flectra.conf
sudo sed -i "s,^\(proxy_mode = \).*,\1"True"," /etc/flectra/flectra.conf
sudo cp /opt/flectra/server/debian/init /etc/init.d/flectra && 
sudo chmod +x /etc/init.d/flectra
sudo ln -s /opt/flectra/server/flectra-bin /usr/bin/flectra
sudo update-rc.d -f flectra defaults
sudo service flectra start
