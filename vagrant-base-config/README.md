# vagrant base-config

### Purpose

Vagrant is a tool to download distros from web and install as guest virtual machine (VM) in local (host) machines.
Local host machine may be Windows, OSX, Linux distro or another OS which is supported by Vagrant.

## Downlaod & Install

[Vagrant Getting started](https://www.vagrantup.com/docs/getting-started/provisioning.html)
[Oracle Virtualbox](https://www.virtualbox.org/wiki/Downloads)



## Setup

1. make a clean dir

        mkdir vagrant


3. download git project

        cd vagrant
        git clone 
        vagrant init

4. Vagrantfile is created in the current dir.
<br/> Vagrantfile a config file that tells

        1. which linux distro to be downloaded  
        2. changes to be done on the Virtualbox  
        3. multiple VM details
        4. other scripts to run after downloading the distro.
        and more...

See below  for more config changes on Vagrantfile


## Config

[Refer the attached Vagrantfile](Vagrantfile)

#### Create vagrant boxes

1. Start by searching for an existing distro. Edit Vagrantfile to add this distro's name.
<br/>e.g. below we are starting with the ubuntu precise 64 distro.  <br/>
[Discover Vagrant Boxes](https://atlas.hashicorp.com/boxes/search) <br />

        config.vm.box = "hashicorp/precise64"

2. Start VM

   With the above changes, the guest VM can be installed and started with the command

        vagrant up

3. ssh into them guest VM

  Connect using ssh into the virtual box guest machine. <br/>
  /vagrant folder in the guest is same as the current folder in the host machine, the folder with the Vagrantfile file

        vagrant ssh

4. Teardown

    run the following command from the folder hainvg the Vagrantfile

        vagrant suspend     // to suspend the virtualbox vm
        vagrant halt        // to stop the vm
        vagrant relaod      // to re run the Vagrantfile without downloading the initial distro.
        vagrant destroy     // to destroy and remove all vm files from Virtualbox

5. Provisioning

  provision is used to call additional install scripts. It can be either inline or use a sh sript. E.g.

        config.vm.provision "shell", inline: <<-SHELL
          apt-get update
          apt-get install -y apache2
        SHELL

    To call another shell script .... <br>
    create create a file e.g. [bootstrap.sh](bootstrap.sh) <br>
    call bootstrap.sh file from Vagrantfile <br>

        config.vm.provision :shell, path: "bootstrap.sh"


6. Networking

    Port forwarding -
    <br/>the below config forwards request from host.ip.addr.num:4567 to guest.ip.addr.num:80
    <br/> try to open in browser http://host.ip.addr.num:4567

        config.vm.network :forwarded_port, guest: 80, host: 4567

7. Sharing

    This creates a unique url accessible on internet which will directly connect to the vagrant guest box.
  <br/> create a new account at [HashiCorp's Atlas](https://www.vagrantup.com/docs/other/atlas.html).
  <br/> login and give user name / password here
  <br/> once logged in, share


        vagrant login
        vagrant share

  it outputs a unique url e.g. ==> default: URL: http://misty-diesel-0857.vagrantshare.com <br>
  This url can be accessed from any browser connected to net and the request will be served from the guest box <br>


## Projects

## Tips and Notes

### Help


### Version


### Command 1

    command 1


# Wiki Ref
1. [Markdown-Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
2. [mastering-markdown](https://guides.github.com/features/mastering-markdown/)
