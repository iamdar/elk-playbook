# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Use Ubuntu 22.04 LTS
  config.vm.box = "bento/ubuntu-22.04"
  config.vm.hostname = "elk-lab"

  # Networking: Private IP for access from the host machine
  config.vm.network "private_network", ip: "192.168.56.10"

  # Provider Configuration
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096" # 4GB RAM is recommended for ELK
    vb.cpus = 2
    vb.name = "elk-stack-lab"
  end

  # Provisioning: Install ELK Stack
  config.vm.provision "shell", inline: <<-SHELL
    set -e # Exit on error

    echo "Updating system and installing dependencies..."
    export DEBIAN_FRONTEND=noninteractive
    apt-get update -y
    apt-get install -y wget gpg apt-transport-https openjdk-17-jre-headless

    echo "Adding Elastic GPG key and repository..."
    wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | tee /etc/apt/sources.list.d/elastic-8.x.list
    
    echo "Installing Elasticsearch, Logstash, and Kibana..."
    apt-get update -y
    apt-get install -y elasticsearch logstash kibana

    echo "Configuring Elasticsearch..."
    # Set Heap size (1GB for this lab)
    echo "-Xms1g" > /etc/elasticsearch/jvm.options.d/heap.options
    echo "-Xmx1g" >> /etc/elasticsearch/jvm.options.d/heap.options
    
    # Disable security for easier learning in this specific lab environment
    sed -i 's/xpack.security.enabled: true/xpack.security.enabled: false/' /etc/elasticsearch/elasticsearch.yml
    echo "network.host: 0.0.0.0" >> /etc/elasticsearch/elasticsearch.yml
    echo "discovery.type: single-node" >> /etc/elasticsearch/elasticsearch.yml

    echo "Configuring Kibana..."
    echo "server.host: \"0.0.0.0\"" >> /etc/kibana/kibana.yml
    echo "elasticsearch.hosts: [\"http://localhost:9200\"]" >> /etc/kibana/kibana.yml

    echo "Starting services..."
    systemctl daemon-reload
    systemctl enable elasticsearch kibana logstash
    systemctl start elasticsearch
    systemctl start kibana

    echo "ELK Stack installation complete!"
    echo "Elasticsearch is running on http://192.168.56.10:9200"
    echo "Kibana is running on http://192.168.56.10:5601"
  SHELL
end
