# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
apt-get update
apt-get -y install unzip 

echo "\n----- Installing openjdk 8 ------\n"
apt-get -y install openjdk-8-jdk

echo "\n----- Installing tomcat 8 ------\n"
apt-get -y install tomcat8
/etc/init.d/tomcat8 stop

echo "\n----- Updating tomcat memory settings ------\n"
sed -i "s/-Xmx128m/-Xms512m -Xmx1024m/" /etc/default/tomcat8

echo "\n----- Installing Postgres ------\n"
PG_VERSION=9.5
PG_CONF="/etc/postgresql/$PG_VERSION/main/postgresql.conf"
PG_HBA="/etc/postgresql/$PG_VERSION/main/pg_hba.conf"

apt-get -y install postgresql postgresql-contrib
echo "\n----- Updating listen_addresses on postgresql.conf ------\n"
sed -i "s/#listen_addresses = 'localhost'/listen_addresses = '*'/" "$PG_CONF"
echo "\n----- Updating postgres pg_hba.conf file------\n"
echo "host    all             all             all                     md5" >> "$PG_HBA"

service postgresql restart

echo "\n----- Downloading/installing GeoNetwork ------\n"
GN_VERSION=3.2.2
wget -q -O geonetwork.war https://downloads.sourceforge.net/project/geonetwork/GeoNetwork_opensource/v$GN_VERSION/geonetwork.war
unzip geonetwork.war -d /var/lib/tomcat8/webapps/geonetwork
chown -R tomcat8:tomcat8 /var/lib/tomcat8/webapps/geonetwork

echo "\n----- Setup GeoNetwork database ------\n"
sudo -u postgres psql -c "CREATE USER geonetwork WITH PASSWORD 'geonetwork';"
sudo -u postgres bash -c "createdb -E UTF8 -T template0 --locale=en_US.utf8 -O geonetwork geonetwork;"

sed -i "s/jdbc.username=admin/jdbc.username=geonetwork/" /var/lib/tomcat8/webapps/geonetwork/WEB-INF/config-db/jdbc.properties
sed -i "s/jdbc.password=gnos/jdbc.password=geonetwork/" /var/lib/tomcat8/webapps/geonetwork/WEB-INF/config-db/jdbc.properties    
sed -i "s/h2.xml/postgres.xml/" /var/lib/tomcat8/webapps/geonetwork/WEB-INF/config-node/srv.xml
    
echo "\n----- Starting Tomcat ------\n"
/etc/init.d/tomcat8 start
SCRIPT


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.network "forwarded_port", guest: 5432, host: 15432
  
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  #config.vm.synced_folder ".", "/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = "2048"

    vb.name = "GeoNetwork 3.2.2"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", privileged: true, inline: $script
end