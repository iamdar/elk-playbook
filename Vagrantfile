# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-22.04"

  # --- ELK STACK NODE ---
  config.vm.define "elk-node" do |elk|
    elk.vm.hostname = "elk-lab"
    elk.vm.network "private_network", ip: "192.168.56.10"
    
    elk.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.cpus = 2
      vb.name = "elk-stack-lab"
    end

    elk.vm.provision "shell", inline: <<-SHELL
      set -e
      export DEBIAN_FRONTEND=noninteractive
      apt-get update -y
      apt-get install -y wget gpg apt-transport-https openjdk-17-jre-headless

      echo "Adding Elastic GPG key and repository..."
      wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
      echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | tee /etc/apt/sources.list.d/elastic-8.x.list
      
      apt-get update -y
      apt-get install -y elasticsearch logstash kibana

      # Configure Elasticsearch
      echo "-Xms1g" > /etc/elasticsearch/jvm.options.d/heap.options
      echo "-Xmx1g" >> /etc/elasticsearch/jvm.options.d/heap.options
      sed -i 's/xpack.security.enabled: true/xpack.security.enabled: false/' /etc/elasticsearch/elasticsearch.yml
      echo "network.host: 0.0.0.0" >> /etc/elasticsearch/elasticsearch.yml
      echo "discovery.type: single-node" >> /etc/elasticsearch/elasticsearch.yml

      # Configure Kibana
      echo "server.host: \"0.0.0.0\"" >> /etc/kibana/kibana.yml
      echo "elasticsearch.hosts: [\"http://localhost:9200\"]" >> /etc/kibana/kibana.yml

      systemctl daemon-reload
      systemctl enable elasticsearch kibana logstash
      systemctl start elasticsearch
      systemctl start kibana
    SHELL
  end

  # --- PHP APPLICATION NODE ---
  config.vm.define "php-app" do |php|
    php.vm.hostname = "php-app"
    php.vm.network "private_network", ip: "192.168.56.20"
    
    php.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
      vb.name = "php-app-node"
    end

    php.vm.provision "shell", inline: <<-SHELL
      set -e
      export DEBIAN_FRONTEND=noninteractive
      apt-get update -y
      apt-get install -y nginx php-fpm php-curl

      # Create a simple PHP application
      mkdir -p /var/www/html
      cat <<EOF > /var/www/html/index.php
<?php
header('Content-Type: application/json');
echo json_encode([
    "status" => "success",
    "message" => "Hello from the PHP App Node!",
    "timestamp" => date('Y-m-d H:i:s'),
    "server_ip" => \$_SERVER['SERVER_ADDR']
]);
?>
EOF

      # Configure Nginx for PHP
      cat <<EOF > /etc/nginx/sites-available/default
server {
    listen 80;
    root /var/www/html;
    index index.php index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
    }
}
EOF
      systemctl restart nginx
      systemctl restart php8.1-fpm

      echo "PHP App Node ready at http://192.168.56.20"
    SHELL
  end
end
