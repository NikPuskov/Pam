ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

MACHINES = {
  :"pam" => {
              :box_name => "ubuntu/jammy64",
              :cpus => 2,
              :memory => 1024,
              :ip => "192.168.0.234",
            }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.network "public_network", ip: boxconfig[:ip]
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s

      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
      box.vm.provision "shell", inline: <<-SHELL
          sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
          systemctl restart sshd.service
  	  SHELL
    end
  end
end
