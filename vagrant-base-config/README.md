# vagrant base config

### Purpose

Vagrant is a tool to download distros from web and install as guest virtual machine (VM) in local (host) machines.
Local host machine may be Windows, OSX, Linux distro or another OS which is supported by Vagrant.

## Downlaod & Install

[Vagrant](https://www.vagrantup.com/downloads.html)

[Oracle Virtualbox](https://www.virtualbox.org/wiki/Downloads)



## Setup

1. make a clean dir

        mkdir vagrant


3. download git project

        cd vagrant
        git clone https://github.com/abeym/vagrant.git
        cd vagrant-base-config

4. start vagrant

        vagrant up

## Explanation

[Refer the attached Vagrantfile](Vagrantfile)

#### Vagrantfile

  The primary configuration location for any Vagrant development environment is a file called Vagrantfile which you need to place in your project’s folder. The configuration syntax of this Vagrantfile is Ruby. Every configuration option you will need you can place inside this file.

#####  About provisioning

Provisioning is used to call additional install scripts. It can be either inline or use a sh sript. E.g.
Vagrant offers multiple options ranging from basic shell scripts to software automation managers such as Puppet, Chef or Ansible. You can even configure it to use multiple provisioners at the same time.

#####  Vagrant API version

        Vagrant.configure("2") do |config|
        end

#####  Specifying the base box

        config.vm.box = "ubuntu/trusty64"

#####  Network configurations

to set up port forwarding for now. Insert the following line into your configuration file:

        config.vm.network :forwarded_port, guest: 80, host: 8931, auto_correct: true

#####  Syncing project files

A good practice to follow when you’re using a Vagrant development environment, or as a matter of fact any virtualized development environment, is to share your project files between the host and the guest operating systems, so that your project files are not copied into the virtual machine, because if you delete your VM, the files will be lost with it.

Sharing folders between the host and the guest operating systems is very easy to do with Vagrant. Just enter the following config into the Vagrantfile:

        config.vm.synced_folder "./", "/var/www", create: true, group: "www-data", owner: "www-data"


#####  VirtualBox specific configurations

Now that we configured the network and synced folders, we should configure the virtual machine itself. Vagrant lets you dynamically modify the VM – you can change the name, memory, etc.

        config.vm.provider "virtualbox" do |v|
            v.name = "SitePoint Test Vagrant"
            v.customize ["modifyvm", :id, "--memory", "1024"]
        end

#####  Shell script provisioning

The easiest way to provision a base box is to use basic shell script commands which then run inside the virtual machine. We need to define the provisioning type, which in our case is called shell. Lets crate a file provision/setup.sh file. Let’s write this block inside the configuration file:

        config.vm.provision "shell" do |s|
            s.path "provision/setup.sh"
        end

#####  Installing Base Packages

Let the fun begin! Let’s install the base packages, namely: Git, Nginx, PHP-FPM and MySQL. In the provision/setup.sh file append the following lines:

        echo "Installing Git"
            apt-get install git -y > /dev/null

            echo "Installing Nginx"
            apt-get install nginx -y > /dev/null


##### Installing PHP

        echo "Updating PHP repository"
          apt-get install python-software-properties build-essential -y > /dev/null
          add-apt-repository ppa:ondrej/php5 -y > /dev/null
          apt-get update > /dev/null


        echo "Installing PHP"
          apt-get install php5-common php5-dev php5-cli php5-fpm -y > /dev/null

        echo "Installing PHP extensions"
          apt-get install curl php5-curl php5-gd php5-mcrypt php5-mysql -y > /dev/null


#####  Installing MySQL

        apt-get install debconf-utils -y > /dev/null

        debconf-set-selections <<< "mysql-server mysql-server/root_password password 1234"
        debconf-set-selections <<< "mysql-server mysql-server/root_password_again password 1234"

        apt-get install mysql-server -y > /dev/null


#####  Configuring Nginx settings

Let’s create a file called nginx_vhost (no file extension) in our provision folder inside another folder called config. I.e. the path to the file will be provision/config/nginx_vhost.

The file will contain an Nginx basic virtual host configuration:

        server {
                listen 80;
                server_name localhost;

                root /var/www/src/;
                index index.php index.html;

                # Important for VirtualBox
                sendfile off;

                location / {
                    try_files $uri $uri/ =404;
                }

                location ~* \.php {
                    include fastcgi_params;

                    fastcgi_pass unix:/var/run/php5-fpm.sock;

                    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                    fastcgi_cache off;
                    fastcgi_index index.php;
                }
            }

Insert the following lines into setup.sh to copy this configuration to the Nginx folder:

        echo "Configuring Nginx"
        cp /var/www/provision/config/nginx_vhost /etc/nginx/sites-available/nginx_vhost > /dev/null
        ln -s /etc/nginx/sites-available/nginx_vhost /etc/nginx/sites-enabled/
        rm -rf /etc/nginx/sites-available/default
        service nginx restart > /dev/null

### Help / Feedback

[Vagrant Getting started](https://www.vagrantup.com/docs/getting-started/provisioning.html)

[abeym](https://github.com/abeym)

<a target="_blank" href="mailto:abey_mail@yahoo.com?Subject=vagrant-base-config&body=About%20vagrant-base-config">Send an Email</a>


### Version

    1.0


# Wiki Ref
1. [Markdown-Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
2. [mastering-markdown](https://guides.github.com/features/mastering-markdown/)
