# -*- mode: ruby -*-
# vi: set ft=ruby :

## ElasticSearch cluster
server_count = 2
network = '10.0.0.'
first_ip = 100

servers = []
seeds = ""
(0..server_count-1).each do |i|
  name = 'awesome-search-' + (i + 1).to_s
  ip = network + (first_ip + i).to_s
  seeds << "\"" << ip << "\""
  if i < server_count - 1
    seeds << ", "
  end
  servers << {'name' => name,
              'ip' => ip,
              'num' => i.to_s
             }
end


Vagrant.configure("2") do |config2|
  servers.each do |server|
    config2.vm.define server['name'] do |config|

      config.vm.box = "Centos6_4"
      config.vm.box_url = "http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20130427.box"

      config.vm.hostname = server['name']
      config.vm.network :private_network, ip: server['ip']
      config.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", "2048"]
      end

      config.vm.provision :shell do |shell|
      shell.inline = "mkdir -p /etc/puppet/modules;
                  puppet module install --force puppetlabs-firewall"
      end

      config.vm.provision :puppet do |puppet|
        # puppet.options = "--verbose --debug"
        puppet.facter = { "esversion" => "1.1.1", "seeds" => seeds }
        puppet.module_path = "puppet/modules"
        puppet.manifests_path = "puppet/manifests"
        puppet.manifest_file  = "elasticsearchnode.pp"
      end
    end
  end
end
