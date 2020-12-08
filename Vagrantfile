$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'petclinic'@'%' identified by 'petclinic';" && \
  mysql -e "create database petclinic;" && \
  mysql < /vagrant/mysql/script/user.sql && \
  mysql < /vagrant/mysql/script/schema.sql && \
  mysql < /vagrant/mysql/script/data.sql
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  config.vm.define "mysqlserver" do |mysqlserver|
    mysqlserver.vm.network "public_network", ip: "192.168.1.22", bridge: "Qualcomm QCA61x4A 802.11ac Wireless Adapter"

    mysqlserver.vm.provider "virtualbox" do |vb|
      vb.name = "mysqlserver"
    end

    mysqlserver.vm.provision "shell", inline: $script_mysql
    mysqlserver.vm.provision "shell",
         inline: "cat /vagrant/mysql/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
    mysqlserver.vm.provision "shell", inline: "service mysql restart"
  end

  config.vm.define "springapp" do |springapp|
    springapp.vm.network "forwarded_port", guest: 8080, host: 8080
    springapp.vm.network "public_network", ip: "192.168.1.25", bridge: "Qualcomm QCA61x4A 802.11ac Wireless Adapter"

    springapp.vm.provider "virtualbox" do |vb|
      vb.name = "springapp"
      vb.memory = 4096
      vb.cpus = 2
    end

    springapp.vm.provision "shell", inline: "apt-get update && apt-get install -y openjdk-11-jre"
    springapp.vm.provision "shell", inline: "java -Dspring.profiles.active=mysql -jar /vagrant/springapp/*.jar &"
  end
end
