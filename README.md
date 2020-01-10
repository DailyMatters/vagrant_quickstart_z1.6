### Zend framework (1.6) quickstart

#### Build a workspace

1 - Download and install VirtualBox 6.0 or older (newer versions haven't been bugfixed yet).

2 - Download and install Vagrant (if you're not familiar with vagrant please read the [documentation first](https://www.vagrantup.com/docs/)).

3 - Create  folder for your new project and run `vagrant init levelten/ubuntu64-php5.6` to initialize a new vagrant box.

4 - Open the `Vagrantfile` file and uncomment the following line:

`# config.vm.network "private_network", ip: "192.168.33.10"`

This will allow us to launch the webserver on the ip above.

5 - Run `vagrant up` to download the resources and fire the VM.

6 - [Download Zend Framework version 1.6](https://packages.zendframework.com/releases/ZendFramework-1.6.2/ZendFramework-1.6.2.zip) to the same folder where you have the `Vagrantfile` file. This folder is synced by default.

7 - Unzip the contents of the framework.

8 - Access the VM with `vagrant ssh`.

9 - Inside the VM you should be able to access the framework files inside the `/vagrant` folder. (type `cd /vagrant` to access the folder).

This way you can work the files in your computer while still having access to a VM to run the project. The alternative is to run the entire project inside the VM (be careful with this option because if you destroy your VM with `vagrant destroy` you'll lose everything).

To follow this alternative follow steps 1 and 2 just like in the previous example.

Instead of creating the box from scratch, replace the VagrantFile contents with the content below. This file will provision everything needed (including the framework itself) to start working.

```ruby
# Copy below this point

# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  
  config.vm.box = "levelten/ubuntu64-php5.6"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get install -y unzip
     wget https://packages.zendframework.com/releases/ZendFramework-1.6.2/ZendFramework-1.6.2.zip
     unzip ZendFramework-1.6.2.zip
     rm ZendFramework-1.6.2.zip
   SHELL
end

# End here
```

After this point you can serve the code by using [PHP built in server](https://www.php.net/manual/en/features.commandline.webserver.php). Just access the VM using `vagrant ssh`, navigate to the root of your code and execute `php -S 192.168.33.10:8000`.

----

If you want to strip Zend framework to a bare minimum, delete the demos, externals, laboratory and tests folders. For now, we can do grest with just the library folder which contains all the Zend Framework code.

You can also download the [full documentation here](https://packages.zendframework.com/releases/ZendFramework-1.6.2/ZendFramework-1.6.2-manual-en.zip). It will help you understanding better each component, their functionality and their use.

From this point on, please follow the pdf file and have fun! 