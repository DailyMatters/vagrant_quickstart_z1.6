### Zend framework (1.6) quickstart

#### Build a workspace

1 - Download and install VirtualBox 6.0 or older (newer versions haven't been bugfixed yet).

2 - Download and install Vagrant (if you're not familiar with vagrant please read the [documentation first](https://www.vagrantup.com/docs/)).

3 - Create folder for your new project and run `vagrant init levelten/ubuntu64-php5.6` to initialize a new vagrant box.

4 - Open the `Vagrantfile` file and replace it with the following:

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
     GREEN='\033[0;32m'
     NC='\033[0m' # No Color

     apt-get update

     # Get java for liquibase
     apt-get install -y openjdk-8-jre-headless

     printf "${GREEN}Start Provisioning Liquibase${NC}\n"

     # Get liquibase
     mkdir liquibase
     cd liquibase
     wget https://github.com/liquibase/liquibase/releases/download/v3.8.5/liquibase-3.8.5.tar.gz
     tar -zxvf liquibase-3.8.5.tar.gz
     rm liquibase-3.8.5.tar.gz
     echo 'export PATH="${PATH}:/home/vagrant/liquibase"' >> /home/vagrant/.bashrc
     source /home/vagrant/.bashrc

     printf "${GREEN}Start Provisioning Database Structure and fields.${NC}\n"

     # Create a database
     mysql -uroot -e "CREATE DATABASE IF NOT EXISTS zftest;"
     mysql -uroot -e "use zftest; CREATE TABLE IF NOT EXISTS albums (id int(11) NOT NULL auto_increment, artist varchar(100) NOT NULL, title varchar(100) NOT NULL, PRIMARY KEY (id));"
     mysql -uroot -e "use zftest; INSERT INTO albums (artist, title) VALUES ('Duffy','Rockferry'), ('Van Morrison', 'Keep it Simple');"

     printf "${GREEN}Finished Provisioning.${NC}\n"
   SHELL
end

# End here
```

This will allow us to launch the webserver on the ip above (`192.168.33.10`). It also creates the database needed to run the code from the tutorial.

The actual credentials that you need to insert into the `config.ini` file are the following:

```c
zf-tutorial/application/config.ini
[general]
db.adapter = PDO_MYSQL
db.params.host = localhost
db.params.username = root
db.params.password =
db.params.dbname = zftest
```

Add these when you reach the `Database` section of the tutorial.

Please note that we're also installing jre and liquibase, this might be useful if you want to explore Liquibase later on.

5 - Run `vagrant up` to download the resources and fire the VM.

6 - [Download Zend Framework version 1.6](https://packages.zendframework.com/releases/ZendFramework-1.6.2/ZendFramework-1.6.2.zip) to the same folder where you have the `Vagrantfile` file. This folder is synced by default.

7 - Unzip the contents of the framework.

8 - Access the VM with `vagrant ssh`.

9 - Inside the VM you should be able to access the framework files inside the `/vagrant` folder. (type `cd /vagrant` to access the folder).

This way you can work the files in your computer while still having access to a VM to run the project. The alternative is to run the entire project inside the VM (be careful with this option because if you destroy your VM with `vagrant destroy` you'll lose everything).

To follow this alternative follow steps 1 and 2 just like in the previous example.

Instead of creating the box from scratch, create a new folder for your project and run `vagrant init`. Then replace the VagrantFile contents with the content below. This file will provision everything needed (including the framework itself) to start working.

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

     # Create a database
     mysql -uroot -e "CREATE DATABASE IF NOT EXISTS zftest;"
     mysql -uroot -e "use zftest; CREATE TABLE IF NOT EXISTS albums (id int(11) NOT NULL auto_increment, artist varchar(100) NOT NULL, title varchar(100) NOT NULL, PRIMARY KEY (id));"
     mysql -uroot -e "use zftest; INSERT INTO albums (artist, title) VALUES ('Duffy','Rockferry'), ('Van Morrison', 'Keep it Simple');"
   SHELL
end

# End here
```

After this point you can serve the code by using [PHP built in server](https://www.php.net/manual/en/features.commandline.webserver.php). Just access the VM using `vagrant ssh`, navigate to the root of your code and execute `php -S 192.168.33.10:8000`.

----

If you want to strip Zend framework to a bare minimum, delete the demos (although you get bonus points if you can run all the demos and understand the code inside), externals, laboratory and tests folders. For now, we can do great with just the library folder which contains all the Zend Framework code.

You can also download the [full documentation here](https://packages.zendframework.com/releases/ZendFramework-1.6.2/ZendFramework-1.6.2-manual-en.zip). It will help you understanding better each component, their functionality and their use.

From this point on, please follow the pdf file and have fun!

----

#### Liquibase support

If you want [Liquibase](https://www.liquibase.org/) support use the code block below instead to provision your machine:

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
     apt-get install -y sqlite3
     apt-get install -y openjdk-8-jre-headless
     wget https://packages.zendframework.com/releases/ZendFramework-1.6.2/ZendFramework-1.6.2.zip
     unzip ZendFramework-1.6.2.zip
     rm ZendFramework-1.6.2.zip
     mkdir liquibase
     cd liquibase
     wget https://github.com/liquibase/liquibase/releases/download/v3.8.5/liquibase-3.8.5.tar.gz
     tar -zxvf liquibase-3.8.5.tar.gz
     rm liquibase-3.8.5.tar.gz
     echo 'export PATH="${PATH}:/home/vagrant/liquibase"' >> /home/vagrant/.bashrc
     source /home/vagrant/.bashrc
     wget https://bitbucket.org/xerial/sqlite-jdbc/downloads/sqlite-jdbc-3.30.1.jar

     # Create a database
     mysql -uroot -e "CREATE DATABASE IF NOT EXISTS zftest;"
     mysql -uroot -e "use zftest; CREATE TABLE IF NOT EXISTS albums (id int(11) NOT NULL auto_increment, artist varchar(100) NOT NULL, title varchar(100) NOT NULL, PRIMARY KEY (id));"
     mysql -uroot -e "use zftest; INSERT INTO albums (artist, title) VALUES ('Duffy','Rockferry'), ('Van Morrison', 'Keep it Simple');"
   SHELL
end

# End here
```

Let's take a quick look. On the box above we were only downloading and giving access to Zend Framework 1.6. On this box we're doing a little more. We're installing Openjdk because we'll need it for Liquibase. We're also installing sqlite3 so we can have a database engine to work some examples with Liquibase. Lastly, we're installing Liquibase and adding the executable and adding it to the path so it's available everywhere on the machine.

So, after provisioning this machine, use `vagrant ssh` to access the machine and then try running `liquibase --help`. You should see the liquibase help prompt.

This box build on the last one, so everything that's available on the first, is also available on this one. Consider this one as being version 2.0.
